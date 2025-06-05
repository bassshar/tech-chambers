React App with Docker

This is a simple React application bootstrapped with [Create React App](https://create-react-app.dev/), containerized using Docker.

🚀 Features

-  React (CRA)
-  Docker support
-  WebVitals performance monitoring
-  Production-ready build served via Nginx

---

Getting Started

Prerequisites

- [Node.js](https://nodejs.org/) (for local development)
- [Docker](https://www.docker.com/) (for containerization)



Local Development
# Install dependencies
npm install
# Start the development server
npm start
# Build the production  
npm run build

# Build the docker image
docker build -t eu-chamber-app .
# Run the image
docker run -p 80:80 eu-chamber-app

All of this runs through a workflow.
The workflow runs automatically on every push to the main branch.

on:
  push:
    branches:
      - main
Permissions & Security
This part of the code grants read access to the repository content and write access to request a temporary AWS identity token

permissions:
  id-token: write
  contents: read

# Workflow Steps (Explaination of CI/CD)
1. Checkout Code

- name: Checkout Code
  uses: actions/checkout@v3

2. Configure AWS Credentials

- name: Configure AWS Credentials via OIDC
  uses: aws-actions/configure-aws-credentials@v2
  with:
    role-to-assume: arn:aws:iam::842747873421:role/github-role
    aws-region: us-east-1
3. Login to Amazon ECR

- name: Login to Amazon ECR
  uses: aws-actions/amazon-ecr-login@v2
This step Authenticates Docker with ECR registry so it can push images.

4. Build, Tag, and Push Docker Image

docker build -t $IMAGE_URI .
docker push $IMAGE_URI

This step Builds a production Docker image using the Dockerfile.

- Tags the image with the ECR registry and app name.

- Pushes it to your ECR repository.
  
5. Deploy to EC2 via SSH

- name: Deploy to EC2
  uses: appleboy/ssh-action@v1.0.0

This step connects to the EC2 instance using SSH and:

- Logs into ECR from within EC2.

- Pulls the latest image from ECR.

- Stops and removes the old container (if running).

- Runs the new container on port 80.
# EC2 credentials (IP and SSH private key) are securely stored in GitHub Secrets:

- EC2_HOST

- EC2_SSH
