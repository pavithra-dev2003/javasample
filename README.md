#Java Sample Application

This is a sample Java application built with Maven and containerized using Docker.
The project is integrated with GitHub Actions to automatically build and push Docker images to Docker Hub.

# Java Sample Application   

This is a sample Java application built with **Maven** and containerized using **Docker**.  
The project is integrated with **GitHub Actions** to automatically build and push Docker images to **Docker Hub**.  

##  Features  
- Java 17 project  
- Built with Maven (`mvn clean package`)  
- Dockerized for container deployment  
- CI/CD pipeline using GitHub Actions  
- Automatic image push to Docker Hub  

## Build and Run Locally  

### 1. Clone the repository  
```bash
git clone https://github.com/<your-username>/<your-repo>.git
cd <your-repo>/javasample

2. Build with Maven
mvn clean package

3. Build Docker Image
docker build -t <docker_username>/javaexample:local .


CI/CD Pipeline (GitHub Actions)

This repository includes a GitHub Actions workflow that:

Builds the project with Maven.

Builds a Docker image using the Dockerfile.

Pushes the image to Docker Hub with a unique tag.

Workflow File: .github/workflows/build-docker.yml
name: build and push to Docker Hub

on:
  push:
    branches:
      - main

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
    - name: Checkout repo
      uses: actions/checkout@v2

    - name: Set up Java 17
      uses: actions/setup-java@v2
      with:
        distribution: 'adopt'
        java-version: '17'

    - name: Build with Maven
      run: mvn clean package
      working-directory: javasample

    - name: Docker login
      uses: docker/login-action@v2
      with:
        username: ${{ secrets.DOCKER_USERNAME }}
        password: ${{ secrets.DOCKER_PASSWORD }}

    - name: Build and push Docker image
      uses: docker/build-push-action@v5
      with:
        context: javasample
        file: javasample/Dockerfile
        push: true
        tags: ${{ secrets.DOCKER_USERNAME }}/javaexample:${{ github.run_number }}

    - name: Logout from Docker
      run: docker logout

🔑 GitHub Secrets

You must configure the following repository secrets in GitHub:

DOCKER_USERNAME → Your Docker Hub username

DOCKER_PASSWORD → Your Docker Hub password or access token

📦 Docker Image Naming

The image will be pushed to Docker Hub as:

<docker_username>/javaexample:<github_run_number>

4. Run the Container
docker run -p 8080:8080 <docker_username>/javaexample:local




