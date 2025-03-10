after accessing your VPS as `root`, it is highly recommended to create a new user and avoid using the `root` account for regular tasks. This enhances security and helps prevent accidental system-wide changes. Here's what you should do after gaining root access:

* * * * *

### **1\. Update the System**

Before anything else, ensure your VPS is up-to-date:

bash

Copy code

`apt update && apt upgrade -y`

* * * * *

### **2\. Create a New User**

Create a new user account for daily use:

bash

Copy code

`adduser username`

Replace `username` with your desired username. You'll be prompted to set a password and optionally fill in some details (you can press Enter to skip these).

* * * * *

### **3\. Grant Administrative Privileges**

Add the new user to the `sudo` group to give them administrative privileges:

bash

Copy code

`usermod -aG sudo username`

* * * * *

### **4\. Set Up SSH Access for the New User**

If you're using SSH to access the VPS:

#### a. Copy the SSH Key to the New User

Assuming you used an SSH key to log in as `root`, copy the key to the new user:

bash

Copy code

`rsync --archive --chown=username:username ~/.ssh /home/username`

#### b. Test the New User's SSH Access

Log out of `root` and log in as the new user via SSH:

bash

Copy code

`ssh username@your-server-ip`

* * * * *

### **5\. Disable Root Login (Optional but Recommended)**

For better security, disable root login via SSH:

#### a. Edit the SSH Configuration File

Open the SSH config file:

bash

Copy code

`nano /etc/ssh/sshd_config`

Find the line:

plaintext

Copy code

`PermitRootLogin yes`

Change it to:

plaintext

Copy code

`PermitRootLogin no`

#### b. Restart SSH Service

Apply the changes:

bash

Copy code

`systemctl restart sshd`

* * * * *

### **6\. Secure the VPS**

Take additional steps to secure your VPS:

#### a. Configure a Firewall

Install and configure a firewall (e.g., UFW):

bash

Copy code

`sudo apt install ufw
sudo ufw allow OpenSSH
sudo ufw enable`

#### b. Install Fail2Ban

Protect against brute-force attacks:

bash

Copy code

`sudo apt install fail2ban`

#### c. Enable Automatic Security Updates

Install `unattended-upgrades` to automate critical updates:

bash

Copy code

`sudo apt install unattended-upgrades
sudo dpkg-reconfigure --priority=low unattended-upgrades`

* * * * *

### **7\. Set Up Monitoring (Optional)**

Install tools to monitor system performance:

-   `htop` for resource monitoring:

    bash

    Copy code

    `sudo apt install htop`

-   `netstat` or `ss` for network monitoring:

    bash

    Copy code

    `sudo apt install net-tools`

* * * * *

### **8\. Install Necessary Software**

Depending on your use case, you might need software like:

-   A web server (e.g., Apache, Nginx)
-   A database (e.g., MySQL, PostgreSQL)
-   Programming tools (e.g., Node.js, Python, PHP)

* * * * *

### **9\. Backup Your VPS**

Set up regular backups to protect your data in case of issues. You can use tools like `rsync`, or consider a third-party backup solution.

* * * * *

By creating a new user and securing your VPS, you reduce risks and establish a solid foundation for deploying applications or hosting websites.