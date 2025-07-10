1. Project Overview

This project demonstrates deploying a secure and scalable 2-tier web application with a custom VPC on AWS using CloudFormation infrastructure-as-code.

Key components:

Custom VPC with public and private subnets
Public EC2 instance serving as a web server (Apache Tomcat) and bastion host
Private EC2 instance for secure database connection testing
RDS MySQL database hosted in the private subnet
Java web application deployed on Tomcat connected to the RDS database
Objectives:

Implement network isolation using subnets
Secure database access through a bastion host
Deploy a full-stack Java application using Maven and Tomcat
Practice CloudFormation templating
2. Architecture Overview

The architecture includes:

VPC with separate public and private subnets
Public EC2 instance (App + Bastion host) accessible from the internet
Private EC2 instance (DB tester) accessible only from the bastion
RDS MySQL database in the private subnet
Internet Gateway attached to the public subnet
Optional NAT for outbound traffic from private subnet
Connection flow:
Laptop → SSH to Public EC2 → SSH to Private EC2 → Access RDS MySQL

3. Prerequisites

AWS account with configured credentials
SSH key pair to access EC2 instances
Tools: Git, Java (JRE), Maven, Apache Tomcat
Basic knowledge of Linux shell, MySQL, AWS EC2, VPC, and RDS
SSH client or terminal access
4. VPC Setup Using CloudFormation

Provision network resources with a CloudFormation YAML template that creates:

VPC with CIDR 10.0.0.0/16
Public subnet (10.0.0.0/20) and private subnet (10.0.16.0/20)
Internet Gateway and route tables configured for subnet association
Include the vpc-setup.yaml in your project folder.

After ymal file is uploded  below is my stack creaded by cloudformation

<img width="1512" height="982" alt="Image" src="https://github.com/user-attachments/assets/eaa746c5-f611-4234-9630-ebdb074cea13" />

Vpc creaded by cloudformation

<img width="1512" height="982" alt="Image" src="https://github.com/user-attachments/assets/e1218273-4833-4210-bf00-9c8d9e762ee1" />

Subnets creaded by cloudformation

<img width="1512" height="982" alt="Image" src="https://github.com/user-attachments/assets/a5969772-dc29-40e8-9d48-631cd84e4cbe" />

this are the route tables


<img width="1512" height="982" alt="Image" src="https://github.com/user-attachments/assets/f3bb87be-bb59-4167-bab6-9cbbf5ff027f" />


this is the NAT gateway setup to allow public port  to the private database


<img width="1512" height="982" alt="Image" src="https://github.com/user-attachments/assets/2c2b10b8-289e-4030-af8d-0a65bc42e7a6" />

5. EC2 Instance Configuration

Public EC2 (App Server and Bastion):

Ubuntu or Amazon Linux AMI
Located in public subnet
Security Group allows inbound SSH (port 22) from your IP and Tomcat port (8080) from anywhere
Hosts Java web application and acts as a jump host
Private EC2 (Database Tester):

Located in private subnet
Security Group allows SSH only from public EC2’s private IP
Used for secure testing of database connectivity
6. SSH Access via Bastion Host

Use SSH agent forwarding to connect to private EC2 through the public one:

ssh -A -i my-key.pem ec2-user@<public-ec2-ip>

ssh ec2-user@<private-ec2-ip> (from the public EC2)

Ensure SSH forwarding is enabled, security groups allow internal traffic, and private keys are secured.



7. RDS MySQL Setup and Testing

MySQL database hosted privately with a specific endpoint
Test connection from private EC2 using:
mysql -h <rds-endpoint> -u admin -p

<img width="1512" height="982" alt="Image" src="https://github.com/user-attachments/assets/64f722fc-849b-4af7-922d-9251b2126c03" />

8. Web Application Deployment

Build and package the Java app on public EC2:

sudo apt update
sudo apt install git openjdk-17-jre-headless maven -y
git clone https://github.com/SaiGorijala/aws-rds-java.git
cd aws-rds-java/
mvn clean package

This are the login  and user files with the database


<!-- Failed to upload "Screenshot 2025-07-10 at 3.46.54 PM.png" -->


<!-- Failed to upload "Screenshot 2025-07-10 at 3.47.17 PM.png" -->


<img width="1512" height="982" alt="Image" src="https://github.com/user-attachments/assets/0610e7cd-a168-4184-8cf2-a331e584cbf4" />

Install and start Apache Tomcat:

wget https://downloads.apache.org/tomcat/tomcat-9/v9.0.107/bin/apache-tomcat-9.0.107.zip
sudo apt install unzip -y
unzip apache-tomcat-9.0.107.zip
cp target/*.war apache-tomcat-9.0.107/webapps/
cd apache-tomcat-9.0.107/bin
chmod 755 *.sh
sh startup.sh

Confirm port 8080 is open in the security group to access the app.

9. Application Testing

Access the application via browser:

http://<3.91.134.131>:8080/LoginWebApp

Test login and user registration pages which interact with the RDS database via JDBC. Verify database credentials are correctly configured.

Login Page

<img width="1512" height="982" alt="Image" src="https://github.com/user-attachments/assets/16075ce4-5f7e-4f45-a7ae-4afc32c4653c" />


Regestration Page

<img width="1512" height="982" alt="Image" src="https://github.com/user-attachments/assets/f9ebc4b6-cca3-438d-8205-feb75557cded" />

Data regesterd in database

<img width="1512" height="982" alt="Image" src="https://github.com/user-attachments/assets/f5bf6134-65ca-4502-a065-647d9ccdb1b2" />


