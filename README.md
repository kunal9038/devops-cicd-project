
# DevOps CI/CD Project

A hands-on DevOps project demonstrating a complete **CI/CD pipeline** using GitHub Actions, Docker, Docker Hub, and AWS EC2.

The project automatically tests the application, builds a Docker image, pushes the image to Docker Hub, and deploys the latest version to an AWS EC2 server.

---

## 🚀 Project Overview

This project demonstrates how a developer's code change can automatically move through a CI/CD pipeline and become a running application on an AWS EC2 server.

### CI/CD Flow

```text
Developer
    |
    | git push
    v
GitHub Repository
    |
    v
GitHub Actions
    |
    +---- Install Python dependencies
    |
    +---- Run pytest
    |
    +---- Build Docker image
    |
    +---- Push image to Docker Hub
    |
    v
SSH Connection to AWS EC2
    |
    v
deploy.sh
    |
    +---- Pull latest Docker image
    |
    +---- Stop old container
    |
    +---- Remove old container
    |
    +---- Start new container
    |
    v
Flask Application
```

---

## 🛠️ Technologies Used

| Technology | Purpose |
|---|---|
| Python | Application development |
| Flask | Web application framework |
| Pytest | Automated testing |
| Git | Version control |
| GitHub | Source code repository |
| GitHub Actions | CI/CD automation |
| Docker | Application containerization |
| Docker Hub | Container image registry |
| AWS EC2 | Application deployment server |
| Linux | Server operating system |
| SSH | Secure connection between GitHub Actions and EC2 |
| Bash | Deployment automation |

---

## 📁 Project Structure

```text
devops-cicd-project/
│
├── app/
│   ├── __init__.py
│   └── app.py
│
├── tests/
│   └── test_app.py
│
├── .github/
│   └── workflows/
│       └── ci.yml
│
├── .dockerignore
├── .gitignore
├── Dockerfile
├── pytest.ini
├── requirements.txt
├── README.md
└── deploy.sh
```

---

## 🐍 Application

The application is built using Flask.

It provides two endpoints.

### Home

```text
GET /
```

Returns:

```json
{
  "application": "DevOps CI/CD Demo",
  "status": "running",
  "version": "1.0.0"
}
```

### Health Check

```text
GET /health
```

Returns:

```json
{
  "status": "healthy"
}
```

The health endpoint is useful for verifying that the application is running correctly after deployment.

---

# 💻 Run Locally

## 1. Clone the repository

```bash
git clone https://github.com/kunal9038/devops-cicd-project.git
cd devops-cicd-project
```

## 2. Create a virtual environment

Windows PowerShell:

```powershell
python -m venv venv
```

Activate it:

```powershell
.\venv\Scripts\Activate.ps1
```

## 3. Install dependencies

```bash
pip install -r requirements.txt
```

## 4. Run tests

```bash
pytest
```

Expected result:

```text
2 passed
```

## 5. Start the application

```bash
python app/app.py
```

The application runs on:

```text
http://localhost:5000
```

Health check:

```text
http://localhost:5000/health
```

---

# 🐳 Docker

The application is packaged into a Docker image so that the same application environment can be used across different systems.

## Build the image

```bash
docker build -t devops-cicd-app .
```

## Run the container

```bash
docker run -d \
  --name devops-cicd-container \
  -p 5000:5000 \
  devops-cicd-app
```

Check the running container:

```bash
docker ps
```

Test the application:

```bash
curl http://localhost:5000/health
```

---

# 🧪 Running Tests Inside Docker

The Docker image also contains the test files.

Tests can be executed with:

```bash
docker run --rm devops-cicd-app pytest
```

Expected result:

```text
2 passed
```

This verifies that the application and its dependencies work correctly inside the Docker environment.

---

# 📦 Docker Hub

The application image is published to Docker Hub.

Image:

```text
kunalsingh9038/devops-cicd-project:latest
```

The image is automatically pushed by GitHub Actions after the tests and Docker build succeed.

---

# ☁️ AWS EC2 Deployment

The application is deployed to an Ubuntu AWS EC2 instance.

Docker is installed on the EC2 server and the `ubuntu` user is configured to run Docker commands.

The deployed container uses:

```text
Container Name:
devops-cicd-container
```

```text
Docker Image:
kunalsingh9038/devops-cicd-project:latest
```

```text
Container Port:
5000
```

The application is accessed using HTTP:

```text
http://<EC2_PUBLIC_IP>:5000
```

Health check:

```text
http://<EC2_PUBLIC_IP>:5000/health
```

> HTTPS is not configured in this project. The application currently serves HTTP on port 5000.

---

# 🚀 Deployment Script

The EC2 server contains a Bash deployment script:

```text
deploy.sh
```

The script automates the deployment process.

### Deployment process

```text
docker pull
      ↓
Stop existing container
      ↓
Remove existing container
      ↓
Start new container
```

