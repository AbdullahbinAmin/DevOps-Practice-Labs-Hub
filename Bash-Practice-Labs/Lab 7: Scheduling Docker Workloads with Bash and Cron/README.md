# Lab 7: Scheduling Docker Workloads with Bash and Cron

* * *

## Objective

The goal of this lab is to **automate Docker workloads** using a **Bash script** and **Cron jobs**.  
You will create a Dockerized backup tool and then schedule it to run automatically at regular intervals (like every night) using the Cron scheduler.

This helps ensure your Docker data or files are backed up **regularly without manual effort**.

* * *

## Why This Lab Is Important

In real-world DevOps environments, tasks such as **backups, log rotations, and image cleanups** are performed automatically using **Cron jobs**.  
This lab gives you practical experience in automating Docker-related tasks — an essential skill for system administrators and DevOps engineers.

* * *

## Documentation Overview

This lab includes:

*   Understanding **Cron** and how it schedules tasks.
    
*   Writing a **Bash script** that performs Docker-based backups.
    
*   Automating the backup process using **Cron scheduling**.
    

* * *

## Prerequisites

Before starting this lab, make sure you have the following:

1.  ✅ **An AWS account** with administrative access (if you’re working on an EC2 instance).
    
2.  ✅ **Docker Deep Dive Course** — You should already know how to build and run Docker containers.
    
3.  ✅ **Bash Script Deep Dive Course** — You should understand how to write and execute Bash scripts.
    
4.  ✅ **All previous labs completed** — especially the ones covering Docker image creation and user input handling.
    

* * *

## 1\. Introduction

In this lab, you will:

*   Build a **simple Dockerized backup tool** that copies files from one directory to another.
    
*   Write a **Bash script** that runs the backup automatically.
    
*   Use **Cron** to schedule the script so it runs automatically every day.
    

* * *

## 2\. Creating the Dockerized Backup Tool

We’ll first create a **custom Docker image** that performs the backup operation.

* * *

### Step 1: Create a Dockerfile

Open a terminal and create a new file named **Dockerfile** (no file extension):

```bash
nano Dockerfile
```

Now add the following content to it:

```
# Use an official base image
FROM ubuntu:latest

# Create a directory to serve as the source of our backup
RUN mkdir /source
WORKDIR /source

# Create some test files
RUN echo "This is file 1." > file1.txt && echo "This is file 2." > file2.txt

# Create a directory to serve as the backup destination
RUN mkdir /backup

# Copy the files from source to backup
CMD cp -r /source /backup
```

* * *

### Step-by-Step Explanation of the Dockerfile

| Line               | Explanation                                                     |
| ------------------ | --------------------------------------------------------------- |
| FROM ubuntu:latest | Uses the latest version of Ubuntu as the base image. |
| RUN mkdir /source  | Creates a folder named /source where the original files will exist. |
| WORKDIR /source    | Sets /source as the working directory for the next commands. |
| RUN echo "This is file 1." > file1.txt && echo "This is file 2." > file2.txt | Creates two test files (file1.txt and file2.txt) inside /source to simulate data for backup. |
| RUN mkdir /backup  | Creates a folder named /backup, which will store the copied (backed up) files. |
| CMD cp -r /source /backup | When this container runs, it copies everything from /source to /backup. |

This image acts like a **tiny backup machine** that copies files when run.

* * *

### Step 2: Build the Docker Image

Now, in the same directory where your Dockerfile is saved, run:

```bash
docker build -t backup_tool:latest .
```

**Explanation:**

*   `docker build` → Command to create a new Docker image.
    
*   `-t backup_tool:latest` → Tags the image with the name `backup_tool` and the version `latest`.
    
*   `.` → The dot tells Docker to use the **current directory** for the Dockerfile.
    

Once built, check the image using:

```bash
docker images
```

You should see `backup_tool` listed.

* * *

## 3\. Bash Script Implementation

Now we’ll create a **Bash script** that uses this Docker image to perform backups automatically.

* * *

### Step 1: Create the Bash Script

In your terminal, move to the root directory (optional):

```bash
cd /
```

Then create a new script file:

```bash
nano docker_workload.sh
```

* * *

### Step 2: Write the Script

Copy and paste the following code into the file:

```bash
#!/bin/bash

# Define variables
backup_directory="/backup_data"
docker_container_name="my_backup_container"
backup_tool_image="backup_tool:latest"

# Function to perform a backup using Dockerized backup tool
perform_backup() {
    # Create a backup directory if it doesn't exist
    mkdir -p "$backup_directory"

    # Run the Docker container to perform the backup
    docker run --rm -v "$backup_directory:/backup" "$backup_tool_image"
}

# Main script workflow
main() {
    echo "Starting periodic backup..."
    
    # Perform the backup
    perform_backup

    echo "Backup completed."
}

# Call the main function to start the script
main
```

