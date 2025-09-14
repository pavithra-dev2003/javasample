
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

1. Clone the repository  

git clone https://github.com/<your-username>/<your-repo>.git
cd <your-repo>/javasample

2. Build with Maven
mvn clean package

3. Build Docker Image
docker build -t <docker_username>/javaexample:local .


### CI/CD Pipeline (GitHub Actions)

* This repository includes a GitHub Actions workflow that:

* Builds the project with Maven.

* Builds a Docker image using the Dockerfile.

* Pushes the image to Docker Hub with a unique tag.

## Workflow File: .github/workflows/build-docker.yml
#### workflow trigger
```
on:
  push:
    branches:
      - main
```


Runs the workflow automatically when code is pushed to the main branch.
#### building of the image
```
Job: build
```

##Runs on ubuntu-latest and performs the following steps.

#### Checkout repository
```
- name: Checkout repo
  uses: actions/checkout@v2
```

Pulls the repository source into the runner so subsequent steps can access the code.

#### Set up Java 17
```
- name: Set up Java 17
  uses: actions/setup-java@v2
  with:
    distribution: 'adopt'
    java-version: '17'
```
Installs Java 17 (AdoptOpenJDK). Required to run Maven and build the Java project.

#### Build with Maven
```
- name: Build with Maven
  run: mvn clean package
  working-directory: javasample
```

Runs mvn clean package inside the javasample directory to compile and package your application into a JAR.

#### Docker login
```
- name: Docker login
  uses: docker/login-action@v2
  with:
    username: ${{ secrets.DOCKER_USERNAME }}
    password: ${{ secrets.DOCKER_PASSWORD }}
```


Logs into Docker Hub using repository secrets.
You must add these secrets in your GitHub repo settings:

* DOCKER_USERNAME — your Docker Hub username

* DOCKER_PASSWORD — your Docker Hub password (or access token)

(Go to Settings → Secrets and variables → Actions → New repository secret.)

#### Build and push Docker image
```
- name: Build and push Docker image
  uses: docker/build-push-action@v5
  with:
    context: javasample
    file: javasample/Dockerfile
    push: true
    tags: ${{ secrets.DOCKER_USERNAME }}/javaexample:${{ github.run_number }}
```

Uses docker/build-push-action to:
build the image from javasample/Dockerfile,
tag it as <username>/javaexample:<run-number>,
push the image to Docker Hub (because push: true).
github.run_number provides a unique numeric tag per workflow run (useful for versioning).
This action uses BuildKit/buildx under the hood (supports multi-platform builds, cache, etc.).

#### Give the unique tag name
```
tags: |
  ${{ secrets.DOCKER_USERNAME }}/javaexample:${{ github.run_number }}
  ${{ secrets.DOCKER_USERNAME }}/javaexample:latest
```
#### Logout from Docker
```
- name: Logout from Docker
  run: docker logout
```
Logs out from Docker Hub for security.

#### GitHub Secrets
You must configure the following repository secrets in GitHub:
```

DOCKER_USERNAME → Your Docker Hub username

DOCKER_PASSWORD → Your Docker Hub password or access token
```

#### Docker Image Naming

The image will be pushed to Docker Hub as:
<docker_username>/javaexample:<github_run_number>

#### Run the Container
```
docker run -p 8080:8080 <docker_username>/javaexample:local
```




