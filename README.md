# Web-assignment-
this will the web application

Deploying a Node.js Application on AWS EC2 with Nginx and PM2
 Prerequisites
AWS account
SSH key (.pem file)
Your Node.js project on GitHub (example: https://github.com/Sanika-coder/Web-assignment-.git)

1️⃣ Go to AWS Console → EC2 → Launch Instances
2️⃣ Configure:
Name: web-app
AMI: Amazon Linux 2 (Free Tier)
Instance Type: t2.micro
Key Pair: Create/select → download .pem
Security Group:
Allow SSH (port 22) → My IP
Allow HTTP (port 80) → Anywhere (0.0.0.0/0)
(Optional) Allow HTTPS (port 443) → Anywhere
3️⃣ Click Launch Instance → Wait until Running


Step 2 — Connect to EC2 Instance
On your local system, open terminal (where .pem is saved):
chmod 400 your-key.pem
ssh -i my-key.pem ec2-user@13.203.207.181

Step 3 — Install Required Packages
sudo yum update -y
sudo yum install -y git nginx
sudo yum install -y nodejs npm
Check versions:
git --version
node -v
npm -v
nginx -v


Step 4 — Clone Your Node.js Project
git clone https://github.com/Sanika-coder/Web-assignment-.git
cd Web-assignment-
cd AWS-EC2-instance
npm install


Step 5 — Create Your Server File
If not already present, create/edit server.js:
nano server.js
const express = require("express");
const path = require("path");
const app = express();
const PORT = process.env.PORT || 3000;
app.use(express.static(path.join(__dirname, "public")));
app.get("/", (req, res) => {
  res.send("<h1>Welcome to AWS EC2 Node.js Deployment </h1><p>Your app is running successfully!</p>");
});
app.listen(PORT, () => {
  console.log(` Server is running on http://localhost:${PORT}`);
});
npm install express
node server.js
curl http://localhost:3000


sudo nano /etc/nginx/nginx.conf
user nginx;
worker_processes auto;
error_log /var/log/nginx/error.log;
pid /run/nginx.pid;

events {
    worker_connections 1024;
}
http {
    include       /etc/nginx/mime.types;
    default_type  application/octet-stream;

    server {
        listen 80;
        server_name _;

        location / {
            proxy_pass http://localhost:3000;
            proxy_http_version 1.1;
            proxy_set_header Upgrade $http_upgrade;
            proxy_set_header Connection 'upgrade';
            proxy_set_header Host $host;
            proxy_cache_bypass $http_upgrade;
        }
    }

    sendfile on;
    keepalive_timeout 65;
}
sudo nginx -t
sudo systemctl restart nginx
sudo systemctl enable nginx


 Step 7 — Run Node App with PM2
Install PM2 globally:
sudo npm install -g pm2
pm2 start server.js --name aws-app
pm2 save
pm2 startup
pm2 list


Step 8 — Test Everything
Inside EC2:
curl http://localhost:3000
Then from your browser:
http://13.203.207.181


