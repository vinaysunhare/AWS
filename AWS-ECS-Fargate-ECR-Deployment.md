# 🚀 Deploy Dockerized Application on AWS ECR & ECS Fargate

## 📌 Project Overview

This project demonstrates how to deploy a Dockerized application from a GitHub repository to **AWS ECR Public** and run it on **Amazon ECS using AWS Fargate**.

The application is first cloned from GitHub into an EC2 instance, Docker is installed and configured, the Docker image is built, and the image is pushed to Amazon ECR.

After pushing the image to ECR, an **Amazon ECS Fargate** cluster and task definition are created to deploy the application without managing the underlying servers.

The deployed application is exposed on **port 8000** and monitored using **Amazon CloudWatch**.

### GitHub Repository

https://github.com/vinaysunhare/cicd-demo-app-project

---

# 🏗️ Project Architecture

```text
GitHub Repository
       |
       | git clone
       v
AWS EC2 Instance
       |
       | Docker Build
       v
Docker Image
       |
       | Docker Tag & Push
       v
Amazon ECR Public
       |
       | Pull Image
       v
Amazon ECS
       |
       | AWS Fargate
       v
Running Container
       |
       | Port 8000
       v
Application
       |
       v
Amazon CloudWatch
       |
       +---- Logs
       +---- CPU Utilization
       +---- Monitoring
```

---

# ☁️ AWS Services Used

| AWS Service | Purpose |
|---|---|
| IAM | User, group, permissions and roles |
| EC2 | Linux machine used for Docker build and deployment preparation |
| ECR Public | Store Docker container image |
| ECS | Container orchestration |
| AWS Fargate | Serverless container deployment |
| CloudWatch | Application logs and monitoring |
| Security Group | Allow application traffic on port 8000 |

---

# 🛠️ Technologies Used

- AWS
- Amazon IAM
- Amazon EC2
- Amazon ECR
- Amazon ECS
- AWS Fargate
- Amazon CloudWatch
- Docker
- Linux / Ubuntu
- AWS CLI
- Git & GitHub
- Python/Django Application
- Containerization

---

# 1. 👤 Create IAM User

First, login to the AWS account.

Go to:

**AWS Console → IAM**

IAM is a global AWS service.

Instead of using the AWS root/admin account for every operation, I created a separate IAM user for this project.

The IAM user is used for accessing:

- EC2
- ECR
- ECS

---

## Create IAM User

Go to:

**IAM → Users → Create user**

Create the required IAM user.

<img width="512" height="236" alt="image" src="https://github.com/user-attachments/assets/92993823-e3e0-446e-99d8-30588c5bfd58" />

---

# 2. 👥 Create IAM Group

Instead of assigning permissions manually to every user, create an IAM group.

### Group Name

```text
unique-ecs-ecr-ec2
```

Users added to this group automatically receive the permissions attached to the group.

This is useful when multiple users need access to the same AWS services.

<img width="512" height="161" alt="image" src="https://github.com/user-attachments/assets/65598f58-8a9a-484c-85c4-fc0766d7fe17" />

---

# 3. 🔐 Add Permissions to IAM Group

The following permissions were added according to the project requirements.

## EC2 Permission

```text
AmazonEC2FullAccess
```
<img width="512" height="364" alt="image" src="https://github.com/user-attachments/assets/a2fe2582-82e3-42ca-9501-085982314ac5" />

---

## ECS Permission

```text
AmazonECSFullAccess
```
<img width="512" height="170" alt="image" src="https://github.com/user-attachments/assets/1998689d-126d-4e37-abf2-4d864d0da720" />

---

## ECR Permission

```text
AmazonEC2ContainerRegistryFullAccess
```

Additional ECR permissions were added during the ECR authentication troubleshooting.

<img width="512" height="143" alt="image" src="https://github.com/user-attachments/assets/ec2604a3-19a0-4491-94cb-f7e2b1ea6b40" />
<img width="512" height="169" alt="image" src="https://github.com/user-attachments/assets/ef7372c4-306c-4a81-889a-0f15a4462542" />
<img width="512" height="200" alt="image" src="https://github.com/user-attachments/assets/607dc7f9-3a6c-4116-993b-e55dc17f6607" />