* * *

### Step-by-Step Explanation of the Script

Let’s go line by line so you fully understand how this script works.

* * *

#### Shebang

```bash
#!/bin/bash
```

This tells the system to run the script using **Bash**.

* * *

#### Variable Definitions

```bash
backup_directory="/backup_data"
docker_container_name="my_backup_container"
backup_tool_image="backup_tool:latest"
```

*   `backup_directory` — The folder on your system where backups will be stored.
    
*   `docker_container_name` — A name given to the container (not mandatory here but useful in logs).
    
*   `backup_tool_image` — The Docker image name that will be used to perform the backup.
    

* * *

#### perform\_backup Function

```bash
perform_backup() {
    mkdir -p "$backup_directory"
    docker run --rm -v "$backup_directory:/backup" "$backup_tool_image"
}
```

**Explanation:**

*   `mkdir -p "$backup_directory"`  
    → Creates the directory if it doesn’t already exist.  
    The `-p` flag prevents errors if the directory already exists.
    
*   `docker run --rm -v "$backup_directory:/backup" "$backup_tool_image"`  
    → Runs the Docker container using the backup image.
    
    *   `--rm` → Automatically removes the container when the job is done.
        
    *   `-v "$backup_directory:/backup"` → Mounts your system directory `/backup_data` as `/backup` inside the container.
        
    *   `"$backup_tool_image"` → The name of the image to run (`backup_tool:latest`).
        

This means whatever is copied to `/backup` inside the container is stored permanently in `/backup_data` on your system.

* * *

#### Main Function

```bash
main() {
    echo "Starting periodic backup..."
    perform_backup
    echo "Backup completed."
}
```

*   Prints a message when the backup starts.
    
*   Calls the `perform_backup` function to do the actual work.
    
*   Prints a message when the process finishes.
    

* * *

#### Running the Main Function

`main`

*   This line executes the `main` function when the script starts.
    

* * *

### Step 3: Save and Exit

Press:

*   **Ctrl + O** → Save
    
*   **Enter** → Confirm
    
*   **Ctrl + X** → Exit the editor
    

* * *

### Step 4: Make the Script Executable

Run:

```bash
chmod +x docker_workload.sh
```

This command allows you to execute the script.

* * *

### Step 5: Test the Script

Run it manually once to ensure it works:

```bash
./docker_workload.sh
```

If everything is correct, you’ll see:

```
Starting periodic backup...
Backup completed.
```

* * *

## 4\. Scheduling the Script with Cron

Now that the script works, let’s make it run automatically at regular intervals.

Cron is a **job scheduler** in Linux that runs commands or scripts at specific times.

* * *

### Step 1: Open Cron Configuration

To edit your Cron jobs, run:

```bash
crontab -e
```

If it asks you to choose an editor, type `1` (for nano) and press Enter.

* * *

### Step 2: Add a New Cron Job

Add this line at the bottom of the file:

```bash
0 0 * * * /docker_workload.sh
```

* * *

### Step-by-Step Explanation

| Part      | Meaning |
| ---       | --- |
| 0 0 * * * | The time pattern — this means “run every day at midnight”. |
| /docker_workload.sh | The full path to the script you want Cron to run. |

💡 **Tip:**  
Make sure you specify the **absolute path** to your script.  
For example, if your script is stored in `/home/ubuntu/docker_workload.sh`, write:

```bash
0 0 * * * /home/ubuntu/docker_workload.sh
```

* * *

### Step 3: Save and Exit

*   Press **Ctrl + O** → Enter → **Ctrl + X**  
    Now Cron will automatically execute your script every day at midnight.
    

* * *

### Step 4: Verify Cron is Running

You can verify your Cron jobs using:

```bash
crontab -l
```

This lists all scheduled Cron jobs for your user.

* * *

## 5\. Conclusion

In this lab, you learned how to:

✅ Build a **simple Dockerized backup tool** using a Dockerfile.  
✅ Write a **Bash script** to automate Docker backups.  
✅ Use **Cron jobs** to schedule your script at regular intervals.

By automating your Docker operations, you save time and ensure that important maintenance tasks — such as backups — happen reliably and consistently.

This is a critical DevOps practice used in real-world infrastructure management.

* * *

## 6\. Practice Tasks

Try these tasks to improve your understanding:

1.  **Task 1:** Change the Cron schedule to run the backup every 6 hours.
    
2.  **Task 2:** Modify the script to log every backup operation to a file (e.g., `/var/log/docker_backup.log`).
    
3.  **Task 3:** Add error handling in the script — if Docker fails, display a custom error message.
    
4.  **Task 4:** Add a function to clean up old backups automatically (e.g., delete files older than 7 days).
    
5.  **Task 5:** Send a notification (using `mail` or `echo`) after each successful backup.