The script uses:

```bash
IMAGE="kunalsingh9038/devops-cicd-project:latest"
CONTAINER="devops-cicd-container"
```

The script can be executed manually on EC2:

```bash
./deploy.sh
```

After deployment, the application can be verified with:

```bash
curl http://localhost:5000/health
```

Expected response:

```json
{
  "status": "healthy"
}
```

---

# ⚙️ GitHub Actions CI/CD

The workflow is located at:

```text
.github/workflows/ci.yml
```

The pipeline is triggered when code is pushed to the `main` branch.

It also runs for pull requests targeting `main`.

## Pipeline stages

### 1. Checkout

GitHub Actions checks out the source code.

```yaml
uses: actions/checkout@v4
```

### 2. Python Setup

Python 3.13 is configured.

### 3. Install Dependencies

```bash
pip install -r requirements.txt
```

### 4. Run Tests

```bash
pytest
```

The deployment process continues only after the tests succeed.

### 5. Build Docker Image

```bash
docker build -t kunalsingh9038/devops-cicd-project:latest .
```

### 6. Login to Docker Hub

GitHub Actions uses repository secrets to authenticate with Docker Hub.

### 7. Push Docker Image

```bash
docker push kunalsingh9038/devops-cicd-project:latest
```

### 8. Deploy to EC2

GitHub Actions connects to the EC2 server using SSH and executes:

```bash
cd ~
./deploy.sh
```

This pulls the latest Docker image and restarts the application container.

---

# 🔐 GitHub Secrets

The following GitHub repository secrets are required for deployment:

```text
DOCKERHUB_USERNAME
DOCKERHUB_TOKEN

EC2_HOST
EC2_USER
EC2_KEY
```

### Docker Hub

```text
DOCKERHUB_USERNAME
```

Contains the Docker Hub username.

```text
DOCKERHUB_TOKEN
```

Contains a Docker Hub access token with permission to push images.

### AWS EC2

```text
EC2_HOST
```

Contains the EC2 public IP address.

```text
EC2_USER
```

For the Ubuntu EC2 server:

```text
ubuntu
```

```text
EC2_KEY
```

Contains the SSH private key used to connect to the EC2 server.

> Secrets should never be committed to the Git repository or exposed in source code.

---

# 🔄 Complete Deployment Workflow

When a change is made to the application:

```text
1. Developer modifies code
        ↓
2. git add
        ↓
3. git commit
        ↓
4. git push
        ↓
5. GitHub Actions starts
        ↓
6. Dependencies installed
        ↓
7. Automated tests run
        ↓
8. Docker image is built
        ↓
9. Image is pushed to Docker Hub
        ↓
10. GitHub Actions connects to EC2
        ↓
11. deploy.sh executes
        ↓
12. Latest Docker image is pulled
        ↓
13. Old container is stopped
        ↓
14. Old container is removed
        ↓
15. New container starts
        ↓
16. Application becomes available
```

---

# 🧪 Verification

After deployment, verify the container on EC2:

```bash
docker ps
```

Expected:

```text
devops-cicd-container
```

Check the application:

```bash
curl http://localhost:5000
```

Check the health endpoint:

```bash
curl http://localhost:5000/health
```

Expected:

```json
{
  "status": "healthy"
}
```

---

# 🧠 DevOps Concepts Demonstrated

This project demonstrates practical understanding of:

- Version control with Git
- GitHub repositories
- Continuous Integration
- Continuous Deployment
- Automated testing
- GitHub Actions
- Docker image creation
- Docker containers
- Docker Hub
- Linux server administration
- AWS EC2
- SSH
- Bash scripting
- Deployment automation
- Application health checks
- CI/CD troubleshooting

---

# 🛠️ Troubleshooting

## GitHub Actions cannot connect to EC2

If deployment fails with:

```text
dial tcp ...:22: i/o timeout
```

check the EC2 Security Group and ensure SSH access on port `22` is allowed.

Also verify that the EC2 public IP configured in:

```text
EC2_HOST
```

matches the current EC2 public IP.

## Application cannot be accessed from the browser

Verify the container:

```bash
docker ps
```

Verify port mapping:

```bash
sudo ss -tulpn | grep 5000
```

Verify the application locally on EC2:

```bash
curl http://localhost:5000/health
```

Also check that the EC2 Security Group allows inbound TCP traffic on port `5000`.

The application uses **HTTP**, not HTTPS:

```text
http://<EC2_PUBLIC_IP>:5000
```

---

# 🎯 Project Goal

The primary goal of this project is to demonstrate how application source code can move from development to a running production-like environment through an automated CI/CD pipeline.

The project focuses on understanding the complete deployment lifecycle rather than relying on manual deployment steps.

---

## 👨‍💻 Author

**Kunal Singh**

DevOps / Cloud Engineering Learner

GitHub:

```text
https://github.com/kunal9038
```