---

# 4. 🔑 Create IAM Access Key

Go to:

**IAM → Users → Security Credentials → Access Keys**

Create a new access key.

Select:

```text
Command Line Interface (CLI)
```

The access key is required for configuring AWS CLI on the EC2 instance.

---

# 5. 🖥️ Create EC2 Instance

Go to:

**AWS Console → EC2 → Launch Instance**

Create a Linux EC2 instance.

---

## Select Linux Machine

Select the required Linux AMI.

<img width="512" height="251" alt="image" src="https://github.com/user-attachments/assets/2d713371-8469-4aa5-8abf-873e5ce5425a" />

---

## Create Key Pair

Create/select the required EC2 key pair.

<img width="467" height="401" alt="image" src="https://github.com/user-attachments/assets/20552a8e-2dda-4bd4-957c-1cc41aaf5626" />

---

## Launch Instance

Click:

**Launch Instance**

<img width="512" height="82" alt="image" src="https://github.com/user-attachments/assets/f737a5a9-4d7c-44dc-ba38-fba73e367519" />

---

# 6. 🔗 Connect to EC2 Instance

Connect to the running EC2 instance using SSH.

<img width="512" height="219" alt="image" src="https://github.com/user-attachments/assets/b6822aad-9825-4427-b30c-e3cd6f6bc096" />
<img width="512" height="274" alt="image" src="https://github.com/user-attachments/assets/2288c043-023a-4920-a100-b02143067803" />

---

# 7. 🔄 Update EC2 Machine

After connecting to the EC2 instance, update the package repository.

```bash
sudo apt update
```
<img width="512" height="482" alt="image" src="https://github.com/user-attachments/assets/09bd03ef-f28b-4f96-bcea-1f0c58256861" />

---

# 8. 📥 Clone GitHub Repository

Clone the application repository.

```bash
git clone https://github.com/vinaysunhare/cicd-demo-app-project.git
```
<img width="512" height="99" alt="image" src="https://github.com/user-attachments/assets/681db6be-7e04-491c-a082-b7b3c81af092" />

Check the files:

```bash
ls
```

Enter the project directory:

```bash
cd cicd-demo-app-project
```
<img width="389" height="65" alt="image" src="https://github.com/user-attachments/assets/bb906584-7541-41d9-b715-550b44282fbd" />

---

# 9. 📦 Create Amazon ECR Public Repository

Go to AWS Console.

Search for:

```text
ECR
```

Go to:

**Amazon Elastic Container Registry → Public repositories**

Create a new repository.

### Repository Name

```text
vinaysunhare/ecs-ecr-project
```

<img width="512" height="243" alt="image" src="https://github.com/user-attachments/assets/becf2df6-3de6-43e5-a703-66d8c0426dd1" />

---

## ECR Repository Created

The repository was created successfully.

Initially, the repository does not contain any Docker images.

<img width="512" height="85" alt="image" src="https://github.com/user-attachments/assets/025f6761-b170-4713-8985-f0bb774ad5cd" />
<img width="512" height="126" alt="image" src="https://github.com/user-attachments/assets/d96eccb3-8fbc-4e26-b9e9-c081e7485124" />

---

# 10. 📋 View ECR Push Commands

Open the ECR repository and click:

**View push commands**

AWS provides the commands required to authenticate, build, tag and push the Docker image.

<img width="512" height="420" alt="image" src="https://github.com/user-attachments/assets/36d453f1-9918-49b1-93d4-f5771ecdb5b4" />

---

# 11. 🐳 Install Docker on EC2

Install Docker:

```bash
sudo apt install docker.io
```
<img width="512" height="340" alt="image" src="https://github.com/user-attachments/assets/cbf500af-8cb3-4311-8dff-2d43a82ef0e3" />

Check Docker version:

```bash
docker --version
```
<img width="499" height="67" alt="image" src="https://github.com/user-attachments/assets/f6aee00e-5c20-4483-8104-65d81add9436" />

