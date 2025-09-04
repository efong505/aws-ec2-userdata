

***

# 🚀 AWS EC2 Automation Tutorial: Deploying a Web Server with User Data, S3, AMI, and Launch Templates

## 🧩 Overview

This tutorial walks through the process of automating the deployment of a web server on AWS EC2 using:

*   EC2 User Data scripts
*   S3 for hosting website assets
*   Creating a custom AMI
*   Launch Templates for scalable deployment

***

## 🛠️ Step 1: Launch EC2 Instance with User Data

Start by launching an EC2 instance (Amazon Linux 2) and use the following **User Data** script to install and start Apache (`httpd`):

```bash
#!/bin/bash
yum update -y
yum install -y httpd
systemctl start httpd
systemctl enable httpd
```

This ensures the web server is ready immediately after the instance boots.

***

## 📁 Step 2: Upload Website Files to S3

Upload your website files (HTML, CSS, JS, images) to an S3 bucket.\
Example bucket: `ec2-files-ekewaka`\
Region: `us-east-1`\
Example file:\
`https://ec2-files-ekewaka.s3.us-east-1.amazonaws.com/index.html`

Update your `index.html` to reference S3-hosted assets directly:
https://ec2-files-ekewaka.s3.us-east-1.amazonaws.com/index.html


***

## 🧬 Step 3: Create an AMI from the Instance

Once your EC2 instance is configured and tested:

1.  Go to **EC2 Dashboard > Instances**
2.  Select your instance
3.  Click **Actions > Image > Create Image**
4.  Name your AMI and create it

This AMI now contains your pre-installed web server setup.

***

## 📦 Step 4: Create a Launch Template Using the AMI

1.  Go to **EC2 Dashboard > Launch Templates**
2.  Click **Create launch template**
3.  Use the AMI you created
4.  Add the following **User Data** script to pull the latest `index.html` from S3:

```bash
#!/bin/bash
yum update -y
yum install -y httpd
systemctl start httpd
systemctl enable httpd

curl -o /var/www/html/index.html https://ec2-files-ekewaka.s3.us-east-1.amazonaws.com/index.html
```

This keeps your instance lightweight and avoids hitting the user data size limit by offloading large files to S3.

***

## 🗺️ Architecture Diagram

    [S3 Bucket]
         |
         v
    [index.html + assets]
         |
         v
    [EC2 Instance]
      - Apache installed via User Data
      - index.html curled from S3
         |
         v
    [AMI Created]
         |
         v
    [Launch Template]
      - Uses AMI
      - Includes User Data script
         |
         v
    [New EC2 Instances]
      - Automatically configured web server

***

