
# Firewall Configuration Notes for Red Hat-based Linux Distributions

The following commands are for configuring the firewall on Red Hat-based distributions like:
- Red Hat Enterprise Linux (RHEL)
- CentOS
- Fedora
- AlmaLinux
- Rocky Linux
- Oracle Linux

---

## **Open HTTP/HTTPS Services (Temporary)**

1. **Open HTTP/HTTPS ports (80/443)**:
    ```bash
    firewall-cmd --add-service=http
    firewall-cmd --add-service=https
    ```

2. **Specify the zone (optional)**:
    ```bash
    firewall-cmd --zone=public --add-service=http
    firewall-cmd --zone=public --add-service=https
    ```

---

## **Save Running Configuration Permanently**

1. **Save the current configuration to apply after a reboot**:
    ```bash
    firewall-cmd --runtime-to-permanent
    ```

---

## **Show Open Services and Zone Information**

1. **List open services in the `home` zone**:
    ```bash
    firewall-cmd --zone=home --list-services
    ```

2. **Get the default zone**:
    ```bash
    firewall-cmd --get-default-zone
    ```

3. **List all services and settings in the default zone**:
    ```bash
    firewall-cmd --list-all
    ```

4. **Get a list of available zones**:
    ```bash
    firewall-cmd --get-zones
    ```

5. **List all services and settings in the `work` zone**:
    ```bash
    firewall-cmd --zone=work --list-all
    ```

6. **List all services and settings in the `home` zone**:
    ```bash
    firewall-cmd --zone=home --list-all
    ```

---

## **Manage Network Interfaces with Zones**

1. **Change interface (e.g., `eth0`) to the `home` zone**:
    ```bash
    firewall-cmd --zone=home --change-interface=eth0
    ```

2. **Change interface (e.g., `eth0`) to the `public` zone**:
    ```bash
    firewall-cmd --zone=public --change-interface=eth0
    ```

---

## **Open and Remove Services (Permanent)**

1. **Open HTTP/HTTPS services permanently in the `public` zone**:
    ```bash
    firewall-cmd --zone=public --add-service=http --permanent
    firewall-cmd --zone=public --add-service=https --permanent
    ```

2. **Remove HTTP/HTTPS services permanently from the `public` zone**:
    ```bash
    firewall-cmd --zone=public --remove-service=http --permanent
    firewall-cmd --zone=public --remove-service=https --permanent
    ```

---

## **Restart Firewall Service**

1. **Restart `firewalld` to apply permanent changes**:
    ```bash
    systemctl restart firewalld
    ```

---

## **Open and Remove Specific Ports**

1. **Open a specific port (e.g., `8069/tcp`)**:
    ```bash
    firewall-cmd --add-port=8069/tcp
    ```

2. **Remove a specific port (e.g., `8069/tcp`)**:
    ```bash
    firewall-cmd --remove-port=8069/tcp
    ```

---

## **Important Note**
- Always save changes permanently using `firewall-cmd --runtime-to-permanent` if you want the configuration to persist after a system reboot.