---

# 12. 🔐 Configure Docker Permission

Check Docker:

```bash
docker ps
```
<img width="512" height="46" alt="image" src="https://github.com/user-attachments/assets/f6e48869-b61b-4168-b2d2-863e27277392" />

If the current user does not have permission to access Docker, add the user to the Docker group.

Create Docker group:

```bash
sudo groupadd docker
```

Add current user to Docker group:

```bash
sudo usermod -aG docker $USER
```

You can also use:

```bash
sudo usermod -aG docker $USER
```

Logout from the EC2 instance:

```bash
exit
```

Reconnect to the EC2 instance.

Verify Docker:

```bash
docker ps
```
<img width="526" height="55" alt="image" src="https://github.com/user-attachments/assets/ca422456-d5d1-4965-975d-109f9fa1411f" />

---

# 13. ⚙️ Install AWS CLI

Install required packages:

```bash
sudo apt install unzip curl
```
<img width="997" height="708" alt="image" src="https://github.com/user-attachments/assets/2807145e-9944-49f1-a0c1-b2141f5a32c7" />

Download AWS CLI:

```bash
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
```
<img width="976" height="77" alt="image" src="https://github.com/user-attachments/assets/d5fd5c4d-99cd-42a0-a5af-fa21e1f193c1" />

Unzip:

```bash
unzip awscliv2.zip
```

Run the installer:

```bash
sudo ./aws/install
```
<img width="843" height="62" alt="image" src="https://github.com/user-attachments/assets/c6f46aba-8e28-4709-9bc4-20c28183b878" />

Verify AWS CLI:

```bash
aws --version
```

---

# 14. 🔧 Configure AWS CLI

Run:

```bash
aws configure
```

Enter the IAM credentials when prompted.

```text
AWS Access Key ID
AWS Secret Access Key
Default region name
Default output format
```

---

# 15. 🔑 Login to Amazon ECR Public

Run the ECR authentication command:

```bash
aws ecr-public get-login-password --region us-east-1 | docker login --username AWS --password-stdin public.ecr.aws/k1h7z6n8
```
Initially, the command returned an authorization error because the IAM user did not have the required ECR Public permission.

Example error:

```text
AccessDeniedException:
User is not authorized to perform:
ecr-public:GetAuthorizationToken
```
<img width="1616" height="106" alt="image" src="https://github.com/user-attachments/assets/1787924e-26b4-4d9b-93e1-9a6c7a30a6f9" />

---

# 16. 🔐 Add ECR Public Permissions

Additional ECR Public permissions were added to the IAM user/group.

The following policies were used as required:

```text
AmazonElasticContainerRegistryPublicFullAccess
AmazonElasticContainerRegistryPublicPowerUser
AmazonElasticContainerRegistryPublicReadOnly
```
<img width="1363" height="296" alt="image" src="https://github.com/user-attachments/assets/d62798f1-45bf-4245-80de-98df0108cf9e" />
<img width="1361" height="176" alt="image" src="https://github.com/user-attachments/assets/932f5a7f-bcd3-4a25-97b2-85668fa3aae3" />
<img width="1345" height="536" alt="image" src="https://github.com/user-attachments/assets/229cb43a-1318-4905-a448-3ea0e8be3088" />

---

# 17. 🔑 Login to ECR Again

Run the command again:

```bash
aws ecr-public get-login-password --region us-east-1 | docker login --username AWS --password-stdin public.ecr.aws/k1h7z6n8
```

Successful output:

```text
Login Succeeded
```

<img width="1277" height="129" alt="image" src="https://github.com/user-attachments/assets/1f20e3ff-4f53-433e-85f1-8ed798e00689" />

---

# 18. 🏗️ Build Docker Image

Navigate to the project directory:

```bash
cd cicd-demo-app-project
```

Build the Docker image:

```bash
docker build -t vinaysunhare/ecs-ecr-project .
```

