
# Detailed NGINX Installation and Configuration Guide

---

### 1. Installation Steps

Update your system’s package index to get the latest information on software packages and install NGINX:

```bash
sudo apt update
sudo apt install nginx
```
- `sudo apt update`: Updates the local package index.
- `sudo apt install nginx`: Installs the NGINX package.

---

### 2. Service Management

Control the NGINX service with `systemctl`:
```bash
sudo systemctl start nginx         # Starts the NGINX service
sudo systemctl status nginx        # Displays the status of NGINX to verify it’s running
sudo systemctl enable nginx        # Enables NGINX to start on boot
```

---

### 3. Check HTTP Headers and Response Body

Use `curl` to send an HTTP request to verify NGINX is responding:
```bash
curl -i ip_address
# Example:
curl -i 192.168.1.58
```
- `-i`: Displays both HTTP headers and response body in the output.
- Replace `ip_address` with the actual IP address of the server running NGINX.

---

### 4. Setting Up Server Application Root

Create the root directory where your website files will reside:
```bash
sudo mkdir -p /var/www/example.com
```
- `/var/www/example.com`: This directory will serve as the document root for your site `example.com`.

---

### 5. Create a Sample index.html Page

Create an HTML file in the document root to test your configuration:
```bash
vim /var/www/example.com/index.html
```

Add the following HTML content:
```html
<html>
    <head>
        <title>Welcome to our Server!</title>
    </head>
    <body>
        <h1>Success! The example.com server block is working!</h1>
    </body>
</html>
```

---

### 6. Create a Server Block with the Correct Directives

Create an NGINX configuration file in `sites-available` for your site:
```bash
sudo vim /etc/nginx/sites-available/example.com
```

---

### 7. Example NGINX Configuration

Add the following code to set up an NGINX server block for `example.com`:

```nginx
server {
    # Bind to the local IP address and HTTP port 80
    listen 192.168.1.58:80;

    # Define server names (domain or IP)
    server_name example.com www.example.com 192.168.1.58;

    # Specify the root directory for files
    root /var/www/example.com;

    # Define index files
    index index.html index.htm index.php;

    # Configure logs
    access_log /var/log/nginx/example_access.log;
    error_log /var/log/nginx/example_error.log;

    # Set location rules
    location / {
        try_files $uri $uri/ =404;
    }

    # Optional PHP configuration (adjust PHP version if needed)
    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/var/run/php/php7.4-fpm.sock;
    }

    # Set cache expiration for static files
    location ~* \.(jpg|jpeg|png|gif|ico|css|js)$ {
        expires 365d;
    }
}
```

### Explanation of Key Directives
- `listen`: Specifies the IP and port NGINX will listen on.
- `server_name`: Specifies the domain names or IPs this block should handle.
- `root`: Directory to serve the files from.
- `location /`: The main location block handling root requests.

---

### 8. Enable the Site

Activate the configuration by creating a symbolic link from `sites-available` to `sites-enabled`:
```bash
sudo ln -s /etc/nginx/sites-available/example.com /etc/nginx/sites-enabled/
```

---

### 9. Test the NGINX Configuration

Verify there are no syntax errors in the configuration:
```bash
sudo nginx -t
```
- `nginx -t`: Tests the configuration file for errors without applying any changes.

---

### 10. Reload NGINX to Apply the Changes

Apply the changes by reloading NGINX:
```bash
sudo systemctl reload nginx
```
- `systemctl reload nginx`: Reloads NGINX to apply the new configuration without stopping the service.

---

This guide provides a comprehensive overview of each step for setting up, configuring, and managing NGINX on a Linux server.
