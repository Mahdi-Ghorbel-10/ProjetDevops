# DevOps Project 🚀

## 📑 Table of Contents
- [About the Project](#about-the-project)
- [Features](#features)
- [Project Structure](#project-structure)
- [Technologies Used](#technologies-used)
- [Setup & Installation](#setup--installation)
- [Advanced DevOps Pipeline](#advanced-devops-pipeline)
- [Reports & Notifications](#reports--notifications)
- [Monitoring & Security](#monitoring--security)
- [License](#license)

## 📝 About the Project
This project is a CI/CD pipeline designed for robust and secure application deployment using modern DevOps practices. It integrates continuous integration, delivery, monitoring, and security scanning tools to automate deployment and improve software quality and security.

## ✨ Features
- Automated CI/CD Pipeline using Jenkins
- Quality & Security Scanning with SonarQube, OWASP Dependency-Check, Lynis, and Falco
- Containerization with Docker and Docker Compose
- Dependency Management with Nexus repository
- Monitoring with Prometheus and Grafana
- Fault Injection Testing with Pumba
- Twilio & Email Notifications for build events

## 🗂 Project Structure
├── Jenkinsfile-advanced      # Advanced DevOps Pipeline script
├── src/                      # Source code directory
├── Dockerfile                # Docker setup for the application
├── docker-compose.yml        # Docker Compose file for multi-service setup
├── prometheus-config/        # Configuration files for Prometheus
└── README.md                 # Project documentation

## 🛠 Technologies Used

This project leverages a range of powerful tools to automate deployment, enhance security, and monitor system performance:

- **CI/CD Pipeline**:  
  - **Jenkins** – Orchestrates the continuous integration and delivery pipeline.

- **Code Quality & Security**:  
  - **SonarQube** – Analyzes code for quality, vulnerabilities, and potential bugs.  
  - **OWASP Dependency-Check** – Scans project dependencies for known security vulnerabilities.  
  - **Lynis** – Performs security audits and hardening checks on servers.  

- **Containerization**:  
  - **Docker** – Packages the application into containers for portability and scalability.  
  - **Docker Compose** – Defines and runs multi-container Docker applications.

- **Dependency Management**:  
  - **Nexus** – Manages and stores project dependencies and build artifacts.

- **Monitoring & Visualization**:  
  - **Prometheus** – Collects and stores metrics from the containers and services.  
  - **Grafana** – Visualizes the collected metrics for insightful monitoring and alerting.

- **Security Monitoring**:  
  - **Falco** – Monitors runtime behavior and detects abnormal or suspicious activity.  
  - **Pumba** – Simulates network failures and container crashes for resilience testing.

- **Programming Language**:  
  - **Java** (Spring Boot) – The backend application is built using the Spring Boot framework.

---

## 🛠 Setup & Installation

### Prerequisites
Ensure you have the following tools installed:

- **Java Development Kit (JDK)** – Required to build and run the Spring Boot application.
- **Docker** – For containerizing the application and running the environment.
- **Jenkins** – To manage the CI/CD pipeline.
- **Prometheus** – For collecting and monitoring application metrics.
- **Grafana** – For visualizing metrics and monitoring system performance.

### Installation Steps

1. **Clone the repository**:
    ```bash
    git clone https://github.com/RayenBal/DevOps-Project.git
    cd DevOps-Project
    ```

2. **Run Docker Compose** to set up the environment:
    ```bash
    docker-compose up -d
    ```

3. **Access Jenkins**:
    - Open Jenkins in your browser by navigating to `http://localhost:8080`.
    - Set up the pipeline using the provided `Jenkinsfile-advanced` to run the CI/CD pipeline.

---

## 🔄 Advanced DevOps Pipeline

The pipeline automates the full software lifecycle, including code quality checks, security scans, and monitoring:

1. **Git Checkout**: Clones the repository and checks out the latest branch.
2. **Compile Project**: Executes `mvn clean compile` to compile the codebase.
3. **Unit Tests**: Runs unit tests and collects results.
4. **JaCoCo Coverage Report**: Generates a code coverage report.
5. **SonarQube Analysis**: Performs static code analysis for quality and security vulnerabilities.
6. **Dependency Vulnerability Scan**: Uses OWASP Dependency-Check to identify vulnerabilities in dependencies.
7. **Build & Push Docker Image**: Builds a Docker image and pushes it to Docker Hub.
8. **Nexus Deployment**: Deploys build artifacts to Nexus repository.
9. **Deployment with Docker Compose**: Deploys services using Docker Compose.
10. **Container Monitoring**: Prometheus collects metrics, and Grafana visualizes them.
11. **Security Smoke Tests**: Runs security tests to validate the system.
12. **Server Hardening Validation**: Runs Lynis security checks on the server.
13. **Notifications**: 
    - **Twilio SMS**: Sends SMS alerts for build status.
    - **Email**: Sends an email with detailed build reports.

---

## 📊 Reports & Notifications
- **JaCoCo Coverage Report**: Provides detailed insights on code coverage.
- **OWASP Dependency-Check Report**: Lists vulnerabilities found in project dependencies.
- **Lynis Report**: Offers a security validation report for the server.
- **Twilio SMS Alert**: Sends notifications about build completion via SMS.
- **Email Notification**: Sends detailed build results along with links to coverage and security reports.

---

## 📊 Monitoring & Security
- **Prometheus**: Collects and stores metrics from Docker containers and application endpoints.
- **Grafana**: Visualizes Prometheus metrics in real-time for monitoring system health.
- **Falco**: Detects and alerts on abnormal runtime behaviors.
- **Lynis**: Conducts security audits and identifies potential vulnerabilities on the host system.
- **Pumba**: Simulates network issues and container failures for testing system resilience.

---

## 📜 License
This project is licensed under the **MIT License**. See the [LICENSE](LICENSE) file for more details.  
Maintained by [Med Rayen Balghouthi](https://github.com/RayenBal).


