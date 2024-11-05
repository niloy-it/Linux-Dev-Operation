
# SSH Key-Based Authentication

SSH Key-Based Authentication is a secure method for logging into a server using a pair of cryptographic keys: a private key and a public key. This method enhances security by eliminating the need for passwords, as the private key is never transmitted over the network. The server verifies the public key, and access is granted if it matches.

## Step-by-Step Guide

### 1. **Generate SSH Key Pair**
Generate a new SSH key pair on your local machine using the following command:
```bash
ssh-keygen -t rsa -b 4096
```
- **`-t rsa`**: Specifies the type of key to create (RSA).
- **`-b 4096`**: Specifies the number of bits in the key (4096 is recommended for better security).
- When prompted, you can accept the default file location (usually `~/.ssh/id_rsa`) and enter a passphrase for added security (optional).

### 2. **Copy Public Key to Remote Server**
Copy your public key to the remote server using the following command:
```bash
ssh-copy-id username@server_ip
```
- Replace **`username`** with your actual username on the remote server.
- Replace **`server_ip`** with the IP address or hostname of the remote server.
- This command will prompt you for the password of the remote user account. After entering it, the public key will be appended to the `~/.ssh/authorized_keys` file on the remote server.

### 3. **Connect Using SSH Key**
Now you can connect to the remote server using your SSH key:
```bash
ssh username@server_ip
```
- If you set a passphrase for your key, you’ll be prompted to enter it; otherwise, you’ll be logged in without a password.
