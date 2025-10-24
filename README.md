

# Ticket Booking App: CI/CD with Docker, Jenkins, and Kubernetes

This repository hosts a **web-based Ticket Booking Application** and demonstrates a complete **Continuous Integration / Continuous Delivery (CI/CD)** pipeline. The application is containerized with **Docker**, built automatically with **Jenkins**, and deployed/scaled using **Kubernetes (K8s)**.


##  Key Technologies

  * **Application:** Python/Flask (Simulated)
  * **Containerization:** **Docker**
  * **CI/CD:** **Jenkins** Pipeline
  * **Orchestration:** **Kubernetes (K8s)**
  * **Version Control:** **Git** with GitFlow

## 1️ Project Structure

The project directory is structured to clearly separate application code, containerization instructions, CI/CD pipeline definition, and Kubernetes manifests.

```
tickets-booking-app/
├── app.py                  # Main Flask application file
├── requirements.txt        # Python dependencies
├── Dockerfile              # Instructions for building the Docker image
├── Jenkinsfile             # Declarative pipeline for Jenkins CI/CD
├── k8s-deployment.yaml     # Kubernetes Deployment manifest
├── k8s-service.yaml        # Kubernetes Service (NodePort) manifest
└── README.md               # This file
```


## 2️ Version Control and GitFlow

This project utilizes a simplified **GitFlow** branching strategy:

| Branch | Purpose | Status |
| :--- | :--- | :--- |
| `main` | Production-ready, stable code. | Deployment target for stable releases. |
| `develop` | Integration branch for new features. | Triggers the Jenkins CI/CD pipeline. |

### Initialize and Setup

The following commands were used to set up the repository:

```bash
# Initialize Git repo
git init

# Add all files and commit
git add .
git commit -m "Initial project structure"

# Set main branch and push to remote
git branch -M main
git remote add origin https://github.com/tejasvini2805/tickets-booking-app.git
git push -u origin main

# Create and push the develop branch
git checkout -b develop
git push -u origin develop
```


## 3️ Containerization (Docker)

The application is packaged into a container image for consistent and reliable deployment across environments.

###  Build and Push (Manual)

To manually build and push the image, you can use the following steps:

```bash
# Build the Docker image locally
docker build -t tickets-booking-app:latest .

# Tag the image for Docker Hub
docker tag tickets-booking-app:latest tejasvini2805/tickets-booking-app:latest

# Log in to Docker Hub and push the image
docker login
docker push tejasvini2805/tickets-booking-app:latest
```



## 4️ Continuous Integration / Delivery (Jenkins)

The **Jenkinsfile** defines a declarative pipeline that automatically executes the CI process upon changes pushed to the **`develop`** branch.

###  Jenkins Pipeline (`Jenkinsfile`)

```groovy
pipeline {
    agent any

    environment {
        // ID of the stored Docker Hub credentials in Jenkins
        DOCKERHUB_CREDENTIALS = 'dockerhub-credentials'
        IMAGE_NAME = 'tejasvini2805/tickets-booking-app'
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'develop', url: 'https://github.com/tejasvini2805/tickets-booking-app.git'
            }
        }
        stage('Build Docker Image') {
            steps {
                script {
                    // Builds the image, tagging it with :latest
                    dockerImage = docker.build("${IMAGE_NAME}:latest")
                }
            }
        }
        stage('Push Docker Image') {
            steps {
                script {
                    // Pushes the image to Docker Hub using stored credentials
                    docker.withRegistry('https://index.docker.io/v1/', DOCKERHUB_CREDENTIALS) {
                        dockerImage.push()
                        dockerImage.push('latest')
                    }
                }
            }
        }
    }
}
```


## 5️ Kubernetes Deployment

The application is deployed to a Kubernetes cluster for orchestration, scalability, and high availability.

### Manifests Overview

| File | Type | Description |
| :--- | :--- | :--- |
| `k8s-deployment.yaml` | `Deployment` | Manages 3 replicas of the application pods. Pulls image from `tejasvini2805/tickets-booking-app:latest`. |
| `k8s-service.yaml` | `Service` | Exposes the application using a `NodePort` on port **30080**. |

###  Apply Deployment

```powershell
# Apply the Deployment and Service manifests
kubectl apply -f k8s-deployment.yaml
kubectl apply -f k8s-service.yaml

# Verify the status of the pods and service
kubectl get pods
kubectl get svc
```

-----

## 6️ Access the Application

Once the pods are **`Running`** and the service is active, the application can be accessed via the exposed NodePort.

Open your web browser and navigate to:

```arduino
http://localhost:30080
```


## 7️ Scaling the Application

Kubernetes makes horizontal scaling simple. You can easily increase the number of application instances (pods) for handling increased load.

```powershell
# Scale the deployment from 3 to 5 replicas
kubectl scale deployment ticket-booking-deployment --replicas=5

# Verify the new pods are running
kubectl get pods
```

Kubernetes automatically handles traffic distribution among all available pods.