<img width="786" height="680" alt="image" src="https://github.com/user-attachments/assets/70fb661a-265f-4178-9cca-ca7012021a8b" />
<img width="1100" height="417" alt="image" src="https://github.com/user-attachments/assets/e2ae45d2-821e-463b-ba3c-c7804b527083" />

---

# 19. 🐳 Verify Docker Image

Check available Docker images:

```bash
docker images
```

The newly created image should be visible.

<img width="683" height="98" alt="image" src="https://github.com/user-attachments/assets/cec720fd-87c5-4f86-8d81-fc19666a3bd8" />

---

# 20. 🏷️ Tag Docker Image

Tag the Docker image with the ECR Public repository URL:

```bash
docker tag vinaysunhare/ecs-ecr-project:latest public.ecr.aws/k1h7z6n8/vinaysunhare/ecs-ecr-project:latest
```
<img width="1192" height="195" alt="image" src="https://github.com/user-attachments/assets/9ec5faa7-bae4-4253-9bf7-80b242cd6a50" />

---

# 21. 🚀 Push Docker Image to ECR

Push the image:

```bash
docker push public.ecr.aws/k1h7z6n8/vinaysunhare/ecs-ecr-project:latest
```

The Docker image is now uploaded to Amazon ECR Public.

---

# 22. 📦 Verify Image in ECR

Go to:

**Amazon ECR → Public Repositories**

Open:

```text
vinaysunhare/ecs-ecr-project
```

The Docker image should now be available.

<img width="1378" height="341" alt="image" src="https://github.com/user-attachments/assets/36e4994a-dafa-45a9-97b0-ed9ee1ec55d9" />

---

# 23. 🚢 Create Amazon ECS Cluster

Go to:

**AWS Console → ECS**

Create a new cluster.

Use the cluster name:

```text
vinay-cicd-demo-app-project
```

Select:

```text
AWS Fargate
```

Fargate allows the container to run without managing the underlying EC2 servers.

<img width="1345" height="466" alt="image" src="https://github.com/user-attachments/assets/bbca2ba6-439a-4f68-933b-cd67eff2a7fe" />


---

# 24. 📊 Enable Monitoring

Configure monitoring for the ECS workload.

CloudWatch will be used to monitor:

- Application logs
- CPU utilization
- Container activity
- Delivery errors
- Application startup activity

<img width="1310" height="405" alt="image" src="https://github.com/user-attachments/assets/3cbf9aba-ab64-4bbb-bad3-912dfeb4e57f" />


---

# 25. ✅ ECS Cluster Created

The ECS cluster was successfully created.

### Cluster Name

```text
vinay-cicd-demo-app-project
```

<img width="1384" height="255" alt="image" src="https://github.com/user-attachments/assets/8eb40822-dcdf-4e05-be7a-fe44df4b37d6" />


---

# 26. 📋 Create ECS Task Definition

Go to:

**ECS → Task Definitions → Create Task Definition**

Create a new task definition.

### Task Family Name

```text
vinaysunhare-ecr-ecs
```

### Launch Type

```text
AWS Fargate
```

<img width="1611" height="419" alt="image" src="https://github.com/user-attachments/assets/ab7b9b8b-3d2f-42ec-948e-6db0236aaade" />
<img width="1310" height="660" alt="image" src="https://github.com/user-attachments/assets/2da5ed5b-b61b-4e4c-bafa-8122f963de9c" />


---

# 27. 🔐 Create IAM Role for ECS Task

Create/select the required IAM role for the ECS task.

The role allows ECS/Fargate to interact with the required AWS services.

<img width="959" height="233" alt="image" src="https://github.com/user-attachments/assets/19281eef-97b9-4e3f-bd6b-d828a022f1cc" />


---

# 28. 🐳 Configure Container

Add the container to the task definition.

Use the ECR Public image:

```text
public.ecr.aws/k1h7z6n8/vinaysunhare/ecs-ecr-project
```

### Container Port

```text
8000
```

The application runs on port `8000`.

<img width="1377" height="263" alt="image" src="https://github.com/user-attachments/assets/d5a6671e-4254-4963-8867-7a668d7759a0" />
<img width="1288" height="477" alt="image" src="https://github.com/user-attachments/assets/18af649e-0118-4ae5-8e87-10ccc17781df" />


