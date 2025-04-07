This repository demonstrates how to set up a simple Java application using a Dockerfile, build and push the Docker image to Docker Hub via a GitHub Actions workflow, and then pull and run the image locally or on any Docker-compatible environment.

Table of Contents

Overview
Requirements
Project Structure
Dockerfile Explanation
GitHub Actions Workflow
Local Development
Building & Running Locally
Automated Build & Push with GitHub Actions
Pulling & Running from Docker Hub
License
Overview

This project contains a sample Java “HelloWorld” application and a continuous integration (CI) pipeline built with GitHub Actions. Every time code is pushed to the main (or master) branch:

The GitHub Actions workflow checks out the source code.
Docker logs in to Docker Hub using secure credentials.
A Docker image is built from the provided Dockerfile.
The image is tagged and pushed to Docker Hub.
Afterward, you can pull and run the Docker image locally (or anywhere Docker is installed).

Requirements

Java (for local development and compilation, if needed outside of Docker)
Docker (for local image building and running)
GitHub account (to host the repository and set up GitHub Actions)
Docker Hub account (to push and host Docker images)
GitHub Secrets configured:
DOCKER_USERNAME (your Docker Hub username)
DOCKER_PASSWORD (your Docker Hub personal access token or password)
Project Structure

.
├── src
│   └── HelloWorld.java     // Simple Java "HelloWorld" program
├── .github
│   └── workflows
│       └── docker.yml      // GitHub Actions workflow for building & pushing to Docker Hub
└── Dockerfile              // Instructions to build the Docker image
Dockerfile Explanation

FROM openjdk:23     # Base image: Official OpenJDK 23
WORKDIR /app        # Set working directory inside the container

COPY src /app/      # Copy source code from local 'src' folder into '/app' in the container
RUN javac *.java    # Compile all Java files

CMD ["java", "HelloWorld"]  # Run the HelloWorld main class
Key Points:

FROM openjdk:23 specifies the base Docker image, which includes OpenJDK for running Java applications.
WORKDIR /app sets the current working directory.
COPY src /app/ brings your .java files into the container’s /app folder.
RUN javac *.java compiles the Java code into .class files.
CMD ["java", "HelloWorld"] executes the compiled HelloWorld class when the container starts.
GitHub Actions Workflow

Below is the core of .github/workflows/docker.yml:

name: Build and Push to Docker Hub

on:
  push:
    branches:
      - master  # or 'main'

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout repository
        uses: actions/checkout@v4

      - name: Log in to Docker Hub
        run: echo "${{ secrets.DOCKER_PASSWORD }}" | docker login -u "${{ secrets.DOCKER_USERNAME }}" --password-stdin

      - name: Build Docker Image
        run: docker build -t ${{ secrets.DOCKER_USERNAME }}/is147:${{ github.run_number }} .

      - name: Push Docker Image
        run: docker push ${{ secrets.DOCKER_USERNAME }}/is147:${{ github.run_number }}
How It Works:

Trigger: Any push to the master (or main) branch triggers this workflow.
Checkout: The repository’s code is checked out.
Docker Login: GitHub Actions logs in to Docker Hub using the secrets you set in the repository settings.
Build & Tag Image: The Dockerfile is used to build the image and tag it with your Docker Hub username plus a version number based on the GitHub run number.
Push Image: The newly built image is pushed to your Docker Hub repository.
Local Development

For quick edits or testing outside Docker, you can:

Install Java (OpenJDK).
Navigate to the src folder.
Compile and run:
javac HelloWorld.java
java HelloWorld
Building & Running Locally

If you prefer building the Docker image locally instead of using the CI pipeline, use:

# Build the Docker image
docker build -t <your-dockerhub-username>/hello-world:local .

# Run a container from the image
docker run --rm <your-dockerhub-username>/hello-world:local
The --rm flag removes the container automatically after it exits.

Automated Build & Push with GitHub Actions

Commit and Push any changes to the master (or main) branch:
git add .
git commit -m "Automate Docker build and push"
git push origin master
or do this via your IDE’s built-in Git functionality.
GitHub Actions will automatically run the Build and Push to Docker Hub workflow.
If successful, your Docker image will appear in your Docker Hub repository.
Pulling & Running from Docker Hub

Once the workflow completes, you can pull and run the image anywhere Docker is installed:

# Pull the Docker image from Docker Hub
docker pull <your-dockerhub-username>/hello-world:<tag>

# Run the container
