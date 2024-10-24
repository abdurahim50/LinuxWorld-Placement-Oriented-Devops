# TASK 1
## Scenario:
You are part of a DevOps team at a cloud-native company. The company runs multiple containerized microservices in a dynamic infrastructure environment hosted on AWS. As part of improving observability across your infrastructure, you have been tasked to automate the deployment of EC2 instances using Terraform, install Docker on these instances using Ansible, expose Docker metrics, and integrate with Prometheus for monitoring.

### Project Objective: 
Your task is to provision EC2 instances in a scalable manner, ensure Docker is running on these instances, and expose Docker metrics for monitoring. You will use Terraform to handle the infrastructure provisioning and Ansible to install and configure Docker. Prometheus will be configured to scrape Docker metrics to improve observability for the Ops team.

### Key Requirements:

- Automated Infrastructure Setup: Use Terraform to provision EC2 instances dynamically, allowing for future scalability.
- Docker Installation: Use Ansible to automate Docker installation and configuration, ensuring it's ready for production workloads.
- Metrics Exposure: Ensure Docker metrics are exposed for Prometheus scraping, enabling insights into resource usage.
- Monitoring Integration: Integrate with Prometheus for monitoring of Docker containers, providing visibility into container resource usage, health, and performance.
- Security Considerations: Ensure that only necessary ports are exposed and security best practices are followed.