---

# 29. 📝 Configure Logging

Configure CloudWatch logging for the container.

CloudWatch logs allow us to monitor application startup and runtime events.

<img width="1325" height="498" alt="image" src="https://github.com/user-attachments/assets/091d5538-8b17-4b37-b3d1-36a10c9740db" />


---

# 30. ✅ Create Task Definition

Complete the task definition configuration.

The task definition was successfully created.

<img width="1389" height="630" alt="image" src="https://github.com/user-attachments/assets/21be0ab2-ed54-4226-ab55-5e73bdff0186" />


---

# 31. 🚀 Run ECS Task

Go to the ECS cluster.

Select:

```text
Deploy → Run Task
```

Run the task using the previously created task definition.

<img width="1377" height="564" alt="image" src="https://github.com/user-attachments/assets/f2dfa712-614b-4600-a1a2-55e837273781" />


---

# 32. 🟢 ECS Fargate Task Running

After deployment, the ECS task successfully started.

The application container is running using AWS Fargate.

<img width="1374" height="292" alt="image" src="https://github.com/user-attachments/assets/ef9d8a9a-6af1-459a-b9bb-48ef4a5ec118" />


---

# 33. 🔥 Configure Security Group

The application is running on:

```text
Port: 8000
```

Go to the relevant security group.

Select:

```text
Edit inbound rules
```

Add:

```text
Type: Custom TCP
Port: 8000
```

Save the rule.

<img width="1536" height="416" alt="image" src="https://github.com/user-attachments/assets/77c2782f-a0d9-4f89-9c31-88da336bdb61" />


---

# 34. 🌐 Access Application

Go to the running ECS task.

Copy the public IP address.

Example:

```text
54.226.189.32
```

Access the application using:

```text
54.226.189.32:8000
```

The application was successfully accessible through port `8000`.

<img width="1607" height="769" alt="image" src="https://github.com/user-attachments/assets/5b0edb1a-d0ec-4d80-84bf-40148c1ed9cf" />


---

# 35. 📊 CloudWatch Monitoring

Open the ECS task and select the monitoring/logging option.

CloudWatch provides application and container monitoring.

---

## CloudWatch Logs

CloudWatch logs show application events such as:

- Application startup
- Startup completion
- Runtime events
- Application logs

<img width="1333" height="854" alt="image" src="https://github.com/user-attachments/assets/f21d4348-c74a-4eff-9261-700d81a82ae6" />
<img width="1360" height="433" alt="image" src="https://github.com/user-attachments/assets/bf276b82-6de7-4dc9-889e-811f6ad69faf" />

---

# 36. 📈 CPU Utilization

CPU utilization can be monitored through CloudWatch.

The graph shows CPU usage over time.

By hovering over the graph, the specific date/time and CPU utilization can be viewed.

<img width="784" height="241" alt="image" src="https://github.com/user-attachments/assets/f89329f3-dde0-4e83-b127-f9b545003a36" />


---

# 37. 💾 Storage / Volume Monitoring

Volume or block storage usage can also be monitored where applicable.

<img width="1569" height="236" alt="image" src="https://github.com/user-attachments/assets/4014e1cc-0db8-42fd-b72a-eafcb0da89cb" />


---

# 38. ❌ Delivery Errors

CloudWatch metrics can also be used to check delivery errors.

### DeliveryErrors

```text
Sum
```

In this deployment, no delivery error data was present.

This indicates that there were no recorded delivery errors for the monitored metric during the observed period.

<img width="801" height="294" alt="image" src="https://github.com/user-attachments/assets/6e39aa8f-04a8-4414-8ddd-d8784acf0094" />

---

# 39. 🧹 AWS Resource Cleanup

After successfully testing the deployment, all resources were stopped/deleted to avoid unnecessary AWS charges.

The cleanup process included:

1. Stop ECS tasks
2. Delete ECS cluster
3. Deregister ECS task definition
4. Delete ECR repository
5. Terminate EC2 instance

