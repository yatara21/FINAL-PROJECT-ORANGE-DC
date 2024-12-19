# Test01 Project

This project demonstrates the integration of Python, Docker, Jenkins, and Ansible to streamline the development and deployment of applications. It serves as a modern template for implementing DevOps best practices, emphasizing automation, scalability, and reliability.

---

## Table of Contents

1. [Project Overview](#project-overview)
2. [Features](#features)
3. [Project Structure](#project-structure)
4. [Prerequisites](#prerequisites)
5. [Setup and Installation](#setup-and-installation)
6. [Continuous Integration with Jenkins](#continuous-integration-with-jenkins)
7. [Configuration Management with Ansible](#configuration-management-with-ansible)
8. [Contributing](#contributing)
9. [License](#license)
10. [Acknowledgments](#acknowledgments)

---

## Project Overview

This repository provides a comprehensive framework for employing DevOps tools to optimize software development and deployment workflows. By leveraging containerization, automation, and CI/CD pipelines, this project enhances both reliability and reproducibility.

---

## Features

- **Dockerized Application**: Ensures compatibility across different environments and platforms.
- **Automated CI/CD Pipeline**: Utilizes Jenkins to build, test, and deploy applications efficiently.
- **Infrastructure as Code (IaC)**: Employs Ansible to streamline environment setup and deployment.
- **Customizable Templates**: Offers flexibility for adapting to various project requirements.

---

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

---

## Prerequisites

Ensure the following tools are installed:

- **Python 3.x**: For running the application.
- **Docker**: To containerize the application.
- **Jenkins**: For executing CI/CD pipelines.
- **Ansible**: For configuration management and deployment.
- **Git**: For version control and repository access.

---

## Setup and Installation

### 1. Clone the Repository

```bash
git clone https://github.com/yatara21/test01.git
cd test01
```

### 2. Install Python Dependencies

```bash
pip install -r requirements.txt
```

### 3. Build the Docker Image

```bash
docker build -t test01-app .
```

### 4. Run the Application

```bash
docker run -p 8000:8000 test01-app
```

The application will be accessible at [http://localhost:8000](http://localhost:8000).

---

## Continuous Integration with Jenkins

The project includes a `jenkinsfile` that defines a CI/CD pipeline with the following stages:

1. **Build Stage**: Compiles the application and creates a Docker image.
2. **Test Stage**: Runs test scripts to validate application functionality.
3. **Deploy Stage**: Uses Ansible to deploy the application to the specified environment.

### Setting Up the Jenkins Pipeline

1. Log in to Jenkins and create a new pipeline project.
2. Configure the pipeline to point to this repository.
3. Run the pipeline to execute the automated workflow.

---

## Configuration Management with Ansible

Ansible is employed to automate the setup of infrastructure and application deployment.

### Key Files

- **`playbook.yml`**: Automates the environment setup and deployment process.
- **`inventory`**: Specifies the target servers or hosts for deployment.

### Running the Playbook

To deploy the application, execute the following command:

```bash
ansible-playbook -i inventory playbook.yml
```

---

## Contributing

We welcome contributions to enhance this project. To get involved:

1. Fork the repository.
2. Create a feature branch:
   ```bash
   git checkout -b feature-name
   ```
3. Implement your changes and commit them:
   ```bash
   git commit -m "Add feature description"
   ```
4. Push your changes to your fork:
   ```bash
   git push origin feature-name
   ```
5. Open a pull request for review.

---

## License

This project is distributed under the MIT License. Refer to the [LICENSE](LICENSE) file for full details.

---

## Acknowledgments

We extend our gratitude to the open-source community for their invaluable contributions and resources that inspired this project.

---

