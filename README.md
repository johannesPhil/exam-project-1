# Dynamic Landing Page Deployment

## Project Overview

Prototype landing page showcasing **"The Future of Document Search for Legal Practitioners"**.

Goal: Demonstrate cloud provisioning and web deployment skills.

---

## Provisioning the Server

1. Created a new EC2 instance on AWS using Ubuntu 22.04.
2. Chose the free-tier t3.micro instance.

### Steps:

```bash
# Connect to instance
ssh -i Ubuntu-WSL.pem ubuntu@<ec2-public-ip>
```

---

## Web Server Setup

- **Web Server:** Nginx
- **Application Server:** Node.js (Express)
- **Process Manager:** PM2

### Install Dependencies:

```bash
sudo apt update
sudo apt install nginx nodejs npm -y
```

## Creating the Project Folder

1. Created a dedicated folder for the project:
   ```bash
   mkdir bio-landing && cd bio-landing
   ```

## Application Setup

1. Initialized a Node.js project:
   ```bash
   npm init -y
   ```
2. Installed Express:
   ```bash
   npm install express
   ```
3. Created `server.js` file with basic Express server setup.
4. Created a `public` folder containing the landing page (`index.html`) and styling files.

---

### Directory Structure:

```plaintext
~/bio-landing
├── server.js       # Node.js server
└── public/
    └── index.html  # Landing page using Tailwind CDN
```

### Example `server.js`:

```js
const express = require("express");
const app = express();
const PORT = 4000;

app.use(express.static("public"));

app.listen(PORT, () => {
  console.log(`Server running on port ${PORT}`);
});
```

## Running the Application with PM2

1. Installed PM2 globally:
   ```bash
   npm install -g pm2
   ```
2. Started the app with PM2:
   ```bash
   pm2 start server.js
   ```
3. Enabled PM2 to restart app on reboot:
   ```bash
   pm2 startup
   pm2 save
   ```
4. Checked the server log to make sure the server was running:
   ```bash
   pm2 logs server
   ```

---

## 🔁 Nginx Reverse Proxy Configuration

### Create Config File:

```bash
sudo nano /etc/nginx/sites-available/bio-landing
```

### Example Config:

```nginx
server {
    listen 80;
    server_name <your-ec2-public-ip-or-domain>;

    location / {
        proxy_pass http://localhost:4000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
}
```

### Enable Site & Restart NGINX:

```bash
sudo ln -s /etc/nginx/sites-available/bio-landing /etc/nginx/sites-enabled/
# To avoid displaying NGINX welcome page
sudo rm /etc/nginx/sites-available/default
sudo nginx -t
sudo systemctl reload nginx
```

---

## Networking & Security

**Security Group Configuration:**

- **Allowed Ports:** `22 (SSH)`, `80 (HTTP)`, `443 (HTTPS)`
- **Inbound Rules:**
  - **HTTPS (TCP/443):** `0.0.0.0/0`
  - **SSH (TCP/22):** `0.0.0.0/0`
  - **HTTP (TCP/80):** `0.0.0.0/0`

---

## Deployment URL

[http://ec2-51-21-168-120.eu-north-1.compute.amazonaws.com/](http://ec2-51-21-168-120.eu-north-1.compute.amazonaws.com/)

---

## Landing Page Preview

![Landing Page Screenshot](./images/landing-ss.png)

## SSL Status

SSL setup **attempted but skipped** due to issues with free domain registrars requiring suspicious payments. I decided to focus on ensuring **HTTP** accessibility for project completion.

---
