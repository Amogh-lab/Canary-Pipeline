# Canary-Pipeline

## 1. Introduction
The **Canary Pipeline Project** demonstrates the use of continuous integration and deployment using **GitHub Actions**, **Docker**, and **Kubernetes**.  
A canary deployment allows gradual rollout of new application versions to a subset of users before full deployment, reducing risks of downtime.

## 2. Technologies Used
- Node.js (Backend Framework)
- Docker (Containerization)
- GitHub Actions (CI/CD)
- Kubernetes (Deployment & Scaling)

## 3. Project Structure
The project consists of a simple Node.js application containerized with Docker.  
GitHub Actions automates the build and push process to Docker Hub.  
Kubernetes manages the canary and stable releases.

## 4. Docker Setup
A Dockerfile defines the environment for the Node.js app. The build process installs dependencies and runs the app on port 3000.

```bash
FROM node:18-alpine
WORKDIR /app
COPY package*.json ./
RUN npm install --production
COPY . .
CMD ["npm", "start"]
```

## 5. GitHub Actions Workflow
GitHub Actions automates the build and push process whenever a commit is pushed to the main branch.  
It logs in to Docker Hub using secrets, builds the image, and pushes it.

```yaml
name: Build and Push to Docker Hub
on:
  push:
    branches: [ main ]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v3
      - name: Build and push image
        uses: docker/build-push-action@v4
```

## 6. Kubernetes Deployment
The application is deployed to Kubernetes using a canary strategy.  
Two deployments exist — one stable and one canary. The canary receives a small percentage of traffic for testing before promoting to stable.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: canary-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: canary-demo
  template:
    metadata:
      labels:
        app: canary-demo
    spec:
      containers:
        - name: app
          image: <your-dockerhub-username>/canary-demo-pipeline:latest
          ports:
            - containerPort: 3000
```

## 7. How the Canary Release Works
1. GitHub Action builds and pushes a new image to Docker Hub.  
2. The canary deployment updates with the new image.  
3. Only a small portion of traffic is routed to the canary pod.  
4. Once verified, the stable deployment is updated to match the canary version.

## 8. Future Improvements
- Integrate Helm charts for better deployment management.  
- Add Prometheus and Grafana for real-time monitoring.  
- Implement automatic rollback on failure detection.  
- Add blue-green deployment as an alternative strategy.

## 9. Author
Created by **Amogh A P** – AIML Engineer & Cloud Enthusiast.

## 10. Contributing

Contributions are welcome! Please submit a pull request with your changes.

## 11. License

This project is licensed under the MIT License. See `LICENSE` for details.