---

# 40. 🛑 Stop ECS Tasks

Go to:

**ECS → Cluster → Tasks**

Select the running tasks and stop them.

<img width="1385" height="520" alt="image" src="https://github.com/user-attachments/assets/b02500a4-7ced-4e0e-b38e-5ef0f9fd7286" />


---

# 41. 🗑️ Delete ECS Cluster

After stopping the tasks, delete the ECS cluster.

Cluster:

```text
vinay-cicd-demo-app-project
```

<img width="531" height="342" alt="image" src="https://github.com/user-attachments/assets/2c5791d9-f47a-4081-89b5-840c3e5fd4fb" />


---

# 42. 🗑️ Deregister Task Definition

Go to:

**ECS → Task Definitions**

Select:

```text
vinaysunhare-ecr-ecs
```

Use:

```text
Action → Deregister
```

<img width="1582" height="472" alt="image" src="https://github.com/user-attachments/assets/43c703f6-bea0-444a-a338-7d1829ac5f2c" />
<img width="477" height="403" alt="image" src="https://github.com/user-attachments/assets/0b8b82f3-09ab-477f-be27-e9867e64f9a5" />


---

# 43. 🗑️ Delete ECR Repository

Go to:

**Amazon ECR → Public Repositories**

Select:

```text
vinaysunhare/ecs-ecr-project
```

Delete the repository.

<img width="1609" height="294" alt="image" src="https://github.com/user-attachments/assets/6ac36ebb-9a46-45d8-ae94-47f1ec66d963" />
<img width="1467" height="300" alt="image" src="https://github.com/user-attachments/assets/66e35460-40dd-424e-bfaa-dd550ea91470" />

---

# 44. 🛑 Terminate EC2 Instance

Go to:

**EC2 → Instances**

Select the EC2 instance and terminate it.

The EC2 instance was terminated to avoid unnecessary charges.

<img width="1424" height="253" alt="image" src="https://github.com/user-attachments/assets/7fadfc35-112a-4c33-a9b9-6dcef440726f" />

---

# 45. ✅ Final AWS Resource Status

After cleanup:

```text
ECS Tasks          → Stopped
ECS Cluster        → Deleted
Task Definition    → Deregistered
ECR Repository     → Deleted
EC2 Instance       → Terminated
```

No unnecessary AWS resources were left running.

---

# 🧠 DevOps Concepts Demonstrated

This project demonstrates practical experience with the following DevOps and AWS concepts:

### AWS

- AWS IAM
- IAM Users
- IAM Groups
- IAM Policies
- IAM Roles
- Amazon EC2
- Amazon ECR
- Amazon ECS
- AWS Fargate
- Amazon CloudWatch
- Security Groups

### Containerization

- Docker installation
- Docker permissions
- Docker image creation
- Dockerfile
- Docker image tagging
- Docker image registry
- Docker image push
- Container deployment

### AWS CLI

- AWS CLI installation
- `aws configure`
- AWS authentication
- ECR authentication
- ECR image push

### Linux

- Ubuntu/Linux administration
- Package installation
- Git
- User/group management
- Permissions
- Docker socket permissions
- CLI troubleshooting

### Monitoring

- CloudWatch Logs
- CPU Utilization
- Container monitoring
- Application startup monitoring
- Delivery error monitoring

---

# 🔄 Complete Deployment Flow

```text
1. Create IAM User
        ↓
2. Create IAM Group
        ↓
3. Attach EC2/ECS/ECR Permissions
        ↓
4. Create Access Key
        ↓
5. Launch EC2 Instance
        ↓
6. Connect to EC2
        ↓
7. Update Linux Machine
        ↓
8. Clone GitHub Repository
        ↓
9. Create ECR Public Repository
        ↓
10. Install Docker
        ↓
11. Configure Docker Permissions
        ↓
12. Install AWS CLI
        ↓
13. Configure AWS CLI
        ↓
14. Login to ECR
        ↓
15. Build Docker Image
        ↓
16. Tag Docker Image
        ↓
17. Push Image to ECR
        ↓
18. Create ECS Cluster
        ↓
19. Create Fargate Task Definition
        ↓
20. Configure IAM Role
        ↓
21. Configure Container
        ↓
22. Configure Port 8000
        ↓
23. Configure CloudWatch Logging
        ↓
24. Run ECS Fargate Task
        ↓
25. Configure Security Group
        ↓
26. Access Application
        ↓
27. Monitor with CloudWatch
        ↓
28. Cleanup AWS Resources
```

