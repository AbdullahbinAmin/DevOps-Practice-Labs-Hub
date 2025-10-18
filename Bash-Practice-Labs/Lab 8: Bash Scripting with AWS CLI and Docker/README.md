# Lab 8: Bash Scripting with AWS CLI and Docker

* * *

## Objective

In this lab, you will learn how to **combine AWS CLI and Docker** commands inside a **Bash script**.  
You will create a script that:

1.  Launches a new **EC2 instance** using AWS CLI commands.
    
2.  Once the instance is running, automatically connects to it through **SSH**.
    
3.  Runs a **Docker container** inside that EC2 instance.
    

This lab teaches you how to automate AWS and Docker workflows together in one script.

* * *

## Documentation Focus

*   Introduction to **AWS CLI** (Command Line Interface).
    
*   How to combine **AWS operations and Docker commands** in a single Bash script.
    
*   Step-by-step code explanation.
    

* * *

## Prerequisites

Before starting, make sure you have the following:

1.  An **AWS account** with administrator access.
    
2.  **AWS CLI installed and configured** on your system (you must have run `aws configure` and added your access/secret keys).
    
3.  Completed previous Docker and Bash labs.
    
4.  **Docker installed** locally for understanding Docker commands.
    
5.  **A working key pair (.pem file)** to SSH into EC2 instances.
    

* * *

## 1\. Introduction

In the real world, DevOps engineers often automate cloud infrastructure tasks.  
Instead of manually launching an EC2 instance from the AWS Console, you can use a **Bash script** with the **AWS CLI** to:

*   Create and manage instances.
    
*   Connect to them automatically.
    
*   Run Docker containers inside them.
    

This saves time, avoids human errors, and enables repeatable, automated setups.

* * *

## 2\. Script Implementation

Here’s the full Bash script that integrates AWS CLI and Docker:

```bash
#!/bin/bash

# ------------------------------
# AWS and Docker Integration Script
# ------------------------------

# Define AWS EC2 instance details
aws_region="your_aws_region"
instance_type="t2.micro"
ami_id="your_ami_id"
key_name="your_key_name"
security_group_ids="sg-xxxxxxxxxxxxxxxxx"   # Replace with your security group ID
subnet_id="subnet-xxxxxxxxxxxxxxxxx"        # Replace with your subnet ID

# Define Docker container details
docker_image="ubuntu:latest"
docker_command="docker run -it --rm $docker_image /bin/bash"

# Function to launch an EC2 instance
launch_instance() {
    echo "Launching EC2 instance..."

    # Launch EC2 instance using AWS CLI
    instance_id=$(aws ec2 run-instances \
        --region "$aws_region" \
        --instance-type "$instance_type" \
        --image-id "$ami_id" \
        --key-name "$key_name" \
        --security-group-ids "$security_group_ids" \
        --subnet-id "$subnet_id" \
        --query "Instances[0].InstanceId" \
        --output text)

    echo "Instance is being created with ID: $instance_id"

    # Wait until the instance is running
    aws ec2 wait instance-running --instance-ids "$instance_id" --region "$aws_region"

    # Retrieve public DNS of the running instance
    public_dns=$(aws ec2 describe-instances \
        --region "$aws_region" \
        --instance-ids "$instance_id" \
        --query "Reservations[0].Instances[0].PublicDnsName" \
        --output text)

    echo "EC2 Instance is now running."
    echo "Public DNS: $public_dns"
}

# Function to SSH into EC2 instance and run Docker
ssh_and_run_docker() {
    echo "Waiting for EC2 instance to initialize..."
    sleep 60   # Give the instance a minute to boot fully

    # Get the public DNS again (to ensure it’s updated)
    public_dns=$(aws ec2 describe-instances \
        --region "$aws_region" \
        --filters "Name=instance-state-name,Values=running" "Name=key-name,Values=$key_name" \
        --query "Reservations[0].Instances[0].PublicDnsName" \
        --output text)

    echo "Connecting to EC2 via SSH..."

    # SSH into EC2 instance and run Docker command
    ssh -i "your_key.pem" ec2-user@"$public_dns" "$docker_command"
}

# Main workflow
main() {
    launch_instance
    ssh_and_run_docker
}

# Execute the script
main
```

* * *

## 3\. Step-by-Step Explanation of the Script

Let’s break down the script line by line so you understand what each part does.

* * *

### Header

```bash
#!/bin/bash
```

This tells the system that the script should be executed using the **Bash shell**.

* * *

### Defining AWS Variables

