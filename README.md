# Node.js Docker Hello World

A simple Node.js application containerized with Docker that displays "Hello World" when accessed.

## Prerequisites

- Docker installed on your machine
- Node.js and npm (for local development)
- AWS EC2 instance (for public access)

## Building the Docker Image

```bash
docker build -t node-hello-world .
```

## Running the Container

```bash
docker run -p 3000:3000 node-hello-world
```

## Accessing the Application

### Local Access
Once the container is running, you can access the application at:
- http://localhost:3000

### Public Access (EC2 Instance)

1. **Fix Docker Permission Issues (if needed)**
```bash
# Add your user to the docker group
sudo usermod -aG docker $USER

# Apply the new group membership
newgrp docker
```

2. **Clone and Setup the Application**
```bash
# Clone the repository
git clone <your-repository-url>
cd node-docker-app

# Build the Docker image
docker build -t node-hello-world .

# Run the container in detached mode
docker run -d -p 3000:3000 node-hello-world
```

3. **Verify Local Access**
```bash
# Check if the application is running locally
curl localhost:3000
```

4. **Configure EC2 Security Group**
- Go to AWS Management Console > EC2 > Security Groups
- Select your instance's security group
- Add inbound rule:
  - Type: Custom TCP
  - Port range: 3000
  - Source: 0.0.0.0/0
  - Description: Node.js app access

5. **Access the Application**
- Get your EC2 instance's public IP from AWS console
- Access the application at: `http://<your-ec2-public-ip>:3000`

### Troubleshooting

1. **Check Running Containers**
```bash
# List running containers
docker ps

# Check port listening status
sudo netstat -tulpn | grep 3000
```

2. **Restart Container**
```bash
# Stop all running containers
docker stop $(docker ps -q)

# Run container with explicit port mapping
docker run -d -p 0.0.0.0:3000:3000 node-hello-world
```

3. **Check Firewall Status**
```bash
sudo ufw status
```

setup EC2:

Sure! Here's a consolidated list of all the commands you need to run to install Docker, Git, and Node.js on your EC2 instance.

### **Step-by-Step Commands**

1. **Update package list:**
   ```bash
   sudo apt-get update
   ```

---

### **Install Docker**

2. **Install Docker dependencies:**
   ```bash
   sudo apt-get install apt-transport-https ca-certificates curl software-properties-common -y
   ```

3. **Add Docker's official GPG key:**
   ```bash
   curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
   ```

4. **Add Docker repository:**
   ```bash
   sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
   ```

5. **Update the package list again:**
   ```bash
   sudo apt-get update
   ```

6. **Install Docker:**
   ```bash
   sudo apt-get install docker-ce -y
   ```

7. **Start Docker and enable it to run on boot:**
   ```bash
   sudo systemctl start docker
   sudo systemctl enable docker
   ```

8. **Verify Docker installation:**
   ```bash
   sudo docker --version
   ```

9. **(Optional) Add your user to the Docker group:**
   ```bash
   sudo usermod -aG docker $USER
   ```

---

### **Install Git**

10. **Install Git:**
    ```bash
    sudo apt-get install git -y
    ```

11. **Verify Git installation:**
    ```bash
    git --version
    ```

---

### **Install Node.js**

12. **Install Node.js from NodeSource repository:**
    ```bash
    curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
    ```

13. **Install Node.js and npm:**
    ```bash
    sudo apt-get install -y nodejs
    ```

14. **Verify Node.js and npm installation:**
    ```bash
    node --version
    npm --version
    ```

---

## Continuous Deployment

This project uses GitHub Actions for continuous deployment to EC2. When changes are pushed to the main branch, the following automated process occurs:

1. GitHub Actions connects to your EC2 instance at `ec2-51-20-81-225.eu-north-1.compute.amazonaws.com`
2. Pulls the latest code from the repository
3. Rebuilds and restarts the Docker container

### Required Secrets

You need to add your EC2 .pem key as a secret in your GitHub repository:

1. Go to your GitHub repository
2. Click on "Settings"
3. Click on "Secrets and variables" → "Actions"
4. Click "New repository secret"
5. Name: `EC2_PEM_KEY`
6. Value: Copy the entire contents of your `test_instance.pem` file

### Manual Deployment

If you need to manually deploy the application, use these commands:

```bash
# SSH into your EC2 instance
ssh -i "test_instance.pem" ubuntu@ec2-51-20-81-225.eu-north-1.compute.amazonaws.com

# Once connected, run these commands
cd ~/test_app/node-docker-app
git pull
docker build -t node-hello-world .
docker stop node-app || true
docker rm node-app || true
docker run -d --name node-app -p 3000:3000 node-hello-world
```