---

# 📸 Project Screenshots

The project screenshots document the complete deployment process from IAM configuration to AWS resource cleanup.

Recommended screenshot directory structure:

```text
screenshots/
├── 01-create-iam-user.png
├── 02-create-iam-group.png
├── 03-ec2-full-access.png
├── 04-ecs-full-access.png
├── 05-ecr-permission.png
├── 06-create-access-key.png
├── 07-launch-ec2.png
├── 08-linux-machine.png
├── 09-create-key-pair.png
├── 10-ec2-running.png
├── 11-connect-ec2.png
├── 12-git-clone.png
├── 13-create-ecr-repository.png
├── 14-ecr-empty.png
├── 15-ecr-push-commands.png
├── 16-docker-installation.png
├── 17-docker-permission.png
├── 18-aws-cli-installation.png
├── 19-aws-configure.png
├── 20-ecr-permission-error.png
├── 21-ecr-public-permissions.png
├── 22-ecr-login-success.png
├── 23-docker-build.png
├── 24-docker-images.png
├── 25-docker-push.png
├── 26-image-in-ecr.png
├── 27-create-ecs-cluster.png
├── 28-ecs-monitoring.png
├── 29-ecs-cluster-created.png
├── 30-task-definition.png
├── 31-ecs-iam-role.png
├── 32-container-configuration.png
├── 33-cloudwatch-logging.png
├── 34-task-definition-created.png
├── 35-run-ecs-task.png
├── 36-ecs-task-running.png
├── 37-security-group-8000.png
├── 38-application-running.png
├── 39-cloudwatch-logs.png
├── 40-cpu-utilization.png
├── 41-volume-monitoring.png
├── 42-delivery-errors.png
├── 43-stop-ecs-task.png
├── 44-delete-ecs-cluster.png
├── 45-deregister-task-definition.png
├── 46-delete-ecr-repository.png
└── 47-terminate-ec2.png
```

---

# 🎯 Project Outcome

Successfully deployed a Dockerized application from GitHub to **Amazon ECR Public** and then deployed the container on **Amazon ECS using AWS Fargate**.

The application was successfully exposed on **port 8000** and monitored using **Amazon CloudWatch**.

The project demonstrates an end-to-end practical workflow involving:

```text
GitHub
   ↓
EC2
   ↓
Docker
   ↓
Amazon ECR
   ↓
Amazon ECS
   ↓
AWS Fargate
   ↓
CloudWatch
```

This project provides practical hands-on experience with **AWS cloud infrastructure, Docker containerization, container registry, ECS Fargate deployment, IAM, Linux administration and CloudWatch monitoring**.

---

# 👨‍💻 Author

**Vinay Sunhare**

DevOps / Cloud Engineering Learner

GitHub:

https://github.com/vinaysunhare

Project Repository:

https://github.com/vinaysunhare/cicd-demo-app-project

---

# ⭐ Key Skills Demonstrated

```text
AWS
Docker
Linux
IAM
EC2
ECR
ECS
Fargate
CloudWatch
AWS CLI
Git
GitHub
Containerization
Container Deployment
IAM Policies
IAM Roles
Security Groups
Application Monitoring
Troubleshooting
```

---

## ⚠️ Security Note

Never commit the following information to GitHub:

```text
AWS Access Key ID
AWS Secret Access Key
Private SSH Keys
Passwords
Tokens
API Keys
Credentials
```

Use IAM roles, environment variables, AWS Secrets Manager, or other secure credential-management mechanisms instead of exposing credentials in source code or documentation.
