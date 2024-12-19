# Test01 Project

This project demonstrates the integration of Python, Docker, Jenkins, and Ansible to streamline the development and deployment of applications. It serves as a modern template for implementing DevOps best practices, emphasizing automation, scalability, and reliability.

---

## Table of Contents

1. [Project Overview](#project-overview)
2. [Features](#features)
3. [Project Structure](#project-structure)
4. [Setup and Installation](#setup-and-installation)
5. [Continuous Integration with Jenkins](#continuous-integration-with-jenkins)
6. [Configuration Management with Ansible](#configuration-management-with-ansible)



## Project Overview

This repository provides a comprehensive framework for employing DevOps tools to optimize software development and deployment workflows. By leveraging containerization, automation, and CI/CD pipelines, this project enhances both reliability and reproducibility.



## Features

- **Dockerized Application**: Ensures compatibility across different environments and platforms.
- **Automated CI/CD Pipeline**: Utilizes Jenkins to build, test, and deploy applications efficiently.
- **Infrastructure as Code (IaC)**: Employs Ansible to streamline environment setup and deployment.
- **Customizable Templates**: Offers flexibility for adapting to various project requirements.
- **Automatic Rebuild**: Jenkins pipeline automatically triggers rebuilds whenever new code is pushed to the GitHub repository.
- **Automatic Email Notifications**: Automatically sends the status of the build to the system administrator using the `Sendmail` package.



## Project Structure

```
test01/
├── app.py              # Core Python application file
├── Dockerfile          # Configuration for Docker image creation
├── jenkinsfile         # Script defining Jenkins pipeline stages
├── playbook.yml        # Ansible playbook for automating deployments
├── requirements.txt    # Python library dependencies
├── inventory           # List of target hosts for Ansible
└── templates/          # Template files used by Ansible for configuration
```

## Setup and Installation
Follow the steps below to set up and run this project locally or on your server.

1. **Clone the Repository**: Start by cloning the GitHub repository to your local machine or server
```
git clone https://github.com/yatara21/Final-Project-ODC.git
cd Final-Project-ODC
```
2. **Install Required Software**: Ensure the following tools are installed on your system:
   - **Docker**: For building and running the application in a container.
   - **Ansible**: For automating the deployment and configuration on web servers.
   - **Python 3.x**: Required to run the application.
   - **Jenkins**: To automate the CI/CD pipeline (if you want to use Jenkins for integration).
   - **Sendmail**: To send the status of your Build. 
   - **Ngrok**: To expose your Jenkins server to the Public Internet.

3. **Install Python Dependencies**: This project requires certain Python libraries. Install them using the `requirements.txt` file:
```
pip install -r requirements.txt
```

4. **Set Up Docker and Jenkins (Optional)**:

      4.1 **Build the Docker Image**: To build the Docker image for the application
   ```
   docker build -t yatara21/final-project-odc .
   ```
      4.2 **Run the Docker Container**: To run the application in a Docker container on your local machine
   ```
   docker run -p 5000:5000 yatara21/final-project-odc:latest
   ```
5. **Configure and Run Ansible Playbook**:
   You can use the Ansible playbook to deploy the application on your web servers.
   
      5.1 **Edit Ansible Inventory File**:
   Ensure the `inventory` file contains the correct target web servers where you want to deploy the application.
   
      Example:
   ```
   [webservers]
   your-server-ip
   ```
      5.2 **Run the Playbook**:
   Run the Ansible playbook to install Docker, pull the latest Docker image, and deploy the container:
   ```
   ansible-playbook -i inventory playbook.yml
   ```
   This will ensure that the required dependencies are installed on your web server, and the Docker container is running with the latest image.

6. **Set Up Jenkins (Optional)**: If you want to automate the build, test, and deployment process using Jenkins, follow these steps:

   - Create a new Jenkins Pipeline.
   
   - Add the `Jenkinsfile` from the project repository.

   - Set up the necessary credentials in Jenkins for GitHub and Docker Hub (as specified in the `Jenkinsfile`).

   - Run the Jenkins Pipeline to automatically fetch the code, build the Docker image, test, push to Docker Hub, and deploy to the web servers.

   - Access the Application

   After the deployment is complete, the application should be accessible at `http://<your-web-server-ip>:5000`.

## Continuous Integration with Jenkins

The Jenkins integration automates the CI/CD process for building, and deploying applications. The pipeline, defined in the `jenkinsfile`, handles stages like code fetching, Docker image building, logging into Docker Hub, pushing the image, and deploying the application to web servers using Ansible.

1. **Fetch from GitHub**: 

   This stage pulls the latest code from the specified GitHub repository using the   credentials defined in the environment variables.

```groovy
stage('Fetch from GitHub') {
    steps {
        git branch: 'main', 
            url: "${GITHUB_REPO}", 
            credentialsId: "${GITHUB_CREDENTIALS}"
    }
}
```


2. **Build Docker Image**: 

   This stage builds the Docker image based on the code fetched from the repository.

```groovy
stage('Build Docker Image') {
    steps {
        sh 'docker build -t $DOCKER_IMAGE:$DOCKER_TAG .'
    }
}
```
- **Purpose**: Builds a Docker image from the Dockerfile in the repository.
- **Explanation**: The `docker build` command uses the Dockerfile in the current directory (`.`) and tags the image with the specified `$DOCKER_IMAGE:$DOCKER_TAG.`


3. **Login to Docker Hub**: 

   This stage logs into Docker Hub using credentials stored in Jenkins.

```groovy
stage('Login to Docker Hub') {
    steps {
        script {
            withCredentials([usernamePassword(
                credentialsId: "${DOCKER_HUB_CREDENTIALS}", 
                usernameVariable: 'DOCKER_USER', 
                passwordVariable: 'DOCKER_PASS'
            )]) {
                sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
            }
        }
    }
}

```
- **Purpose**: Authenticates Jenkins with Docker Hub to allow pushing the Docker image to a repository.
- **Explanation**: Uses the `withCredentials` block to securely inject Docker Hub credentials into the environment, which are then used to log in using `docker login`.

4. **Push Docker Image to Docker Hub**: 

   This stage tags the Docker image and pushes it to Docker Hub.

```groovy
stage('Push Docker Image to Docker Hub') {
    steps {
        sh 'docker tag $DOCKER_IMAGE:$DOCKER_TAG $DOCKER_HUB_REPO:$DOCKER_TAG'
        sh 'docker push $DOCKER_HUB_REPO:$DOCKER_TAG'
    }
}
```
- **Purpose**: Tags the built Docker image and uploads it to the Docker Hub repository.
- **Explanation**: First, the image is tagged with the `DOCKER_HUB_REPO:$DOCKER_TAG`, and then it is pushed to the repository using the `docker push` command.

5. **Install Docker on Webservers**: 

   This stage uses Ansible to install Docker and deploy the application on the web servers.

```groovy
stage('Install Docker on webservers') {
    steps {
        script {
            sh "ansible-playbook -i $ANSIBLE_INVENTORY $ANSIBLE_PLAYBOOK"
        }
    }
}
```
- **Purpose**: Deploys the Docker container to remote web servers using Ansible.
- **Explanation**: Runs the Ansible playbook (`playbook.yml`) on the target web servers listed in the `inventory file`. The playbook ensures that Docker is installed and the latest container is deployed.

6. **Post Build Actions**: 

   This block sends an email notification with the status of the Jenkins build.

```groovy
post {
    always {
        emailext(
            subject: "Jenkins Build Status: ${currentBuild.result}",
            body: "The Jenkins pipeline build has completed with status: ${currentBuild.result}.\n\nBuild URL: ${env.BUILD_URL}",
            to: "${EMAIL_RECIPIENT}"
        )
    }
}
```
- **Purpose**: Sends an email notification to the recipient regardless of whether the build succeeds or fails.
- **Explanation**: The `emailext` function sends an email with the build result and a link to the build URL. The email is sent to the address specified in `EMAIL_RECIPIENT`.

### Summary of Each Stage:

1. **Post Build Actions**: Fetches the latest code from the specified GitHub repository.
2. **Build Docker Image**: Builds a Docker image from the fetched code.
3. **Login to Docker Hub**: Logs into Docker Hub using provided credentials.
4. **Push Docker Image to Docker Hub**: Tags and pushes the Docker image to Docker Hub.
5. **Install Docker on Webservers**: Deploys the application to web servers using Ansible.
6. **Post Build Actions**: Sends an email notification with the status of the build.




## Configuration Management with Ansible

This playbook automates the deployment of a Dockerized Python application on web servers. It ensures required dependencies and Docker are installed, stops and removes any existing containers, pulls the latest Docker image from Docker Hub, and starts a new container.
```groovy
---
- name: Deploy Docker Application on webservers
  hosts: webservers
  become: yes

  tasks:
    - name: Install dependencies and Docker
      yum:
        name:
          - yum-utils
          - device-mapper-persistent-data
          - lvm2
          - python3
          - python3-pip
          - docker-ce
        state: present

    - name: Ensure Docker service is running
      service:
        name: docker
        state: started
        enabled: yes

    - name: Manage existing Docker container
      block:
        - name: Stop and remove container if running
          docker_container:
            name: "my-python-app"
            state: absent
          when: container_info.container is defined

    - name: Pull latest Docker image
      docker_image:
        name: "your-username/your-repo"
        tag: "latest"
        source: pull

    - name: Run Docker container
      docker_container:
        name: "my-python-app"
        image: "your-username/your-repo:latest"
        state: started
        restart_policy: always
        ports:
          - "5000:5000"
        detach: yes
```
### Summary of Key Tasks:

- **Install Dependencies**: Installs required packages and Docker.
- **Ensure Docker Service**: Starts and enables Docker.
- **Manage Containers**: Stops and removes any existing container.
- **Pull Docker Image**: Fetches the latest image from Docker Hub.
- **Run Docker Container**: Deploys the updated container with the application. 

### Running the Playbook

Execute the following command to deploy the application:

```bash
ansible-playbook -i inventory playbook.yml
```

This command will:

- Provision the required infrastructure.
- Configure dependencies and runtime environments.
- Deploy the latest version of the application.

### *Monitoring and Debugging*

- Use the `-vvv` flag for verbose output when running playbooks:
  ```bash
  ansible-playbook -i inventory playbook.yml -vvv
  ```
- Check logs for detailed error messages and task execution details.

