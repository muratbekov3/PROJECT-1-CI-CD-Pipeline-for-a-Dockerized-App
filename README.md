CI/CD Pipeline for a Dockerized Flask App
📖 Project Overview

This project demonstrates a fundamental DevOps workflow. It features a simple Python Flask application that is containerized using Docker. The core of the project is a Continuous Integration/Continuous Deployment (CI/CD) pipeline built with GitHub Actions.

Whenever code is pushed to the main branch, the pipeline automatically:

    Builds a new Docker image.

    Logs into Docker Hub securely.

    Pushes the image to a public Docker Hub repository.

    Allows for deployment via a generic Bash script.
    

🛠️ Tech Stack

    Language: Python 3.10 (Flask)

    Containerization: Docker

    CI/CD: GitHub Actions

    Registry: Docker Hub

    Scripting: Bash

📂 Project Structure

      ├── .github/
      │   └── workflows/
      │       └── ci.yml        # The GitHub Actions configuration
      ├── app/
      │   └── app.py            # The simple Flask application
      ├── deploy.sh             # Script to pull and run the new image
      ├── Dockerfile            # Blueprint for the Docker container
      └── README.md             # Project documentation

⚙️ Setup & Installation
1. Prerequisites

Before running this project, ensure you have:

    Docker Desktop installed locally.

    A Docker Hub account.

    A GitHub account.

2. The Application Code

The application is a lightweight web server.

      app/app.py
      Python
      
      from flask import Flask
      
      app = Flask(__name__)
      
      @app.route("/")
      def home():
          return "Hello from DevOps Project!"
      
      if __name__ == "__main__":
          app.run(host="0.0.0.0", port=5000)

3. Docker Configuration

The Dockerfile defines the environment.

Dockerfile

    FROM python:3.10-slim

    WORKDIR /app

    COPY app/app.py .

    RUN pip install flask

    EXPOSE 5000

    CMD ["python", "app.py"]

🚀 The CI/CD Pipeline

The pipeline is defined in .github/workflows/ci.yml. It triggers on every push to the main branch.
Configuration (ci.yml)

    Note: This configuration pushes two tags: the specific commit SHA (for version history) and latest (for easy deployment).

YAML

      on:
        push:
          branches: [ "main" ]
      
      jobs:
        build-test-push:
          runs-on: ubuntu-latest
          steps:
          - name: Checkout
            uses: actions/checkout@v4
      
          - name: Set up QEMU (for multi-arch, optional)
            uses: docker/setup-qemu-action@v2
      
          - name: Set up Docker Buildx
            uses: docker/setup-buildx-action@v2
      
          - name: Login to Docker Hub
            uses: docker/login-action@v2
            with:
              username: ${{ secrets.DOCKERHUB_USERNAME }}
              password: ${{ secrets.DOCKERHUB_TOKEN }}
      
          - name: Build and push
            uses: docker/build-push-action@v4
            with:
              context: .
              file: ./Dockerfile
              push: true
              tags: ${{ secrets.DOCKERHUB_USERNAME }}/devops-simple:latest,${{ secrets.DOCKERHUB_USERNAME }}/devops-simple:${{ github.sha }}

        
🔐 Setting up Secrets

For the pipeline to work, you must set the following Secrets in your GitHub Repository settings (Settings -> Secrets and variables -> Actions):

      DOCKERHUB_USERNAME: Your Docker Hub ID.
  
      DOCKERHUB_TOKEN: Your Docker Hub Access Token (Create one in Docker Hub -> Account Settings -> Security).

💻 Deployment

To deploy the latest version of the application on your local machine (or server), run the provided bash script.

deploy.sh Make sure to replace YOUR_DH_USER with your actual username.
Bash

      #!/bin/bash
      
      # Pull the latest image
      docker pull YOUR_DH_USER/devops-simple:latest
      
      # Stop and remove the existing container (if any)
      docker stop devops-simple || true
      docker rm devops-simple || true
      
      # Run the new container
      docker run -d -p 5000:5000 --name devops-simple YOUR_DH_USER/devops-simple:latest
      
      echo "Deployment successful! App running on port 5000."

Run the deployment:
Bash
      
      chmod +x deploy.sh
      ./deploy.sh

Visit http://localhost:5000 to see your app live.
