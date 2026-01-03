# **Gitea Self-Hosted Git Service on AWS EC2**

This repository contains the Standard Operating Procedure (SOP) and configuration references required to set up a secure, self-hosted Gitea instance on an AWS EC2 t3.micro instance running **Ubuntu 22.04 LTS**.

Gitea is a lightweight, easy-to-manage alternative to GitHub or GitLab, ideal for personal projects, small teams, or learning purposes.

## **🚀 Key Features of this Setup**

* **Platform:** AWS EC2 (Free-Tier friendly: t3.micro).  
* **Operating System:** Ubuntu 22.04 LTS.  
* **Service:** Gitea (running as a systemd service).  
* **Web Server:** Nginx (used as a reverse proxy).  
* **Security:** UFW firewall and Certbot for free SSL/TLS (HTTPS).  
* **Authentication:** Configuration for password-less SSH access.  
* **Persistence:** Basic manual backup procedure included..

## **📖 Setup Guide (Standard Operating Procedure)**

For the complete, step-by-step instructions, please refer to the primary document: \[Gitea EC2 Installation SOP.pdf\](Gitea EC2 Installation SOP.pdf).

The SOP covers the entire process, from launching the instance in the AWS Console to configuring your local machine for secure git push operations.

### **High-Level Setup Steps**

1. **Launch EC2 Instance:** Configure a t3.micro instance with SSH (22), HTTP (80), and HTTPS (443) ports open in the Security Group.  
2. **Initial SSH & Setup:** Update the system and install packages (git, nginx, ufw, certbot).  
3. **Gitea Service Setup:** Create the dedicated git user, directories, download the Gitea binary, and configure it to run as a **systemd service** on port 3000\.  
4. **Reverse Proxy:** Set up Nginx to handle traffic on port 80 and forward it to Gitea's port 3000\.  
5. **Secure with HTTPS:** Use **Certbot** to automatically provision and renew a Let's Encrypt SSL certificate for your domain (if applicable).  
6. **Web Installation:** Complete the Gitea configuration via the web installer, using **SQLite3** as the database backend.  
7. **SSH Authentication:** Generate an SSH key locally, add the public key to Gitea's web UI, and configure your local Git remote to use the SSH URL.

## **🛠️ Essential Command Cheatsheet**

Below are the most critical commands for maintenance, troubleshooting, and daily use. **Remember to replace \<EC2\_IP\>, \<GITEA\_USER\>, and \<REPO\> placeholders.**

### **A. Gitea Service Management**

| Action | Command |
| :---- | :---- |
| **Check Status** | sudo systemctl status gitea |
| **Check Logs (Last 200 lines)** | sudo journalctl \-u gitea \-n 200 \--no-pager |
| **Stop Service** | sudo systemctl stop gitea |
| **Start Service** | sudo systemctl start gitea |

### **B. Local Git Configuration (Post-Setup)**

| Action | Command | Notes |
| :---- | :---- | :---- |
| **Fix/Change Remote (HTTP)** | git remote set-url origin http://\<EC2\_IP\>:3000/\<GITEA\_USER\>/\<REPO\>.git | Use this if you are not using a domain or SSH. |
| **Change Remote (SSH)** | git remote set-url origin git@\<EC2\_IP\>:\<GITEA\_USER\>/\<REPO\>.git | **Recommended** for security and convenience. |
| **Sync Remote README** | git pull origin main \--rebase | Run this if the repo was initialized on the Gitea web UI. |
| **Test SSH Access** | ssh \-T git@\<EC2\_IP\> | Should return a success message from Gitea. |

### **C. Backup & Disaster Recovery**

The SOP recommends a simple manual backup process.

1. **Stop Gitea & Create Archive**  
   sudo systemctl stop gitea  
   sudo tar czf /root/gitea-backup-$(date \+%F).tar.gz /var/lib/gitea /etc/gitea  
   sudo systemctl start gitea

2. **Download Backup to Local Machine**  
   scp \-i \~/keys/my-ec2.pem ubuntu@\<EC2\_IP\>:/root/gitea-backup-YYYY-MM-DD.tar.gz \~/Downloads/

### **D. Troubleshooting**

| Issue | Command |
| :---- | :---- |
| Nginx Configuration Errors | sudo nginx \-t |
| Check UFW Firewall Rules | sudo ufw status verbose |
| Verify SSH Key Installation | sudo cat /home/git/.ssh/authorized\_keys |


## Author

**Yadnesh Raut**

📧 Email: yadneshmraut@gmail.com

🔗 LinkedIn: [YadneshR](www.linkedin.com/in/yadneshishere)

💬 Feel free to contact me for setup assistance, troubleshooting, or improvements