```bash
aws_region="your_aws_region"
instance_type="t2.micro"
ami_id="your_ami_id"
key_name="your_key_name"
security_group_ids="sg-xxxxxxxxxxxxxxxxx"
subnet_id="subnet-xxxxxxxxxxxxxxxxx"
```

*   These variables store EC2 configuration values.
    
*   You must replace them with your actual AWS details.
    
    *   Example:
        
        *   `aws_region="us-east-1"`
            
        *   `ami_id="ami-0c55b159cbfafe1f0"` (for Ubuntu)
            
        *   `key_name="my-ec2-key"`
            

* * *

### Defining Docker Variables

```bash
docker_image="ubuntu:latest"
docker_command="docker run -it --rm $docker_image /bin/bash"
```

*   `docker_image` specifies which image to run. Here, it’s the official **Ubuntu** image.
    
*   `docker_command` defines what to do when connected: run a container interactively (`-it`) and remove it when done (`--rm`).
    

* * *

### Function 1: Launch EC2 Instance

```bash
launch_instance() { ... }
```

This function launches an EC2 instance using the **AWS CLI**.

Inside the function:

```bash
instance_id=$(aws ec2 run-instances \
    --region "$aws_region" \
    --instance-type "$instance_type" \
    --image-id "$ami_id" \
    --key-name "$key_name" \
    --security-group-ids "$security_group_ids" \
    --subnet-id "$subnet_id" \
    --query "Instances[0].InstanceId" \
    --output text)
```

*   `aws ec2 run-instances`: Launches a new instance.
    
*   `--region`: Specifies the AWS region.
    
*   `--image-id`: Uses the AMI to define the operating system.
    
*   `--key-name`: Specifies your SSH key pair.
    
*   `--query`: Extracts only the instance ID from the JSON output.
    
*   The result is stored in the variable `instance_id`.
    

Then:

```bash
aws ec2 wait instance-running --instance-ids "$instance_id" --region "$aws_region"
```

This command pauses the script until the instance is fully running.

Next:

```bash
public_dns=$(aws ec2 describe-instances \
    --region "$aws_region" \
    --instance-ids "$instance_id" \
    --query "Reservations[0].Instances[0].PublicDnsName" \
    --output text)
```

This retrieves the **public DNS** name so you can connect via SSH later.

* * *

### Function 2: SSH and Run Docker

```bash
ssh_and_run_docker() { ... }
```

This function connects to the EC2 instance and runs the Docker container.

```bash
sleep 60
```

This waits one minute to allow the instance to finish booting.

```bash
ssh -i "your_key.pem" ec2-user@"$public_dns" "$docker_command"
```

*   `-i "your_key.pem"`: Specifies your SSH key file.
    
*   `ec2-user@`: Default username for Amazon Linux (use `ubuntu@` if using Ubuntu).
    
*   `"$public_dns"`: The EC2’s public address.
    
*   `"$docker_command"`: Runs the Docker command defined earlier.
    

* * *

### Main Workflow

```bash
main() {
    launch_instance
    ssh_and_run_docker
}

main
```

*   The `main` function calls both earlier functions sequentially.
    
*   When the script runs, it first launches the instance, then connects and runs Docker.
    

* * *

## 4\. Running the Script

Follow these steps carefully:

### Step 1: Save the Script

Create a file named:

```bash
nano aws_docker_integration.sh
```

Paste the script inside and save it.

* * *

### Step 2: Make It Executable

```bash
chmod +x aws_docker_integration.sh
```

This gives the script permission to run.

* * *

### Step 3: Execute the Script

```bash
./aws_docker_integration.sh
```

The script will:

1.  Launch a new EC2 instance.
    
2.  Wait for it to become active.
    
3.  SSH into it automatically.
    
4.  Run a Docker container inside.
    

* * *

## 5\. Expected Output

When executed successfully, you’ll see messages like:

```bash
Launching EC2 instance...
Instance launched with ID: i-0abcd1234ef56789
Waiting for instance to be ready...
EC2 Instance is now running.
Public DNS: ec2-12-34-56-78.compute-1.amazonaws.com
Connecting to EC2 via SSH...
Docker container running successfully inside EC2!
```

* * *

## 6\. Conclusion

In this lab, you learned how to:

*   Use the **AWS CLI** to launch and manage EC2 instances.
    
*   Connect automatically to your instance using **SSH**.
    
*   Run **Docker containers** inside EC2 directly through a **Bash script**.
    

This approach is extremely powerful for automating **cloud operations**, **DevOps pipelines**, and **infrastructure setup**.  
With this knowledge, you can extend the script further — for example:

*   Automate Docker image builds and pushes to ECR.
    
*   Install dependencies remotely after instance creation.
    
*   Terminate instances automatically after workloads finish.
