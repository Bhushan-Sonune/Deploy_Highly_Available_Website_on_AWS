# Deploy and Design Highly avialable and scalable Website 

This project demonstrates how to deploy a Flask web application on AWS using a highly available and scalable architecture. The application is built with Flask and served to users through AWS infrastructure components such as EC2, Application Load Balancer, and Auto Scaling.
Deployment_web_architecture_on_AWS.pdf

## Project Overview

The main goal of this project is to show how a web application can be hosted on AWS so that users can access it through a browser. It also explains how AWS services help keep the application available, scalable, and reliable.

When a user opens the website link, the request travels through AWS networking components and reaches the Flask application running on an EC2 instance. The application processes the request and returns the webpage to the user.

## Working of the Project

1. **User sends request**  
   The user opens the website from a web or mobile browser using HTTP/HTTPS.

2. **Request enters through Internet Gateway**  
   The request enters the Amazon VPC through the Internet Gateway, which allows internet traffic to enter and leave the VPC.

3. **Request reaches Application Load Balancer (ALB)**  
   The request is sent to the Application Load Balancer.  
   The ALB:
   - acts as the public entry point
   - listens on HTTP:80
   - distributes user traffic
   - sends traffic only to healthy EC2 instances

4. **Security Groups control access**  
   There are two main security groups:
   - **ALB Security Group**
     - allows HTTP/HTTPS traffic from `0.0.0.0/0`
     - allows users from anywhere to access the website
   - **EC2 Security Group**
     - allows HTTP traffic only from the ALB
     - allows SSH only from the Admin IP
     - protects EC2 instances from direct public access

5. **ALB forwards request to Target Group**  
   The ALB forwards the request to the Target Group.  
   The Target Group:
   - contains EC2 instances
   - performs health checks
   - ensures traffic goes only to healthy servers

6. **Request goes to EC2 instances in public subnets**  
   The EC2 instances are launched in:
   - Public Subnet A in Availability Zone A
   - Public Subnet B in Availability Zone B

   This improves high availability because if one Availability Zone fails, the other can still serve traffic.

7. **Flask app runs inside EC2**  
   Each EC2 instance contains:
   - Nginx running on port 80
   - Gunicorn
   - Flask app running on port 5000

   Working inside EC2:
   - Nginx receives the request
   - Nginx forwards the request to Gunicorn
   - Gunicorn runs the Flask application
   - Flask processes the request and returns the response

8. **Response goes back to the user**  
   After processing:
   - Flask sends the response to Gunicorn
   - Gunicorn sends it to Nginx
   - Nginx sends it back through ALB
   - ALB returns the final webpage to the user’s browser

## Auto Scaling Working

9. **Launch Template is used**  
   The Launch Template defines how new EC2 instances are created.  
   It contains:
   - AMI
   - instance type
   - key pair
   - security group
   - user data script

10. **Auto Scaling Group manages instances**  
    The Auto Scaling Group uses the Launch Template and maintains:
   - min = 2
   - desired = 2
   - max = 4

   This means:
   - at least 2 instances stay running
   - normally 2 instances serve traffic
   - it can increase up to 4 when load increases

11. **CloudWatch monitors the system**  
    Amazon CloudWatch collects metrics such as:
   - CPU utilization
   - status checks
   - latency
   - request count
   - healthy hosts

12. **CloudWatch Alarms trigger scaling**  
    Based on the monitored metrics, CloudWatch Alarms can trigger Auto Scaling to:
   - launch new EC2 instances when traffic increases
   - remove extra EC2 instances when traffic decreases

## Complete Flow in Short

`User → Internet Gateway → ALB → Target Group → EC2 (Nginx → Gunicorn → Flask) → Response back to User`
