# Steps 1: Install SSH on Ubuntu
**Command**
```bash
sudo apt update
sudo apt install openssh-server
```
# Steps 2: Start and Enable SSH Service
**Command**
```bash
sudo systemctl status ssh
sudo systemctl enable --now ssh
```

# SSH Cheat Sheet  
*Essential Commands for SSH Connections, File Transfers, Port Forwarding, and Automation*

---

### 1. SSH into a Remote Server
**Command:**
```bash
ssh username@remote_host
```
- **username**: User account on the remote server.
- **remote_host**: IP address or domain name of the server.

---

### 2. Specify a Port
**Command:**
```bash
ssh username@remote_host -p port_number
```
- **port_number**: Port number to connect to (default SSH port is 22).

---

### 3. Connect with an Identity File (Private Key)
**Command:**
```bash
ssh -i /path/to/private_key username@remote_host
```
- **private_key**: Path to your private key file.

---

### 4. Execute a Command on the Remote Server
**Command:**
```bash
ssh username@remote_host "command_to_execute"
```
- **Example**:
  ```bash
  ssh user@server "ls /var/www"
  ```

---

### 5. Enable Debugging Output
**Command:**
```bash
ssh -v username@remote_host
```
- Use additional `-v` for increased verbosity: `-vv`, `-vvv`.

---

## Copying Files Using SCP (Secure Copy Protocol)

### 6. Copy a File from Local to Remote
**Command:**
```bash
scp /path/to/local_file username@remote_host:/path/to/remote_directory
```

### 7. Copy a File from Remote to Local
**Command:**
```bash
scp username@remote_host:/path/to/remote_file /path/to/local_directory
```

### 8. Copy a Directory Recursively
**Command:**
```bash
scp -r /path/to/local_directory username@remote_host:/path/to/remote_directory
```

---

## SSH Key Management

### 9. Generate a New SSH Key Pair
**Command:**
```bash
ssh-keygen -t rsa -b 4096
```
- **-t rsa**: Specifies the type of key (RSA).
- **-b 4096**: Sets the key length to 4096 bits.

### 10. Copy Public Key to Remote Server (Passwordless SSH)
**Command:**
```bash
ssh-copy-id username@remote_host
```

### 11. Manually Copy Public Key to Server
**Command:**
```bash
cat ~/.ssh/id_rsa.pub | ssh username@remote_host "mkdir -p ~/.ssh && cat >> ~/.ssh/authorized_keys"
```
- This command appends the public key to the `authorized_keys` file on the remote server.

---

## SSH Config File

### 12. Create/Edit SSH Config File
- Simplify SSH connections using `~/.ssh/config`.

**Command:**
```bash
nano ~/.ssh/config
```

**Example Entry:**
```bash
Host myserver
  HostName 192.168.1.100
  User username
  IdentityFile ~/.ssh/id_rsa
  Port 22
```

- **To connect**:
  ```bash
  ssh myserver
  ```

---

## Tunneling and Port Forwarding

### 13. Local Port Forwarding (Access Remote Service Locally)
**Command:**
```bash
ssh -L local_port:remote_host:remote_port username@remote_host
```
- **Example**: Access a remote MySQL database on `localhost:3306`:
  ```bash
  ssh -L 3306:localhost:3306 user@remote_host
  ```

### 14. Remote Port Forwarding (Expose Local Service to Remote)
**Command:**
```bash
ssh -R remote_port:localhost:local_port username@remote_host
```
- **Example**: Expose a local web server on port `8080` to a remote server:
  ```bash
  ssh -R 8080:localhost:8080 user@remote_host
  ```

### 15. Dynamic Port Forwarding (Create SOCKS Proxy)
**Command:**
```bash
ssh -D local_port username@remote_host
```
- **Example**: Create a SOCKS proxy on `localhost:1080`:
  ```bash
  ssh -D 1080 user@remote_host
  ```

---

**Note**: This cheat sheet covers essential SSH commands and configurations for everyday server management tasks, including establishing connections, copying files, setting up key-based authentication, and enabling tunneling.

---
