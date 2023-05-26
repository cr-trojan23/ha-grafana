# Deploying Highly Available Grafana Service on ECS with Load Balancing

This CloudFormation template allows you to deploy a highly available Grafana service on Amazon ECS (Elastic Container Service) with load balancing. The template creates necessary resources such as VPC, subnets, security groups, ECS cluster, task definition, service, load balancer, and target group.

## Prerequisites

Before deploying the template, ensure that you have the following prerequisites in place:

- An AWS account with appropriate permissions to create the required resources.
- Basic knowledge of Amazon ECS, Amazon VPC, and Elastic Load Balancing.

## CloudFormation Template

The provided CloudFormation template automates the deployment process by creating all the necessary AWS resources. Here's a brief overview of the key components:

1. **VPC and Subnets:** A new VPC is created along with two subnets for high availability across different availability zones.

2. **Internet Gateway and Route:** An internet gateway is attached to the VPC, allowing communication with the internet. A route is configured to enable internet access for the subnets.

3. **Security Groups:** Two security groups are created - one for the Grafana container and another for the load balancer. These security groups define the inbound traffic rules to allow access to the Grafana service and load balancer.

4. **ECS Cluster and Task Definition:** An ECS cluster is provisioned to manage the Grafana service. A task definition is defined for the Grafana container, specifying the container image, resource requirements, and log configurations.

5. **ECS Service and Load Balancer:** An ECS service is created to manage the Grafana tasks, ensuring high availability and automatic scaling. A load balancer is configured to distribute traffic across the Grafana containers.

6. **Target Group and Listener:** A target group is defined to route requests to the Grafana containers. An Elastic Load Balancer (ELB) listener is set up to listen on port 80 and forward incoming requests to the target group.

By deploying this CloudFormation template, you can quickly set up a highly available Grafana service with automatic load balancing, allowing you to monitor and visualize your data efficiently.

## Deployment Steps

Follow these steps to deploy the Grafana service using the CloudFormation template:

1. Sign in to the AWS Management Console.
2. Open the CloudFormation service.
3. Click on "Create stack" or "Create new stack" to start the stack creation process.
4. In the "Specify template" section, select "Upload a template file" and upload the CloudFormation template file or copy-paste its contents into the editor.
5. Click "Next" to proceed to the stack configuration.
6. Provide a stack name and fill in any required parameters as necessary.
7. Review the configuration details and click "Next."
8. Optionally, you can add tags for the stack resources in the "Tags" section.
9. Click "Next" to proceed to the final step.
10. In the "Configure stack options" section, you can modify advanced settings if needed.
11. Click "Next" and review the stack details.
12. Finally, click "Create stack" to start the deployment process.

## Stack Resources

The CloudFormation template creates the following resources:

- **VPC:** Amazon Virtual Private Cloud (VPC) to isolate the Grafana service.
- **Subnet1, Subnet2:** Two subnets within the VPC for high availability and fault tolerance.
- **RouteTable, Route:** Route table and route to enable communication between the subnets and the internet.
- **SubnetRouteTableAssociation1, SubnetRouteTableAssociation2:** Associations between the subnets and the route table.
- **VPCGatewayAttachment, InternetGateway:** Attachments and configuration for internet access.
- **GrafanaContainerSecurityGroup:** Security group for the Grafana container, allowing inbound traffic on port 3000.
- **NetworkInterface1, NetworkInterface2:** Network interfaces for ECS Fargate tasks in the subnets.
- **Cluster:** ECS cluster for managing the Grafana service.
- **GrafanaLogGroup:** CloudWatch Logs log group for Grafana container logs.
- **ECSTaskExecutionRole:** IAM role for ECS task execution with necessary permissions.
- **GrafanaTaskDefinition:** ECS task definition for the Grafana container.
- **GrafanaService:** ECS service for managing the Grafana tasks.
- **LBSecurityGroup:** Security group for the load balancer, allowing inbound traffic on port 80.
- **LB:** Elastic Load Balancer (ELB) to distribute traffic to Grafana containers.
- **LBListener:** ELB listener to listen on port 80 and forward requests to the target group.
- **TargetGroup:** ELB target group for routing requests to the Grafana containers.

## Customization

You can customize the deployment by modifying the CloudFormation template:

- Adjust the VPC configuration, such as the CIDR block and subnet IP ranges, based on your requirements.
- Modify the security group rules to allow specific inbound traffic as per your needs.
- Change the CPU and memory settings for the Grafana task definition to match the desired resource allocation.
- Update the desired count of Grafana containers in the ECS service for scalability.

## Infrastructure Diagram
![INFRA](https://raw.githubusercontent.com/cr-trojan23/ha-grafana/main/ha-grafana.png)
---

# Highly Available Grafana Deployment with Docker and Nginx

This guide outlines the steps to deploy a highly available Grafana service using Docker containers and Nginx as a reverse proxy. By using this approach, you can achieve scalability, load balancing, and increased availability for your Grafana instances.

## Prerequisites

Make sure you have the following prerequisites installed on your system:

- Docker: [Install Docker](https://docs.docker.com/get-docker/)
- Docker Compose: [Install Docker Compose](https://docs.docker.com/compose/install/)

## Architecture

The deployment architecture consists of three main components:

1. **Nginx Proxy**: Acts as a reverse proxy to distribute traffic to multiple Grafana instances. It runs on port 80 and forwards requests to Grafana instances.
2. **Grafana Database**: Uses PostgreSQL as the database for Grafana. It ensures data persistence and can be scaled separately if needed.
3. **Grafana Instances**: Multiple Grafana instances running in separate Docker containers. They share the same configuration and plugin directories mounted from the host machine.

## Deployment Steps

Follow these steps to deploy the highly available Grafana service using Docker and Nginx:

1. Create a directory for your project and navigate to it.
2. Create a new file named `docker-compose.yml` and copy the provided YAML code into it.
3. Create two additional directories: `conf` and `plugins` in the same project directory.
4. Create an Nginx configuration file named `nginx.conf` in the `conf` directory. Customize it if needed.
5. Create a Grafana configuration file named `grafana.ini` in the `conf` directory. Customize it as per your requirements.
6. Start the deployment by running the following command in the project directory:

```
docker-compose up -d
```
This command will start the Nginx proxy, Grafana database, and two Grafana instances.

7. Access Grafana by opening a web browser and navigating to `http://localhost`. You should see the Grafana login page.
   - Username: admin
   - Password: admin

## Scaling the Deployment

To scale the Grafana instances or the Grafana database, you can adjust the number of instances or replicas in the `docker-compose.yml` file. For example, to have three Grafana instances, you can add the following service definition:

```yaml
grafana3:
  restart: always
  container_name: grafana3
  image: grafana/grafana
  networks:
    - monitor
  expose:
    - 3000
  volumes:
    - ./plugins:/var/lib/grafana/plugins
    - ./conf/grafana.ini:/etc/grafana/grafana.ini
  depends_on:
    - grafana-db
```
After adding the service definition, run `docker-compose up -d` to apply the changes and scale the deployment.

## Customization

- **Nginx Configuration**: Customize the Nginx configuration file `nginx.conf` to meet your specific requirements, such as SSL/TLS termination or custom routing rules.
- **Grafana Configuration**: Modify the Grafana configuration file `grafana.ini` to configure Grafana settings, data sources, authentication options, etc.
- **Plugins**: Add any custom plugins or extensions to the `plugins` directory. They will be automatically mounted into Grafana containers.

## Cleanup

To stop and remove the deployment, run the following command in the project directory:

```
docker-compose down
```

This will stop and remove all containers.

