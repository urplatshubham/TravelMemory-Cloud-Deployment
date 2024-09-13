# TravelMemory-Cloud-Deployment

This repository provides the step by step approach to deploy a **MERN** (MongoDB, Express, React, Node.js) stack application, **TravelMemory**, on an AWS EC2 instance. The application deployment is done leveraging Nginx, as reverse proxy server acting between the load balancer and the EC2 instances. Also a custom sub-domain is connected via **Cloudflare**.

## Table of Contents

- [Project Overview](#project-overview)
- [Features](#features)
- [Prerequisites](#prerequisites)
- [Deployment on AWS EC2](#deployment-on-aws-ec2)
  - [Step 1: Clone the Repository](#step-1-clone-the-repository)
  - [Step 2: Backend Configuration](#step-2-backend-configuration)
  - [Step 4: Setup for Reverse Proxy with Nginx](#step-4-setup-for-reverse-proxy-with-Nginx)
  - [Step 3: Frontend Configuration](#step-3-frontend-configuration)
  - [Step 5: Scaling and Load Balancing](#step-5-scaling-and-load-balancing)
  - [Step 6: Domain Setup with Cloudflare](#step-6-domain-setup-with-cloudflare)
- [Architecture Diagram](#architecture-diagram)
- [Troubleshooting](#troubleshooting)
- [License](#license)

---

## Project Overview

**TravelMemory** is a MERN stack application for creating and sharing travel memories. It consists of a **React** frontend, **Node.js/Express** backend, and uses **MongoDB** for data storage. The goal of this project is to provide a comprehensive guide to deploying a full-stack application on **AWS EC2**, incorporating **load balancing** and configuring a **custom domain** using **Cloudflare**.

## Features

- Full-stack MERN application.
- Backend powered by **Node.js** and **Express**.
- Frontend developed using **React**.
- Scalable deployment using **AWS EC2** instances.
- Load balancing using **AWS Elastic Load Balancer** (ELB).
- Domain management using **Cloudflare**.
- Reverse proxy configuration with **Nginx**.

## Prerequisites

Before starting the deployment, ensure you have:

- Basic knowledge of AWS services, including **EC2** and **Elastic Load Balancer**.
- A MongoDB instance, either hosted on a separate server or locally.
- An active **AWS account**.
- A **custom domain** (if needed) and a **Cloudflare** account.
- Basic knowledge of **Nginx**, **Linux** terminal, and **MERN stack** applications.
- Basic understanding of the load balancer,target group, AMI,template
- Access to the complete codebase of the TravelMemory application: [TravelMemory GitHub Repository](https://github.com/UnpredictablePrashant/TravelMemory/tree/main)

## Technologies Used:
- Node.js (Backend framework)
- React (Frontend framework)
- MongoDB (Database)
- AWS EC2 (Cloud Server)
- Nginx (Reverse Proxy Server)
- Cloudflare (DNS and Domain Management)
### Set-up
Create an EC2 instance using AWS console and configure it with ubuntu, arm architecture, t2g-micro.

Similarly create another instance using the image of the already create EC2 instance and follow all the steps
## Deployment on AWS EC2
### Backend Configuration
### Step 1: Clone the Repository

1. Clone the Repository
  Open the terminal and run
  ```bash
  $ sudo apt-get update
  ```

2. Clone the TravelMemory repository from GitHub link provided below:
   ```bash
   git clone https://github.com/UnpredictablePrashant/TravelMemory.git
   ```

3. Navigate to the backend folder and install the node.js dependencies:
   ```bash
   cd TravelMemory/backend
   npm install
   ```

### Step 2: Backend Configuration

1. Open the `.env` file in the `backend` directory and update the following environment variables:

   ```bash
   $sudo nano .env
   ```
  update the below code and paste the connection string copied in place of <your-mongodb-url>(and appended /db_name) at the end from Mongo DB Compass application once configured.
  Choose port 3001 to run the backend
   ```bash
   MONGO_URL=<your-mongodb-url>
   PORT=3001
   ```

2. Start the backend server:
   ```bash
   npm start
   ```
![Screenshot of the Backend Configured](https://github.com/urplatshubham/TravelMemory-Cloud-Deployment/blob/main/BE%20configured.png)
### Step 3: Set up Reverse Proxy with Nginx
  Install Nginx using (if it wasn't installed already).
  ```bash
  $sudo apt install nginx
  ```
  Edit the Nginx configuration file present in /etc/nginx/sites-available/default and update proxy settings for the backend running on http://<instance-ip>:3000.
  ``` bash
  $sudo nano /etc/nginx/sites-available/default
  ```
  ```
  location / {
               # First attempt to serve request as file, then
               # as directory, then fall back to displaying a 404.
               #try_files $uri $uri/ =404;
               proxy_pass http://<instance-ip>:3000.
               proxy_set_header Host $host;
               proxy_set_header X-Real-IP $remote_addr;
               proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
               proxy_set_header X-Forwarded-Proto $scheme;
       }
  ```
  After updating the configuration file, save the changes and restart Nginx to apply the reverse proxy setup.

  ```
  $sudo systemctl start nginx
  ```
### Step 4: Frontend Configuration

Open another terminal in the browser to access the already created EC2 instance.

1. Navigate to the frontend directory and install dependencies:
   ```bash
   cd TravelMemory/frontend
   npm install
   ```

2. Modify `frontend/src/urls.js` to ensure the frontend points to the correct backend API:
   ```javascript
   export const API_URL = 'http://<your-ec2-public-ip>:3000';
   ```

3. Build the frontend application:
   ```bash
   $npm start
   ```

4. Restart Nginx to apply the changes:
   ```bash
   sudo systemctl restart nginx
   ```
![Screenshot of the Frontend Configured](https://github.com/urplatshubham/TravelMemory-Cloud-Deployment/blob/main/FE%20configured.png)
### Step 5: Scaling and Load Balancing of the application

1. Similarly create another instance using the image of the already create EC2 instance and follow all the steps
2. Create an **Elastic Load Balancer (ELB)** to distribute incoming traffic across these instances:
   - Go to the AWS Console.
   - Navigate to **EC2 → Load Balancers** and create a load balancer.
   - Add the EC2 instances to the load balancer using target groups point the EC2 instances.

3. Ensure the frontend communicates with the load balancer, not directly with an EC2 instance.
![Screenshot of the ELB Configured](https://github.com/urplatshubham/TravelMemory-Cloud-Deployment/blob/main/LB%20Configured.png)
### Step 6: Domain Setup with Cloudflare

1. In your **Cloudflare** dashboard, add your domain. if not add a sub-domain under a already existing domain.
2. Create a **CNAME** record pointing to the ELB DNS name.
3. Create an **A** record pointing to the DNS name of the load balancer handling the  the application.
![Screenshot of the final deployment in cloud flare](https://github.com/urplatshubham/TravelMemory-Cloud-Deployment/blob/main/Deployment-CF-TM.png)
## Troubleshooting

### Nginx Fails to Start
- Check the configuration syntax with:
  ```bash
  sudo nginx -t
  ```
- Verify that no other process is using port 80:
  ```bash
  sudo lsof -i :80
  ```

### Architecture Diagram
Here’s an overview of the architecture used in this deployment as uploaded above as Tm Architecture Diagram.drawio:
![Architecture Diagram](https://github.com/urplatshubham/TravelMemory-Cloud-Deployment/blob/main/TM%20Architecture%20Diagram.drawio)

- Frontend and backend hosted on EC2 instances.
- Elastic Load Balancer (ELB) to distribute traffic across instances.
- Nginx acting as a reverse proxy to handle incoming requests.
- Cloudflare used to manage the domain and SSL termination.  


## Testing
  Stopped one EC2 instance and verified that the load balancer redirected traffic seamlessly to the other instance, ensuring no downtime during testing. Both backend and frontend worked         flawlessly through the load balancer.
  This deployment ensures smooth scaling, reverse proxy handling via Nginx, and efficient load balancing across EC2 instances.

## License

This project is licensed under the MIT License. See the [LICENSE](LICENSE) file for more details.


