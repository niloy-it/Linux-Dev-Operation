
# Configuring Multiple Static Websites with NGINX on Ubuntu

## Step 1: Install NGINX
Start by updating the package list and installing NGINX.

```bash
sudo apt update               # Updates the package list
sudo apt install nginx         # Installs NGINX web server
```

## Step 2: Manage NGINX Service
Start, check the status, and enable NGINX to start on boot.

```bash
sudo systemctl start nginx     # Starts the NGINX service
sudo systemctl status nginx    # Checks the current status of NGINX
sudo systemctl enable nginx    # Enables NGINX to start automatically at boot
```

## Step 3: Verify NGINX Installation
To check if NGINX is serving pages correctly, use `curl` to send an HTTP request.

```bash
curl -i <ip_address>           # Replace <ip_address> with your server’s IP, e.g., 192.168.1.58
```

## Step 4: Set Up Directories for Websites
Create separate directories for each website (e.g., "site1" and "site2").

```bash
sudo mkdir -p /var/www/site1   # Creates a directory for site1
sudo mkdir -p /var/www/site2   # Creates a directory for site2
```

## Step 5: Create Sample HTML Files
Create an `index.html` file for each website. Use `vim` (or any text editor) to add content.

```bash
# For site1
sudo vim /var/www/site1/index.html
# Inside, add:
<html>
    <head><title>Welcome to Site 1!</title></head>
    <body><h1>This is Site 1, served on port 8081!</h1></body>
</html>

# For site2
sudo vim /var/www/site2/index.html
# Inside, add:
<html>
    <head><title>Welcome to Site 2!</title></head>
    <body><h1>This is Site 2, served on port 8082!</h1></body>
</html>
```

## Step 6: Create Server Blocks for Each Site
Define separate configuration files for each site with custom ports.

```bash
# For site1, using port 8081
sudo vim /etc/nginx/sites-available/site1
# Inside, add:
server {
    listen 192.168.1.58:8081;
    server_name 192.168.1.58;
    root /var/www/site1;
    index index.html index.htm index.php;
    access_log /var/log/nginx/site1_access.log;
    error_log /var/log/nginx/site1_error.log;

    location / {
        try_files $uri $uri/ =404;
    }

    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/var/run/php/php7.4-fpm.sock;
    }

    location ~* \.(jpg|jpeg|png|gif|ico|css|js)$ {
        expires 365d;
    }
}

# For site2, using port 8082
sudo vim /etc/nginx/sites-available/site2
# Inside, add:
server {
    listen 192.168.1.58:8082;
    server_name 192.168.1.58;
    root /var/www/site2;
    index index.html index.htm index.php;
    access_log /var/log/nginx/site2_access.log;
    error_log /var/log/nginx/site2_error.log;

    location / {
        try_files $uri $uri/ =404;
    }

    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/var/run/php/php7.4-fpm.sock;
    }

    location ~* \.(jpg|jpeg|png|gif|ico|css|js)$ {
        expires 365d;
    }
}
```

## Step 7: Enable the Site Configurations
Enable each site by creating a symbolic link in the `sites-enabled` directory.

```bash
sudo ln -s /etc/nginx/sites-available/site1 /etc/nginx/sites-enabled/  # Enables site1
sudo ln -s /etc/nginx/sites-available/site2 /etc/nginx/sites-enabled/  # Enables site2
```

## Step 8: Test NGINX Configuration
Test the configuration for any syntax errors.

```bash
sudo nginx -t                  # Tests the configuration files for syntax errors
```

## Step 9: Reload NGINX
Reload NGINX to apply the changes.

```bash
sudo systemctl reload nginx    # Reloads NGINX with the new configurations
```

---

Following these steps will allow you to serve multiple static websites on different ports using NGINX on Ubuntu.
