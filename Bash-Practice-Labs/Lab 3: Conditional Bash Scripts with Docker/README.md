🧠 **Lab 3: Conditional Bash Scripts with Docker**
==================================================

This version uses **easy language**, includes **every step**, explains **each line of code in detail**, and ends with **5 practice tasks** to help learners go beyond the basics.

🎯 **Objective**
----------------

The main goal of this lab is to understand how **conditional statements (if-else)** work in **Bash scripting**, and how they can be used to make **smart decisions** when working with **Docker**.

You will learn:

*   How to use conditions in Bash scripts.
    
*   How to check Docker commands for success or failure.
    
*   How to print user-friendly messages based on outcomes.
    
*   How to pull and run Docker images automatically using Bash.
    

🧩 **Lab Overview**
-------------------

In this lab, we’ll create a **Bash script** that:

1.  Checks if Docker is installed.
    
2.  Pulls the **Nginx** image from Docker Hub.
    
3.  Checks whether the pull command was successful.
    
4.  Runs a container only if the image is available.
    
5.  Confirms that the container is running properly.
    

This script will use **conditional logic** (if, else, and exit) to make decisions.

📜 **Bash Script: lab2\_conditional\_script.sh**
------------------------------------------------

```bash
#!/bin/bash

# Task 1: Check if Docker is installed
echo "Checking if Docker is installed..."
if ! command -v docker &> /dev/null; then
    echo "Docker is not installed. Please install Docker before running this script."
    exit 1
else
    echo "Docker is installed."
fi

# Task 2: Pull Nginx Docker image
echo "Pulling Nginx Docker image..."
docker pull nginx

# Check if image pull was successful
if [ $? -eq 0 ]; then
    echo "Nginx image pulled successfully."
else
    echo "Failed to pull Nginx image. Exiting script."
    exit 1
fi

# Task 3: Check if a container with the same name already exists
if docker ps -a --format '{{.Names}}' | grep -q "^nginx-container$"; then
    echo "A container named 'nginx-container' already exists. Removing it..."
    docker rm -f nginx-container
fi

# Task 4: Run a new Nginx container
echo "Starting a new Nginx container..."
docker run -d -p 8080:80 --name nginx-container nginx

# Task 5: Verify if the container is running
if docker ps | grep -q nginx-container; then
    echo "Nginx container is running successfully."
    echo "You can access it at http://localhost:8080"
else
    echo "Container failed to start."
    exit 1
fi

# End of script
echo "Lab 2 - Conditional Bash Script completed successfully."
```

🧱 **Step-by-Step Explanation (Word-by-Word)**
----------------------------------------------

Let’s go through each part of the script carefully.

### 1\. #!/bin/bash

This line is called a **shebang**.It tells the system that this file should be run using the **Bash shell**.

### 2\. if ! command -v docker &> /dev/null; then

*   command -v docker checks if the **docker** command exists on your system.
    
*   ! means **NOT**, so if Docker **is not found**, the condition becomes true.
    
*   &> /dev/null hides any command output so that only our custom message appears.
    

If Docker is missing, the script shows:

```kotlin   
Docker is not installed. Please install Docker before running this script.
```

and then stops using exit 1.

### 3\. docker pull nginx

This command **downloads the Nginx image** from Docker Hub.If it’s already downloaded, Docker simply uses the existing version.

### 4\. if \[ $? -eq 0 \]; then

*   $? holds the **exit code** of the last command.
    
*   0 means success, any other number means an error.So this line checks whether the image pulled correctly.
    

If successful, it prints:

```arduino
Nginx image pulled successfully.
```

Otherwise, it prints a failure message and exits the script.

### 5\. Checking for existing containers

```bash   
if docker ps -a --format '{{.Names}}' | grep -q "^nginx-container$"; then
```

*   docker ps -a lists all containers (running or stopped).
    
*   \--format '{{.Names}}' shows only container names.
    
*   grep -q searches for the name **nginx-container** quietly (no output).If found, the script removes it:
    

```bash   
docker rm -f nginx-container
```

This ensures there’s no naming conflict before creating a new container.

### 6\. Running the container

```   
docker run -d -p 8080:80 --name nginx-container nginx
```

*   docker run → Start a new container.
    
*   \-d → Detached mode (runs in the background).
    
*   \-p 8080:80 → Maps port 8080 on your system to port 80 inside the container.
    
*   \--name nginx-container → Assigns a specific name.
    
*   nginx → The image to use.
    

After running, you can open your browser and visit:

```arduino
http://localhost:8080
```

You should see the Nginx welcome page.

### 7\. Verifying container status

```bash
if docker ps | grep -q nginx-container; then
```

This checks if the container is currently running.If yes, it prints the success message with the URL.Otherwise, it shows a failure message.

### 8\. exit 1 and exit 0

*   exit 1 → Script ends because of an error.
    
*   exit 0 → (or no exit at the end) means the script finished successfully.
    

⚙️ **How to Run This Script**
-----------------------------

### Step 1: Create a new file

```bash
nano lab2_conditional_script.sh
```

Paste the script into the file, save, and exit.

### Step 2: Make it executable

```bash
chmod +x lab2_conditional_script.sh
```

This gives permission to run the script as a program.

### Step 3: Execute the script

```bash
./lab2_conditional_script.sh
```

You will see messages for each step such as:

```arduino
Checking if Docker is installed...
Pulling Nginx Docker image...
Starting a new Nginx container...
Nginx container is running successfully.
```

🧾 **Summary**
--------------

In this lab, you learned how to:

*   Use **if-else conditions** in Bash scripts.
    
*   Check command success using **$?**.
    
*   Automate Docker image pulling and container running.
    
*   Verify container status programmatically.
    

This kind of script is a **foundation** for real DevOps automation — it helps you create **smart scripts** that act automatically based on conditions.

🧠 **Practice Tasks (To Strengthen Your Skills)**
-------------------------------------------------

Try these tasks after completing the lab:

### **Task 1: Pull a Different Image**

Modify the script to pull and run the **httpd (Apache)** image instead of Nginx.Run it on port **8081** and confirm it’s working at http://localhost:8081.

### **Task 2: Add Image Check**

Add a condition that first checks if the **nginx** image already exists locally using:

```bash
docker images | grep nginx
```

If it exists, skip pulling again.

### **Task 3: Add Cleanup Logic**

Add steps at the end of the script to **stop and remove** the container automatically after a few seconds using:

```bash
sleep 5
docker stop nginx-container
docker rm nginx-container
```

### **Task 4: Add Logging**

Modify the script so that all output (both standard and error messages) is saved to a file named:

```lua 
lab2_conditional_output.log
```

Hint:

```bash
./lab2_conditional_script.sh > lab2_conditional_output.log 2>&1
```

### **Task 5: Display Container Details**

After the container starts, print:

*   The **container ID**
    
*   The **image name**
    
*   The **container’s internal IP address**
    

Use this command for help:

```bash
docker inspect nginx-container
```
