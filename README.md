# Irondesk
Gym management system
IronDesk: Gym Management App on AWS

A full-stack gym management app (members, trainers, payments) built with Node.js and Express, deployed on AWS inside a custom VPC behind an Application Load Balancer.
I built this project to learn how a real application is deployed and secured in the cloud, not only how to write it. The app itself is simple on purpose.

Services used:

1)VPC -Custom network (10.0.0.0/16) with public and private subnets and an internet gateway
2)EC2 -Runs the Node.js application on Amazon Linux 2023
3)Application Load Balancer -Receives visitor traffic on port 80 and forwards it to the app, across two Availability Zones
4)Target group -Registers the EC2 instance and runs health checks on /login.html
5)Security groups -The app (port 3000) accepts traffic only from the load balancer's security group. SSH is limited to the VPC, using EC2 Instance Connect Endpoint
6)CloudWatch + SNS -Alarm when average CPU stays above 70% for 5 minutes, with an email notification
7)S3 -Stores backups of the application's data.json
8)IAM role -The EC2 instance writes to S3 using a role with least-privilege permissions, so no access keys are stored on the server

How I deployed it on AWS:
1)Created a custom VPC with public and private subnets and an internet gateway
2)Launched an EC2 instance in the public subnet and connected with EC2 Instance Connect Endpoint
3)Installed Node.js and Git, cloned this repository, and ran npm install
4)Started the app with PM2 so it keeps running after the terminal closes
5)Created a second public subnet in another Availability Zone, then an Application Load Balancer and target group (health check on /login.html)
6)Locked the app's port 3000 so only the load balancer's security group can reach it
7)Created a CloudWatch CPU alarm that sends email through SNS
8)Created an S3 bucket and an IAM role with least-privilege access, attached it to the instance, and backed up data.json to S3

What I learned:
1)A security group rule names an exact source, so "only my IP" really means one internet address, and the same home network can reach it from several devices
2)Why a load balancer needs subnets in two Availability Zones
3)"Healthy" in a target group only proves the load balancer can reach the app from inside the VPC, not that every browser can open it
4)Using an IAM role instead of access keys keeps credentials off the server
5)Why plain HTTP is a problem: the login travels unencrypted
