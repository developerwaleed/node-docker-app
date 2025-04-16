# Node.js Docker Hello World

A simple Node.js application containerized with Docker that displays "Hello World" when accessed.

## Prerequisites

- Docker installed on your machine
- Node.js and npm (for local development)

## Building the Docker Image

```bash
docker build -t node-hello-world .
```

## Running the Container

```bash
docker run -p 3000:3000 node-hello-world
```

## Accessing the Application

Once the container is running, you can access the application at:
- http://localhost:3000

## Deploying to AWS

To deploy this application to AWS, you can use services like:
- AWS Elastic Container Service (ECS)
- AWS Elastic Beanstalk
- AWS App Runner

Make sure to:
1. Push your Docker image to Amazon ECR (Elastic Container Registry)
2. Configure the appropriate AWS service to use your container image
3. Set up proper security groups and networking
4. Configure the environment variables if needed 