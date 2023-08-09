# Highly Available Three-Tier Architecture on AWS

## Project Overview

**Architecture:**
![Highly Available Three-Tier Architecture on AWS](https://github.com/MHossam99/Highly-Available-Three-Tier-Architecture-on-AWS/assets/136257265/9f58d709-d6f4-495a-a879-6d2d4e8c5cf7)

In this project, I am designing and configuring a three-tier architecture on AWS.  
The three-tier architecture is the most popular implementation of a multi-tier architecture and consists of:  
- Tier 1 - User/Presentation Tier
- Tier 2 - Application/Logic Tier
- Tier 3 - Data Tier

The following illustration shows an example of a simple, generic three-tier application.
![Architectural pattern for a three-tier application](https://github.com/MHossam99/Highly-Available-Three-Tier-Architecture-on-AWS/assets/136257265/9a2cf5bb-8efe-41d4-9912-49fc8685aad6)

## Prerequisites

This project requires:

- Access to a notebook computer with Wi-Fi and Microsoft Windows, macOS, or Linux (Ubuntu, SuSE, or Red Hat)
- An internet browser such as Chrome, Firefox, or Edge.
- An AWS account

## Services Used

- [Amazon Virtual Private Cloud (VPC)](https://docs.aws.amazon.com/vpc/index.html): enables you to provision a logically isolated section of the AWS Cloud where you can launch AWS resources in a virtual network that you've defined.
- [Amazon Elastic Compute Cloud (EC2)](https://docs.aws.amazon.com/ec2/?icmpid=docs_homepage_compute): a web service that provides resizable computing capacity literally, servers in Amazon's data centers that you use to build and host your software systems.
- [Amazon Relational Database Service (RDS)](https://docs.aws.amazon.com/rds/?icmpid=docs_homepage_databases): a web service that makes it easier to set up, operate, and scale a relational database in the cloud. It provides cost-efficient, resizeable capacity for an industry-standard relational database and manages common database administration tasks.

## Research & Terms to be familiar with

- CIDR Blocks: Classless Inter-Domain Routing (CIDR) is an IP address allocation method that improves data routing efficiency on the internet. CIDR Blocks are a collection of IP addresses that share the same network prefix and number of bits. A large block consists of more IP addresses and a small suffix.
- Subnet: A subnet is a range of IP addresses in your VPC. You can create AWS resources, such as EC2 instances, in specific subnets. Each subnet must reside entirely within one Availability Zone and cannot span zones. By launching AWS resources in separate Availability Zones, you can protect your applications from the failure of a single Availability Zone.
- AWS Regions and Availability Zones: The AWS Global Infrastructure comprises AWS Regions and Availability Zones. AWS Regions are separate geographic areas. AWS Regions consist of multiple, physically separated, and isolated Availability Zones that are connected with low latency, high throughput, and highly redundant networking. Availability Zones consist of one or more discrete data centers, each with redundant power, networking, and connectivity, and housed in separate facilities.
![AWS Regions and Availability Zones](https://github.com/MHossam99/Highly-Available-Three-Tier-Architecture-on-AWS/assets/136257265/f16714da-8ab8-439c-a3d2-f086a5c9106b)  
These Availability Zones enable you to operate production applications and databases that are more highly available, fault-tolerant, and scalable than possible when using a single data center. You can deploy your applications and databases across multiple Availability Zones. In the unlikely event of a failure of one Availability Zone, user requests are routed to your application instances in the second Availability Zone. This approach ensures that your application continues to remain available at all times.
- Internet Gateway: An internet gateway is a horizontally scaled, redundant, and highly available VPC component that allows communication between your VPC and the internet. It supports IPv4 and IPv6 traffic. It does not cause availability risks or bandwidth constraints on your network traffic. An internet gateway enables resources in your public subnets (such as EC2 instances) to connect to the internet if the resource has a public IPv4 address or an IPv6 address. Similarly, resources on the internet can initiate a connection to resources in your subnet using the public IPv4 address or IPv6 address. For example, an internet gateway enables you to connect to an EC2 instance in AWS using your local computer. There's no additional charge for creating an internet gateway.
- NAT Gateways: A NAT gateway is a Network Address Translation (NAT) service. You can use a NAT gateway so that instances in a private subnet can connect to services outside your VPC but external services cannot initiate a connection with those instances.
- Route Tables: A route table contains a set of rules, called routes, that determine where network traffic from your subnet or gateway is directed.
- Security Groups: A security group acts as a firewall that controls the traffic allowed to and from the resources in your virtual private cloud (VPC). You can choose the ports and protocols to allow for inbound traffic and for outbound traffic. For each security group, you add separate sets of rules for inbound traffic and outbound traffic.
- Bastion Host: A bastion host is a server whose purpose is to provide access to a private network from an external network, such as the Internet. Because of its exposure to potential attack, a bastion host must minimize the chances of penetration. For example, you can use a bastion host to mitigate the risk of allowing SSH.
- LAMP Server: You can use this server to host a static website or deploy a dynamic PHP application that reads and writes information to a database, to setup a LAMP Server you should install an Apache web server with PHP and MariaDB support on your Amazon Linux 2023 instance also called a LAMP stack.

## Project Walkthrough

This walkthrough presents a detailed description of the process and methodology that I followed to complete the project.

### Task 1 — Creating a VPC, Subnets, Routing, and Security Groups

1. Created a VPC
  - Named it "Demo VPC"
  - Gave it a CIDR block of "192.168.0.0/16" and left everything else as default.  
![1 - VPC Creation](https://github.com/MHossam99/Highly-Available-Three-Tier-Architecture-on-AWS/assets/136257265/39c4d071-5104-4b50-88e8-543fa1d4298a)
2. Created a Subnet to be the Public Subnet
  - Associated it with the VPC I created in the previous step "Demo VPC"
  - Named it "Public Subnet 1"
  - Put it in the "us-west-2a" Availability Zone
  - Gave it a CIDR block of "192.168.1.0/24"
![2 - Public Subnet Creation](https://github.com/MHossam99/Highly-Available-Three-Tier-Architecture-on-AWS/assets/136257265/b51f44fd-e1e1-42c9-9530-f4a6e7179ca0)
3. Created a Subnet to be the first Private Subnet
  - Associated it with the VPC I created in Step 1 "Demo VPC"
  - Named it "Private Subnet 1"
  - Put it in the same Availability Zone as the Public Subnet I created in the previous step "us-west-2a"
  - Gave it a CIDR block of "192.168.2.0/24"
![3 - Private Subnet 1 Creation](https://github.com/MHossam99/Highly-Available-Three-Tier-Architecture-on-AWS/assets/136257265/07f1cc4e-59d2-444a-a099-0f32dc371380)
4. Created a Subnet to be the Second Private Subnet
  - Associated it with the VPC I created in Step 1 "Demo VPC"
  - Named it "Private Subnet 2"
  - Put it in the same Availability Zone as the Public and the Private Subnets I created in the previous steps "us-west-2a"
  - Gave it a CIDR block of "192.168.3.0/24"
![4 - Private Subnet 2 Creation](https://github.com/MHossam99/Highly-Available-Three-Tier-Architecture-on-AWS/assets/136257265/977e37e0-9af4-47ca-8249-973e90d9b4c2)
5. Created a Subnet to be the Third Private Subnet in a different Availability Zone
  - Associated it with the VPC I created in Step 1 "Demo VPC"
  - Named it "Private Subnet 3"
  - Put it in a different Availability Zone "us-west-2b" than the one I used with the other Subnets "us-west-2a"
  - Gave it a CIDR block of "192.168.4.0/24"
![5 - Private Subnet 3 Creation](https://github.com/MHossam99/Highly-Available-Three-Tier-Architecture-on-AWS/assets/136257265/9e432b0f-5f67-4377-92f8-a67be45deba8)
6. Allocated an Elastic IP
  - Left Everything as default after making sure it is located in the same Availability Zone as the first 3 Subnets I created "us-west-2a"
![6 - Allocating Elastic IP](https://github.com/MHossam99/Highly-Available-Three-Tier-Architecture-on-AWS/assets/136257265/077bd7b4-db24-4c5c-8a9f-687b129ea042)
7. Created an Internet Gateway
  - Named it "Demo Internet Gateway"
![7 - Creating Internet Gateway](https://github.com/MHossam99/Highly-Available-Three-Tier-Architecture-on-AWS/assets/136257265/0d507ad3-0e9e-4463-8ad7-24c0cc4dd851)
8. Attached the Internet Gateway to the VPC
  - Attached the Internet Gateway I created in the previous step "Demo Internet Gateway" to the VPC I created in Step 1 "Demo VPC" to enable the VPC to communicate with the Internet
![8 - Attaching Internet Gateway to VPC](https://github.com/MHossam99/Highly-Available-Three-Tier-Architecture-on-AWS/assets/136257265/c5d01119-06a5-4e98-a4fb-f99f25aa8d82)
9. Created a NAT Gateway
  - Named it "Demo NAT Gateway"
  - Assigned it to the Subnet I created in step 2 "Public Subnet 1"
  - Chose the connectivity type to be "Public"
  - Assigned the Elastic IP I created in Step 6 to the NAT Gateway
![9 - NAT Gateway Creation](https://github.com/MHossam99/Highly-Available-Three-Tier-Architecture-on-AWS/assets/136257265/bb22a732-9382-4dde-b6d2-f4441064bc92)
10. Created a Route Table to be the Public Route Table
  - Named it "Public Route Table"
  - Associated it with the VPC I created in Step 1 "Demo VPC"
![10 - Public Route Table Creation](https://github.com/MHossam99/Highly-Available-Three-Tier-Architecture-on-AWS/assets/136257265/5e312343-d30f-4975-a777-a2668b86013a)
11. Created a Route Table to be the Private Route Table
  - Named it "Private Route Table"
  - Associated it with the VPC I created in Step 1 "Demo VPC"
![11 - Private Route Table Creation](https://github.com/MHossam99/Highly-Available-Three-Tier-Architecture-on-AWS/assets/136257265/9f25c0b6-82e6-48f0-99cc-ee9043a35a01)
12. Associated the Public Subnet with the Public Route Table
  - Edited the Subnet Associations of the Route Table I created in Step 10 "Public Route Table" and Associated the Subnet I created in Step 2 "Public Subnet 1"
![12 - Associating Public Subnet with Public Route Table](https://github.com/MHossam99/Highly-Available-Three-Tier-Architecture-on-AWS/assets/136257265/32097a7b-c8bd-4bd7-b711-0079e5782241)
13. Added a route to the Public Route Table to access the Internet Gateway
  - Edited the routes of the Route Table I created in Step 10 "Public Route Table" and added a route with a destination of "anywhere" and a target of the Internet Gateway I created in Step 7 "Demo Internet Gateway"
![13 - Adding a route to Public Route Table to access the Internet Gateway](https://github.com/MHossam99/Highly-Available-Three-Tier-Architecture-on-AWS/assets/136257265/6f3b32b2-cabb-4efb-9f99-59b19e8a2679)
14. Associated the Private Subnets with the Private Route Table
  - Edited the Subnet Associations of the Route Table I created in Step 11 "Private Route Table" and Associated the Subnets I created in Steps 3,4, and 5 "Private Subnet 1", "Private Subnet 2", and "Private Subnet 3"
![14 - Associating Private Subnets with Private Route Table](https://github.com/MHossam99/Highly-Available-Three-Tier-Architecture-on-AWS/assets/136257265/01abe9aa-b79f-4eda-b09a-a2708aa5d976)
15. Added a route to the Private Route Table to access the NAT Gateway
  - Edited the routes of the Route Table I created in Step 11 "Private Route Table" and added a route with a destination of "anywhere" and a target of the NAT Gateway I created in Step 9 "Demo NAT Gateway"
![15 - Adding a route to Private Route Table to access the NAT Gateway](https://github.com/MHossam99/Highly-Available-Three-Tier-Architecture-on-AWS/assets/136257265/49374c03-ec43-46f8-896b-9b1076650271)
16. Created a Security Group for the Bastion Host
  - Named it "BastionHostSG" and gave it a description
  - Associated it with the VPC I created in Step 1 "Demo VPC"
  - Added Inbound rules for "SSH", "HTTP", and "HTTPS" with a source of "anywhere"
  - Left the Outbound rules as default
![16 - Create Security Group for Bastion Host](https://github.com/MHossam99/Highly-Available-Three-Tier-Architecture-on-AWS/assets/136257265/78bfa84d-1567-4655-bdb6-aef1a31cd0d8)
17. Created a Security Group for the Web Server
  - Named it "WebServerSG" and gave it a description
  - Associated it with the VPC I created in Step 1 "Demo VPC"
  - Added Inbound rules for "SSH", "HTTP", and "HTTPS" with a source of "anywhere"
  - Left the Outbound rules as default
![17 - Create Security Group for Web Server](https://github.com/MHossam99/Highly-Available-Three-Tier-Architecture-on-AWS/assets/136257265/92fea490-3970-4f92-a63d-0509b623cab4)
18. Created a Security Group for the App Server
  - Named it "AppServerSG" and gave it a description
  - Associated it with the VPC I created in Step 1 "Demo VPC"
  - Added an Inbound rule for "All ICMP - IPv4" with a source of the Security Group created in step 17 "WebServerSG" and an Inbound rule for "SSH" with a source of the Security Group created in step 16 "BastionHostSG" 
  - Left the Outbound rules as default
![18 - Create Security Group for App Server](https://github.com/MHossam99/Highly-Available-Three-Tier-Architecture-on-AWS/assets/136257265/efc1e6dd-eb93-4c8c-8d7f-4ac531ae06b1)
19. Created a Security Group for the Database Server
  - Named it "DatabaseServerSG" and gave it a description
  - Associated it with the VPC I created in Step 1 "Demo VPC"
  - Added an Inbound rule for "MYSQL/Aurora" with a source of the Security Group created in step 18 "AppServerSG" and an Inbound rule for "MYSQL/Aurora" with a source of the Security Group created in step 16 "BastionHostSG" 
  - Left the Outbound rules as default
![19 - Create Security Group for Database Server](https://github.com/MHossam99/Highly-Available-Three-Tier-Architecture-on-AWS/assets/136257265/b3e512e3-f8c7-4e2d-a581-f6c0f2eccc68)
20. Added an Inbound rule in the Bastion Host Security Group for "MYSQL/Aurora" with a source of the Database server Security Group
  - Edited the Inbound rules of the Security Group created in step 16 "BastionHostSG" and added an Inbound rule for "MYSQL/Aurora" with a source of the Security Group created in step 19 "DatabaseServerSG"
![20 - Editing Bastion Host Inbound Rules](https://github.com/MHossam99/Highly-Available-Three-Tier-Architecture-on-AWS/assets/136257265/4f2b174c-1422-464a-b65b-39624e421e2d)
21. Added an Inbound rule in the Web Server Security Group for "All ICMP - IPv4" with a source of the App Server Security Group
  - Edited the Inbound rules of the Security Group created in step 17 "WebServerSG" and added an Inbound rule for "All ICMP - IPv4" with a source of the Security Group created in step 18 "AppServerSG"
![21 - Editing Web Server Inbound Rules](https://github.com/MHossam99/Highly-Available-Three-Tier-Architecture-on-AWS/assets/136257265/8793f335-a887-46da-8a0f-96f7900b4e54)
22. Added an Inbound rule in the App Server Security Group for "MYSQL/Aurora" with a source of the Database Server Security Group and Inbound rules for "HTTP" and "HTTPS" with a source of "anywhere"
  - Edited the Inbound rules of the Security Group created in step 18 "AppServerSG" and added an Inbound rule for "MYSQL/Aurora" with a source of the Security Group created in step 19 "DatabaseServerSG"
  - Added Inbound rules for "HTTP" and "HTTPS" with a source of "anywhere"
![22 - Editing App Server Inbound Rules](https://github.com/MHossam99/Highly-Available-Three-Tier-Architecture-on-AWS/assets/136257265/37ff33fc-8be9-4f37-b965-f6a7fc285393)

### Task 2 — Creating the Bastion Host, Web Server, and the App server

23. Created the Bastion Host
  - Launched an EC2 Instance
  - Named it "Bastion Host"
  - Selected the "Amazon Linux 2023" AMI
  - Selected the "t2.micro" Instance Type
  - Used a Key pair I created earlier (use your own key pair)
  - Attached it to the VPC I created in Step 1 "Demo VPC" and the Subnet created in Step 2 "Public Subnet 1"
  - Enabled Auto-assign public IP
  - Selected an existing security group (the Security Group created in step 16 "BastionHostSG")
![23 - Creating Bastion Host](https://github.com/MHossam99/Highly-Available-Three-Tier-Architecture-on-AWS/assets/136257265/5d3ec616-d49c-4af9-add2-e1c905b3815e)
![24 - Creating Bastion Host](https://github.com/MHossam99/Highly-Available-Three-Tier-Architecture-on-AWS/assets/136257265/5df173fa-13a4-46de-91a1-e42e65cb8c32)
24. Created the Web Server
  - Launched an EC2 Instance
  - Named it "Web Server"
  - Selected the "Amazon Linux 2023" AMI
  - Selected the "t2.micro" Instance Type
  - Used a Key pair I created earlier (use your own key pair)
  - Attached it to the VPC I created in Step 1 "Demo VPC" and the Subnet created in Step 2 "Public Subnet 1"
  - Enabled Auto-assign public IP
  - Selected an existing security group (the Security Group created in step 17 "WebServerSG")
  - Gave it the following User Data to set up and start a LAMP Server
    ```
    #!/bin/bash
    sudo dnf update -y
    sudo dnf install -y httpd wget php-fpm php-mysqli php-json php php-devel
    sudo dnf install mariadb105-server
    sudo systemctl start httpd
    sudo systemctl enable httpd
    ```
![25 - Creating Web Server](https://github.com/MHossam99/Highly-Available-Three-Tier-Architecture-on-AWS/assets/136257265/395cedec-e4a6-4aae-87bd-f0c895c1dda6)
![26 - Creating Web Server](https://github.com/MHossam99/Highly-Available-Three-Tier-Architecture-on-AWS/assets/136257265/24d970c5-fa4c-4136-b8fe-22a36033d437)
25. Created the App Server
  - Launched an EC2 Instance
  - Named it "App Server"
  - Selected the "Amazon Linux 2023" AMI
  - Selected the "t2.micro" Instance Type
  - Used a Key pair I created earlier (use your own key pair)
  - Attached it to the VPC I created in Step 1 "Demo VPC" and the Subnet created in Step 3 "Private Subnet 1"
  - Disabled Auto-assign public IP
  - Selected an existing security group (the Security Group created in step 18 "AppServerSG")
  - Gave it the following User Data to Install the MariaDB software packages
    ```
    #!/bin/bash
    sudo dnf install mariadb105-server
    sudo service mariadb start
    ```
![27 - Creating App Server](https://github.com/MHossam99/Highly-Available-Three-Tier-Architecture-on-AWS/assets/136257265/07e88b4e-4f74-4b75-9574-f494f320011c)
![28 - Creating App Server](https://github.com/MHossam99/Highly-Available-Three-Tier-Architecture-on-AWS/assets/136257265/0e9a0abe-0d41-47bd-80d7-05f27d26a908)

### Task 3 — Creating and setting up the Database Server

26. Created a Database Subnet Group
  - Named it "DBSubnetGroup" and gave it a description
  - Attached it to the VPC I created in Step 1 "Demo VPC"
  - Added the Availability Zones "us-west-2a" and "us-west-2b" that include Subnets "Private Subnet 2" and "Private Subnet 3" created in steps 4 and 5
![29 - Create Database subnet group](https://github.com/MHossam99/Highly-Available-Three-Tier-Architecture-on-AWS/assets/136257265/2446f99a-4b8c-4436-8cbd-34621846b877)
27. Created a Database
  - Launched an RDS Database Instance
  - Selected "Stanard Create" Method
  - Selected "Maria DB" Engine Type
  - Chose the "Free tier" Template
  - Named the Database Instance "DBInstance"
  - Left the Master username as default "admin" and Gave it a Master password
  - Attached it to the VPC I created in Step 1 "Demo VPC" and the DB Subnet Group created in the previous step "DBSubnetGroup"
  -  Selected "No" for Public Access
  -  Chose an existing Security Group (the Security Group created in step 19 "DatabaseServerSG") and the Availability Zone "us-west-2a" that includes Subnet "Private Subnet 2" created in step 4
  -  Gave the Database an initial name "DemoDB"
  -  Disabled automated backups and encryption (the best practice is to leave them enabled but the database will spin up faster with those checked off as they are not needed)
  -  Left everything else as default
![30 - Create database](https://github.com/MHossam99/Highly-Available-Three-Tier-Architecture-on-AWS/assets/136257265/410a8e1f-9944-47c7-b170-8b91b1aeb4c0)
![31 - Create database](https://github.com/MHossam99/Highly-Available-Three-Tier-Architecture-on-AWS/assets/136257265/353322ba-ecc4-40e0-9c1f-8ce5c0de5117)
![32 - Create database](https://github.com/MHossam99/Highly-Available-Three-Tier-Architecture-on-AWS/assets/136257265/1fca0ed3-447e-40f9-8273-4e81fa8616cc)
![33 - Create database](https://github.com/MHossam99/Highly-Available-Three-Tier-Architecture-on-AWS/assets/136257265/795168ab-55a4-422c-a2ff-06d29fbd6647)

### Task 4 — Testing the Connections

28. Uploaded the key pair to the Bastion host
  - Uploaded the key pair to the "Bastion Host" instance created in step 23 by running the following command on Powershell (replace 'keyname' with the name of your key and 'bastion-host-public-ip' with the public IP address of your Bastion Host)
     ```
    Pscp -scp -P 22 -i ’.\Downloads\keyname.ppk’ -| user ec2-user ‘.\Downloads\keyname.pem’ ec2-user@bastion-host-public-ip:/home/ec2-user
    ```  
![34 - Upload Keys to Bastion Host](https://github.com/MHossam99/Highly-Available-Three-Tier-Architecture-on-AWS/assets/136257265/0139415c-244c-4811-b107-23fa7bc2c1bb)  
29. Made an SSH connection with the Bastion Host, confirmed the key was uploaded, and Changed the file permissions of the key
  - Ran the following command on Powershell to SSH to the "Bastion Host" instance created in step 23 (replace 'keyname' with the name of your key and 'bastion-host-public-ip' with the public IP address of your Bastion Host)
  ```
  ssh -i .\Downloads\keyname.pem ec2-user@bastion-host-public-ip
  ```
  - After connecting to the Instance ran the "ls" command to confirm the key was uploaded to the instance
  - Changed the file permissions of the key by running the "chmod 400 keyname.pem" command (replace 'keyname' with the name of your key)  
![35 - Check that key is uploaded and change file permission](https://github.com/MHossam99/Highly-Available-Three-Tier-Architecture-on-AWS/assets/136257265/d8340433-dfb2-47aa-89db-4990a81be5f8)  
30. Made an SSH connection from the Bastion Host with the App Server and tested the connection between the App Server and the Web Server
  - Ran the following command on Powershell to SSH to the "App Server" instance created in step 25 (replace 'keyname' with the name of your key and 'app-server-private-ip' with your app server’s private IP address)
  ```
  ssh -i keyname.pem ec2-user@app-server-private-ip
  ```
  - Used the "ping" command with the Private IP address of the "Web Server" Instance created in step 24 to check the connection between the "App Server" instance and the "Web Server" Instance  
![36 - SSH into the App Server and ping to the Web Server](https://github.com/MHossam99/Highly-Available-Three-Tier-Architecture-on-AWS/assets/136257265/e222bc81-bc5f-4cc9-a9a4-acff7b5b1c8f)  
31. Connected to the Database
- Ran the following command on Powershell to connect to the "DBInstance" Instance created in step 27 (replace 'database-server-endpoint' with your database server endpoint and 'master-username' with your master username and 'master-password' with your password and 'database-name' with your database name)
  ```
  mariadb -h database-server-endpoint -u master-username -pmaster-password database-name
  ```
- After connecting to the Database Instance ran "show databases;" command to see the database from the app server  
![37 - Connecting to the database](https://github.com/MHossam99/Highly-Available-Three-Tier-Architecture-on-AWS/assets/136257265/d9eea30b-4b55-42f2-8c25-f64182a7909c)  


## This Concludes the Project, Thanks for reading ✍️
