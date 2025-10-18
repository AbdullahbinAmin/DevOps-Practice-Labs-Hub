# Lab 1: Overview of EC2 and Docker Installation on EC2

* * *

## Objective

In this lab, you will:

1.  Understand what **Amazon EC2 (Elastic Compute Cloud)** is and how it works.
    
2.  Learn key EC2 concepts such as instances, AMIs, regions, and security groups.
    
3.  Install **Docker** on an EC2 instance using step-by-step Bash commands.
    

* * *

## 1\. Introduction to Amazon EC2

**Amazon EC2 (Elastic Compute Cloud)** is one of the core services provided by **Amazon Web Services (AWS)**. It allows you to rent virtual machines (called **instances**) in the cloud to run your applications.

Instead of buying physical servers, you can use EC2 instances on-demand, scale them up or down, and pay only for the time you use them.

* * *

## 2\. Key EC2 Concepts

Let’s understand the most important EC2 terms step by step:

### 1\. Instances

An **instance** is a virtual server that you can create and run in the AWS cloud.  
You can choose different instance types depending on what you want to do — for example:

*   **t2.micro** for lightweight applications or testing.
    
*   **m5.large** for medium workloads.
    
*   **c5.xlarge** for high-performance computing.
    

Each instance type has a specific amount of CPU, memory (RAM), storage, and network performance.

* * *

### 2\. AMI (Amazon Machine Image)

An **AMI** is a pre-configured image that contains:

*   An operating system (like Ubuntu, Amazon Linux, or Windows Server).
    
*   Optional pre-installed software (like web servers or development tools).
    

When you **launch an instance**, you select an AMI as its base template.

* * *

### 3\. Instance Types

EC2 provides different types of instances to fit different needs:

*   **General Purpose** (e.g., t2.micro, t3.medium): for balanced workloads.
    
*   **Compute Optimized** (e.g., c5.large): for high CPU-intensive tasks.
    
*   **Memory Optimized** (e.g., r5.large): for applications needing more RAM.
    
*   **Storage Optimized** (e.g., i3.large): for heavy read/write operations.
    

* * *

### 4\. Regions and Availability Zones (AZs)

AWS data centers are located all over the world.

*   A **Region** is a physical location (e.g., _us-east-1, ap-south-1_).
    
*   Each region has multiple **Availability Zones (AZs)** — separate data centers that ensure **high availability**.
    

Example:  
If one data center fails, your application can still run in another AZ within the same region.

* * *

### 5\. Security Groups

A **security group** works like a firewall.  
It controls:

*   **Inbound traffic** (who can connect to your instance).
    
*   **Outbound traffic** (where your instance can connect).
    

Example:  
You can allow SSH access (port 22) for administrators or HTTP (port 80) for web traffic.

* * *

### 6\. Elastic IP Address

An **Elastic IP** is a **permanent public IP address** that you can assign to an instance.  
Even if your instance stops or restarts, the IP remains the same.

* * *

### 7\. EBS (Elastic Block Store)

**EBS** provides **block storage** for EC2 instances — similar to a hard drive.  
It stores your data even if the instance is stopped or terminated.

* * *

## 3\. Benefits and Use Cases of EC2

| Benefit | Description |
| --- | --- |
| Scalability | You can increase or decrease resources anytime. |
| Flexibility | Choose instance types, OS, and storage according to needs. |
| Cost Control | Pay only for what you use (On-demand, Reserved, or Spot Instances). |
| High Availability | Deploy across multiple AZs for reliability. |
| Diverse Use Cases | Use EC2 for web hosting, app testing, data analysis, or development. |

* * *

## 4\. Getting Started with EC2

Here’s a short overview of how you use EC2:

1.  **Launch an Instance**
    
    *   Choose an **AMI** (e.g., Ubuntu).
        
    *   Select an **Instance Type** (e.g., t2.micro).
        
    *   Configure **network settings** and **security groups**.
        
    *   Launch the instance.
        
2.  **Connect to the Instance**
    
    *   Use SSH from your terminal or the AWS Console to access your EC2 instance.
        
3.  **Manage Your Instance**
    
    *   You can start, stop, reboot, or terminate it anytime using:
        
        *   AWS Management Console
            
        *   AWS CLI
            
        *   AWS SDKs
            
4.  **Monitor Your Instance**
    
    *   Use **Amazon CloudWatch** to monitor CPU, memory, and network usage.
        
5.  **Load Balancing and Auto Scaling**
    
    *   Use **Elastic Load Balancer (ELB)** to distribute traffic.
        
    *   Use **Auto Scaling** to automatically adjust the number of instances based on demand.
        

* * *

## 5\. Installing Docker on EC2

Once your EC2 instance is running (preferably Ubuntu-based), follow these steps to install Docker.

### Step 1: Update Packages

```bash
sudo apt update
```

This command refreshes your package list and ensures you’re installing the latest available versions.

* * *

### Step 2: Install Required Dependencies

```bash
sudo apt install apt-transport-https ca-certificates curl software-properties-common
```

*   `apt-transport-https`: Enables downloading from repositories over HTTPS.
    
*   `ca-certificates`: Ensures secure connections.
    
*   `curl`: Used to download files from the internet.
    
*   `software-properties-common`: Helps manage additional repositories.
    

* * *

### Step 3: Create Directory for Docker GPG Key

```bash
sudo install -m 0755 -d /etc/apt/keyrings
```

This creates a secure directory where Docker’s official GPG key will be stored.  
The permission **0755** allows the owner full access and others read and execute rights.

* * *

### Step 4: Add Docker’s Official GPG Key

```bash
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | \
  sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
```

*   `curl -fsSL`: Downloads the GPG key from Docker’s official site.
    
*   `gpg --dearmor`: Converts the key to a format APT can read.
    
*   The output file `/etc/apt/keyrings/docker.gpg` stores the verified key securely.
    

* * *

### Step 5: Set Proper File Permissions

```bash
sudo chmod a+r /etc/apt/keyrings/docker.gpg
```

This gives all users **read access** to the Docker GPG key.

* * *

### Step 6: Add the Docker Repository

```bash
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] \
  https://download.docker.com/linux/ubuntu $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

This command adds Docker’s **official repository** to your system sources:

*   `dpkg --print-architecture`: Detects your system’s architecture (e.g., amd64).
    
*   `os-release`: Gets your Ubuntu version (like jammy or focal).
    
*   The repository URL tells APT where to find Docker packages.
    

* * *

### Step 7: Update Packages Again

```bash
sudo apt update
```

This ensures APT can now access the newly added Docker repository.

* * *

### Step 8: Install Docker

```bash
sudo apt install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin -y
```

This installs:

*   **docker-ce**: Docker Community Edition (main engine).
    
*   **docker-ce-cli**: Command Line Interface for Docker.
    
*   **containerd.io**: Manages running containers.
    
*   **docker-buildx-plugin**: For advanced image building.
    
*   **docker-compose-plugin**: For managing multi-container applications.
    

The `-y` flag automatically confirms all installation prompts.

* * *

## 6\. Verify Docker Installation

After installation, check if Docker is installed and running:

```bash
docker --version
```

This will show the installed Docker version.

To verify that Docker is running properly:

```bash
sudo systemctl status docker
```

If you see “**active (running)**,” Docker is successfully installed.

* * *

## 7\. Conclusion

In this lab, we learned:

*   What **EC2** is and how it provides scalable cloud computing.
    
*   Key EC2 concepts like instances, AMIs, regions, and security groups.
    
*   How to **install Docker** step by step on an EC2 instance.
    

You are now ready to use Docker on AWS EC2 to run and manage your containers in the cloud environment.
