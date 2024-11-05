
# Deployment Guide for Dynamic Web Application on Ubuntu

## Step 1: Install Required Tools on the Server
Before cloning your project, ensure all the necessary tools are installed.

1. **Install Git**
   - Update package lists and install Git:
     ```bash
     sudo apt update
     sudo apt install git -y
     ```

2. **Install Java (for Spring Boot)**
   - Install OpenJDK 17:
     ```bash
     sudo apt install openjdk-17-jdk -y
     ```

3. **Install Maven (for Spring Boot build)**
   - Install Maven to manage dependencies and build the backend:
     ```bash
     sudo apt install maven -y
     ```

4. **Install Node.js and NPM (for Angular)**
   - Install Node.js and npm for running the Angular app:
     ```bash
     sudo apt install nodejs npm -y
     ```

5. **Install Angular CLI**
   - Install Angular CLI globally:
     ```bash
     sudo npm install -g @angular/cli
     ```

6. **Install MySQL Server**
   - Install and secure MySQL server:
     ```bash
     sudo apt install mysql-server -y
     sudo mysql_secure_installation
     ```
   - During installation, you’ll be prompted to:
     - Set a root password
     - Remove anonymous users
     - Disallow root login remotely
     - Remove the test database
     - Reload privilege tables

## Step 2: Clone Your GitHub Project
Navigate to the directory where you want to clone the project and use Git to pull the repository.

```bash
cd /opt
sudo git clone https://github.com/abrahimcse/sims-dynamic-web.git
```

This will download your project into the `/opt/sims-dynamic-web` directory.

## Step 3: Set Up the MySQL Database

1. **Log in to MySQL**:
   ```bash
   sudo mysql -u root -p
   ```

2. **Create a Database and User**:
   - Run the following commands to set up the database:
     ```sql
     CREATE DATABASE student_db;
     CREATE USER 'stdn_user'@'%' IDENTIFIED BY 'Admin@123';
     GRANT ALL PRIVILEGES ON student_db.* TO 'stdn_user'@'%';
     FLUSH PRIVILEGES;
     ```

3. **Exit MySQL**:
   ```sql
   EXIT;
   ```

## Step 4: Build and Deploy Spring Boot Backend

1. **Navigate to the backend folder**:
   ```bash
   cd /opt/sims-dynamic-web/student/backend
   ```

2. **Update the Spring Boot Database Configuration**:
   - Edit the `application.properties` file with your database credentials:
     ```bash
     sudo vim src/main/resources/application.properties
     ```
   - Example configuration:
     ```properties
     spring.datasource.url=jdbc:mysql://localhost:3306/student_db
     spring.datasource.username=stdn_user
     spring.datasource.password=Admin@123
     ```

3. **Build the Spring Boot Application**:
   - Use Maven to clean and build the application:
     ```bash
     sudo mvn clean install
     ```
   - This will create a `student-0.0.1-SNAPSHOT.jar` file in the `target` directory.

4. **Copy the JAR to a deployment directory**:
   ```bash
   sudo cp /opt/sims-dynamic-web/student/backend/target/student-0.0.1-SNAPSHOT.jar /opt/sims-dynamic-web/student/backend/
   ```

5. **Create a systemd service for the backend**:
   - Create a service file to run the backend as a service:
     ```bash
     sudo vim /etc/systemd/system/student-backend.service
     ```
   - Add the following content:
     ```ini
     [Unit]
     Description=Spring Boot Backend

     [Service]
     User=www-data
     ExecStart=/usr/bin/java -jar /opt/sims-dynamic-web/student/backend/student-0.0.1-SNAPSHOT.jar
     SuccessExitStatus=143
     Restart=always
     RestartSec=5

     [Install]
     WantedBy=multi-user.target
     ```

6. **Start and enable the backend service**:
   ```bash
   sudo systemctl daemon-reload
   sudo systemctl start student-backend.service
   sudo systemctl enable student-backend.service
   ```

7. **Check the backend service status**:
   ```bash
   sudo systemctl status student-backend.service
   ```

## Step 5: Build and Deploy Angular Frontend

1. **Configure the API URL in Angular Service**:
   - Open the Angular service file to configure the backend API URL:
     ```bash
     cd /opt/sims-dynamic-web/student/frontend/src/app/service
     sudo vim student.service.ts
     ```

2. **Set Up Node.js (Version 18.x)**:
   - Install a specific version of Node.js:
     ```bash
     curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
     sudo npm install -g n
     sudo n stable
     sudo n 18.19.0
     ```

3. **Install Angular dependencies**:
   ```bash
   cd /opt/sims-dynamic-web/student/frontend/
   sudo npm install
   ```

4. **Build the Angular Application**:
   ```bash
   sudo ng build --configuration production
   ```

5. **Copy build files to the web directory**:
   - Create and copy the build files to a deployment directory:
     ```bash
     sudo mkdir -p /var/www/student-info/
     sudo cp -r dist/* /var/www/student-info/
     ```

## Step 6: Configure NGINX as a Reverse Proxy

1. **Install NGINX**:
   ```bash
   sudo apt update
   sudo apt install nginx -y
   ```

2. **Create NGINX Configuration for the Application**:
   - Create a new NGINX configuration file for your app:
     ```bash
     sudo vim /etc/nginx/sites-available/student-info
     ```
   - Add the following configuration:
     ```nginx
     server {
         listen 80;
         server_name 192.168.1.93;  # Replace with your IP or domain

         root /var/www/student-info/student-frontend;
         index index.html;

         location / {
             try_files $uri $uri/ /index.html;
         }

         location /api/ {
             proxy_pass http://localhost:8080/;
             proxy_set_header Host $host;
             proxy_set_header X-Real-IP $remote_addr;
             proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
             proxy_set_header X-Forwarded-Proto $scheme;
         }

         error_page 404 /404.html;
         location = /404.html {
             internal;
         }
     }
     ```

3. **Enable the NGINX Configuration**:
   - Create a symbolic link to activate the site and reload NGINX:
     ```bash
     sudo ln -s /etc/nginx/sites-available/student-info /etc/nginx/sites-enabled/
     sudo nginx -t
     sudo systemctl reload nginx
     ```

4. **Verify NGINX Configuration**:
   - Check NGINX status and backend application logs if needed:
     ```bash
     sudo systemctl status nginx
     sudo journalctl -u student-backend.service -f
     sudo tail -f /var/log/nginx/error.log
     ```

---

By following these steps, you’ll deploy the backend as a Spring Boot application and the frontend as an Angular application on your Ubuntu server, using NGINX as a reverse proxy. This setup allows the frontend to communicate with the backend on separate endpoints, improving security and scalability.
