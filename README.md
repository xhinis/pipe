<h1 align="center"> Pipe Network

![0G Image](https://github.com/xhinis/pipe/blob/b68b8b72547bc430bb77db1bd12dcc9d05885dc7/Pipe.png)


</h1>

 * [Discord](https://discord.gg/cqfGXR85YY)<br>
 * [Twitter](https://x.com/pipenetwork)<br>
 * [Website](https://pipe.network/)<br>
 * [Blog](https://pipe.network/blog)<br>


### System Requirements

| Component | Minimum Requirements |
|---|---|
| CPU | 2 |
| RAM | 4+ GB |
| Storage | 50 GB SSD |

### Prerequisites



---

### Installation Steps

#### 1. **Prepare the Environment**
Move pipe-tool and dcdnd files to root directory.

Create the directory for the binaries:
```bash
sudo mkdir -p /opt/dcdn
```

Move the provided `pipe-tool` and `dcdnd` binaries to this directory:
```bash
sudo mv /root/pipe-tool /opt/dcdn/pipe-tool
sudo mv /root/dcdnd /opt/dcdn/dcdnd
```

#### 2. **Make Binaries Executable**
Grant executable permissions:
```bash
sudo chmod +x /opt/dcdn/pipe-tool
sudo chmod +x /opt/dcdn/dcdnd
```

---

### Node Registration Process

#### Step 1: Login to the Pipe Network
Generate an access token using the following command:
```bash
/opt/dcdn/pipe-tool login --node-registry-url="https://rpc.pipedev.network"
```

Follow the on-screen instructions:
- **Authenticate via browser**: You will need to sign in or create an account (Google credentials supported).
- Upon success, the credentials will be saved at `~/.permissionless/credentials.json`.

#### Step 2: Generate Registration Token
Run this command to create a registration token:
```bash
/opt/dcdn/pipe-tool generate-registration-token --node-registry-url="https://rpc.pipedev.network"
```

This generates `registration_token.json` in `~/.permissionless/`. The token is valid for one year.

---

### Systemd Service Setup

Create a `systemd` service file for the `dcdnd` node.

1. Open the service file for editing:
   ```bash
   sudo nano /etc/systemd/system/dcdnd.service
   ```

2. Add the following configuration:
   ```ini
   [Unit]
   Description=DCDN Node Service
   After=network.target
   Wants=network-online.target

   [Service]
   ExecStart=/opt/dcdn/dcdnd \
             --grpc-server-url=0.0.0.0:8002 \
             --http-server-url=0.0.0.0:8003 \
             --node-registry-url="https://rpc.pipedev.network" \
             --cache-max-capacity-mb=1024 \
             --credentials-dir=/root/.permissionless \
             --allow-origin=*
   Restart=always
   RestartSec=5
   LimitNOFILE=65536
   LimitNPROC=4096
   StandardOutput=journal
   StandardError=journal
   SyslogIdentifier=dcdn-node
   WorkingDirectory=/opt/dcdn

   [Install]
   WantedBy=multi-user.target
   ```

3. Save and close the file.

---

### Configure the Service

#### Step 1: Reload and Enable Service
Reload `systemd` to apply the changes:
```bash
sudo systemctl daemon-reload
```

Enable the service to start on boot:
```bash
sudo systemctl enable dcdnd
```

#### Step 2: Start the Service
Start the `dcdnd` node service:
```bash
sudo systemctl start dcdnd
```

---

### Network Configuration

#### Open Required Ports
Ensure the required ports are accessible:
```bash
sudo ufw allow 8002/tcp
sudo ufw allow 8003/tcp
sudo ufw reload
```

Verify that no other services are using these ports.

---

### Managing the Service

- **View Status**:
  ```bash
  sudo systemctl status dcdnd
  ```

- **View Logs**:
  ```bash
  sudo journalctl -f -u dcdnd.service
  ```

- **Restart the Service**:
  ```bash
  sudo systemctl restart dcdnd
  ```

- **Stop the Service**:
  ```bash
  sudo systemctl stop dcdnd
  ```

---

### Wallet Setup (Optional)
To generate or link a wallet:
- **Generate a wallet**:
  ```bash
  /opt/dcdn/pipe-tool generate-wallet --node-registry-url="https://rpc.pipedev.network"
  ```

- **Link an existing wallet**:
  ```bash
  /opt/dcdn/pipe-tool link-wallet --node-registry-url="https://rpc.pipedev.network" --public-key <BASE58_PUBLIC_KEY>
  ```

---

### Testing the Node
1. Verify the node is listed:
   ```bash
   /opt/dcdn/pipe-tool list-nodes --node-registry-url="https://rpc.pipedev.network"
   ```

2. Confirm ports are accessible:
   - Use a web-based port tester for **8002** and **8003**.
   - Alternatively, use telnet:
     ```bash
     telnet <YOUR_PUBLIC_IP> 8002
     telnet <YOUR_PUBLIC_IP> 8003
     ```

---

### Conclusion
By following these steps, your Pipe Network node should now be operational and ready for the devNet environment. Use logs and status checks to monitor and troubleshoot as needed.
