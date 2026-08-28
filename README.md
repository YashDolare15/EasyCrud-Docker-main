<<<<<<< HEAD
# EasyCrud-Docker-main
=======
#  EasyCRUD – Full Stack Application Deployment
EasyCRUD is a full-stack CRUD (Create, Read, Update, Delete) application deployed using **Amazon RDS**, **EC2**, and **Docker**.  
The backend is developed using **Spring Boot**, the frontend uses **React + Nginx/httpd**, and the database runs on **MariaDB (RDS)**.


---

## 🛠️ Tech Stack

- **Cloud:** AWS (EC2, RDS)
- **Backend:** Spring Boot (Java)
- **Frontend:** Vite + Node.js
- **Database:** MariaDB (Amazon RDS)
- **Containerization:** Docker
- **Web Server:** Apache (inside frontend container)

---

## ⚙️ Prerequisites

- AWS Account
- Ubuntu EC2 Instance
- Amazon RDS (MariaDB)
- Git installed
- Docker installed
- Required ports open in Security Groups:
  - `22` (SSH)
  - `80` (Frontend)
  - `8080` (Backend)
  - `3306` (RDS – only from EC2 SG)

---


# 🔹 PHASE 1: Database Setup (Amazon RDS – MariaDB)


## Step 1: Create RDS Database

1. Login to **AWS Console → RDS**
2. Click **Create database**
3. Choose the following options:
   - **Database Engine:** MariaDB
   - **Template:** Free Tier / Production (as required)
   - **DB Identifier:** `student-db`
   - **Master Username & Password:** (set your own)
4. Connectivity:
   - **VPC:** Same VPC as EC2
   - **Public Access:** Yes (for testing)
   - **Security Group:** Allow inbound `3306` from EC2 Security Group
5. Click **Create Database**
6. Copy the **RDS Endpoint**

---

## Step 2: Launch and Prepare EC2 Instance

1. Launch an **Ubuntu EC2 instance**
2. SSH into the instance
3. Update the system:

```bash
sudo apt update -y
```
## Step 3: Install MySQL Client

Install MySQL client on the EC2 instance to connect with the Amazon RDS database.

```bash
sudo apt install mysql-client -y
```
## Step 4: Connect to RDS from EC2

Use the RDS endpoint to connect to the MariaDB database from the EC2 instance.

```bash
mysql -h <RDS-ENDPOINT> -u admin -p
```
## Step 5: Database Operations

Execute the following SQL commands inside the MySQL shell:

```sql
SHOW DATABASES;
CREATE DATABASE student_db;
USE student_db;
```
## Step 6: Create Students Table

Create the `students` table to store student records.

```sql
CREATE TABLE students (
  id BIGINT NOT NULL AUTO_INCREMENT,
  name VARCHAR(255),
  email VARCHAR(255),
  course VARCHAR(255),
  student_class VARCHAR(255),
  percentage DOUBLE,
  branch VARCHAR(255),
  mobile_number VARCHAR(255),
  PRIMARY KEY (id)
);
```


# 🔹 PHASE 2: Backend Deployment


## Step 7: Install Docker

Install Docker on the EC2 instance and start the Docker service.

```bash
sudo apt install docker.io -y
sudo systemctl start docker
```

## Step 8: Clone Project Repository

Clone the EasyCRUD project repository and navigate to the backend directory.

```bash
git clone https://github.com/Rohit-1920/EasyCRUD-Updated.git
```
Move to backend directory
```bash
cd EasyCRUD/backend/
```

## Step 9: Configure Backend Application

Copy the `application.properties` file to the backend root directory and edit it.

```bash
cp src/main/resources/application.properties .
```
```bash
nano application.properties
```
### Update Configuration Values

Update the following values in the `application.properties` file:

- **RDS Endpoint**
- **Database Name:** `student_db`
- **Database Username**
- **Database Password**

```bash
server.port=8080
spring.datasource.url=jdbc:mariadb://<RDS-EndPoint>:3306/student_db
spring.datasource.username=<username>
spring.datasource.password=<password>
spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=true
```

## Step 10: Create Dockerfile for Backend

Create a Dockerfile for the backend application.

```bash
nano Dockerfile
```
Add the following content to the Dockerfile:

```bash
FROM maven:3.8.5-openjdk-17
COPY . /opt/
WORKDIR /opt
RUN rm -rf src/main/resources/application.properties
RUN cp -r application.properties src/main/resources/
RUN mvn clean package
WORKDIR target/
CMD ["java","-jar","student-registration-backend-0.0.1-SNAPSHOT.jar"]
```
## Step 11: Build Backend Docker Image

Build the Docker image for the backend application and verify the image creation.
```bash
docker build -t <images-name>:<tag> <dockerfile-path>
```
Example :
```bash
docker build -t backend:v1 .
```
Verify Docker images
```bash
docker images
```
## Step 12: Run Backend Container

Run the backend Docker container and verify that it is running.

```bash
docker run -d -p 8080:8080 backend:v1
```
Check running containers
```bash
docker ps
```
## Step 13: Verify Backend

Verify that the backend application is running successfully.

Open a browser and navigate to: 
```bash
http://<EC2_PUBLIC_IP>:8080
```
✅ **Backend deployed successfully**

# 🔹 PHASE 3: Frontend Deployment (Docker + Nginx)

## Step 14: Navigate to Frontend Directory

Navigate to the frontend directory and list all files, including hidden files such as `.env`.

```bash
cd ../frontend/
```
Show hidden files (.env)
```bash
ls -a
```
## Step 15: Configure Environment File

Edit the `.env` file to configure the backend API URL.

```bash
nano .env
```
Update the value as shown below:
```bash
VITE_API_URL=http://<BACKEND_PUBLIC_IP>:8080/api
```
## Step 16: Create Frontend Dockerfile

Create a Dockerfile for the frontend application.

```bash
nano Dockerfile
```
Add the following content to the Dockerfile:
```bash
FROM node:25-alpine
COPY . /opt/
WORKDIR /opt
RUN npm install
RUN npm run build
RUN apk update && apk add apache2
RUN cp -rf dist/* /var/www/localhost/htdocs/
EXPOSE 80
CMD ["httpd","-D","FOREGROUND"]
```
## Step 17: Build Frontend Docker Image

Build the Docker image for the frontend application .

```bash
docker build -t frontend:v1 .
```
verify the image creation
```bash
docker images
```
## Step 18: Run Frontend Container

Run the frontend Docker container.

```bash
docker run -d -p 80:80 frontend:v1
```
verify that it is running
```bash
docker ps
```
## Step 19: Verify Frontend

Verify that the frontend application is running successfully.

Open a browser and navigate to:
```bash
http://<EC2_PUBLIC_IP>
```

🎉 **EasyCRUD application is live and fully functional!**

>>>>>>> 1b1a1fb (EasyCrud-Docker-main)
