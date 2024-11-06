In recent versions of Ubuntu (starting from Ubuntu 20.04 and later), the network configuration has shifted from using the traditional 50-cloud-init.yaml or 01-netcfg.yaml files to a new configuration file called 00-installer-config.yaml. 
# Configuring a Static IP on Ubuntu

To set a static IP on Ubuntu, you'll modify network configuration files using **Netplan**. Follow the steps below for a comprehensive guide.

## Step-by-Step Guide

### 1. **Identify Your Network Interface**
Open the terminal and list your network interfaces to find the one you want to configure:
```bash
ip a
```
Note the name of the network interface (e.g., `eth0`, `enp3s0`, `wlp2s0`).

### 2. **Locate the Netplan Configuration File**
List the configuration files in the Netplan directory:
```bash
ls /etc/netplan/
```
You should see a file such as `01-netcfg.yaml` or `50-cloud-init.yaml`. This is the file you need to modify.

### 3. **Backup the Original Configuration**
It's a good idea to create a backup before making any changes:
```bash
sudo cp /etc/netplan/00-installer-config.yaml /etc/netplan/00-installer-config.yaml.backup
```

### 4. **Edit the Netplan Configuration File**
Open the Netplan configuration file with an editor:
```bash
sudo vim /etc/netplan/00-installer-config.yaml
```

Replace or add the following content in the file:
```yaml
network:
  ethernets:
    eth0:
      dhcp4: false
      addresses:
        - 172.26.137.116/20
      routes:
        - to: 0.0.0.0/0
          via: 172.26.128.1
  version: 2
```

### 5. **Apply the Configuration**
Save your changes and apply the new configuration:
```bash
sudo netplan apply
```
If you want to test the configuration temporarily before fully applying it, use:
```bash
sudo netplan try
```

### 6. **Verify the Static IP**
Check if the changes have taken effect:
```bash
ip a
```
Test network connectivity to ensure everything is functioning:
```bash
ping -c 4 192.168.1.1  # Replace with your gateway or any reliable IP
```

### 7. **Revert to DHCP (if needed)**
If the static IP setup does not work or causes issues, revert to DHCP by modifying the configuration:
```yaml
enp3s0:  # Replace with your network interface name
  dhcp4: yes
```
Reapply the changes:
```bash
sudo netplan apply
```

## Notes:
- **`ethernets` block**: Defines the settings for specific Ethernet interfaces. Replace `enp3s0` with your network interface name.
- **`addresses`**: Specify the static IP address along with the subnet mask in CIDR notation (e.g., `/24` for `255.255.255.0`).
- **`gateway4`**: The IP address of your gateway, often your router’s IP.
- **`nameservers`**: A list of DNS servers to use; the example includes Google’s and Cloudflare's DNS.
