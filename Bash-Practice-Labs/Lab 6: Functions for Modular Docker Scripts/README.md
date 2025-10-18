# Lab 6: Functions in Bash for Modular Docker Scripts

## Objective

In this lab, you will learn how to use **functions in Bash** to make your Docker scripts more **modular**, **organized**, and **reusable**.  
You will create a script that performs Docker operations such as **pulling an image**, **running a container**, and **stopping a container** — all through **separate functions**.

* * *

## Why This Lab Is Important

When you write scripts without functions, all commands are written together in a long list.  
That makes the script hard to read and maintain.

By using **functions**, you can:

*   Separate tasks into logical parts (like pull, run, and stop).
    
*   Reuse those parts easily whenever needed.
    
*   Keep your code cleaner and easier to debug.
    

This is how real DevOps engineers structure their scripts for automation.

* * *

## Prerequisites

Before starting this lab, make sure you have the following:

1.  ✅ An **AWS account with administrative access** (if you are working on an EC2 instance).
    
2.  ✅ Completed the **Docker Deep Dive Course** (you should know basic Docker commands).
    
3.  ✅ Completed the **Bash Script Deep Dive Course** (you should know how to create and run Bash scripts).
    
4.  ✅ Completed **previous labs** that cover Docker basics and user input handling.
    

* * *

## 1\. Introduction

In this lab, we’ll use **Bash functions** to handle Docker operations in a modular way.  
Each Docker task — like pulling, running, or stopping a container — will have its **own function**.

Then, we’ll have a **main function** that runs the workflow step by step.

By the end of this lab, you’ll know how to:

*   Write and call Bash functions.
    
*   Pass parameters (arguments) into those functions.
    
*   Use Docker commands inside Bash scripts in a structured way.
    

* * *

## 2\. Script Implementation

Let’s write the complete script now.

### Step 1: Create the Script File

Open a terminal and create a new file:

```bash
nano docker_modular_script.sh
```

* * *

### Step 2: Write the Script

Copy and paste the following script into your file:

```bash
#!/bin/bash

# Function to pull a Docker image
pull_image() {
    local image_name="$1"
    echo "Pulling the Docker image: $image_name"
    docker pull "$image_name"
}

# Function to run a Docker container
run_container() {
    local image_name="$1"
    echo "Running the Docker container based on: $image_name"
    docker run -it --rm --name "$image_name-container" "$image_name"
}

# Function to stop a Docker container
stop_container() {
    local container_name="$1"
    echo "Stopping the Docker container: $container_name"
    docker stop "$container_name"
}

# Main script workflow
main() {
    read -p "Enter the name of the Docker image to pull and run: " image_name

    # Pull the Docker image
    pull_image "$image_name"

    # Run the Docker container
    run_container "$image_name"

    # Ask if the user wants to stop the container
    read -p "Do you want to stop the container? (yes/no): " stop_choice
    if [ "$stop_choice" == "yes" ]; then
        # Stop the Docker container
        stop_container "$image_name-container"
    else
        echo "Container is still running. You can manually stop it later."
    fi
}

# Call the main function to start the script
main
```

* * *

## 3\. Explanation of the Script (Word by Word)

Let’s break down every part of the script so it’s easy to understand.

* * *

### 1\. Shebang Line

```bash
#!/bin/bash
```

*   This tells the operating system to use the **Bash shell** to run this script.
    
*   Every Bash script starts with this line.
    

* * *

### 2\. pull\_image Function

```bash
pull_image() {
    local image_name="$1"
    echo "Pulling the Docker image: $image_name"
    docker pull "$image_name"
}
```

*   `pull_image()` defines a **function** named `pull_image`.
    
*   Inside the parentheses `{}`, we write the commands this function will perform.
    
*   `local image_name="$1"`:
    
    *   The keyword `local` creates a variable that only exists inside this function.
        
    *   `$1` means **the first argument** passed to the function.
        
    *   For example, if you call `pull_image nginx`, then `$1` becomes `nginx`.
        
*   `echo "Pulling the Docker image: $image_name"`
    
    *   Prints a message showing which image is being pulled.
        
*   `docker pull "$image_name"`
    
    *   Downloads the specified Docker image from Docker Hub.
        
    *   Example: `docker pull nginx`.
        

* * *

### 3\. run\_container Function

```bash
run_container() {
    local image_name="$1"
    echo "Running the Docker container based on: $image_name"
    docker run -it --rm --name "$image_name-container" "$image_name"
}
```

*   Defines a new function called `run_container`.
    
*   `local image_name="$1"` stores the image name passed to this function.
    
*   `echo` prints which container is being started.
    
*   The `docker run` command creates and runs a container:
    
    *   `-it`: Runs the container in interactive mode with a terminal.
        
    *   `--rm`: Automatically removes the container after it stops.
        
    *   `--name "$image_name-container"`: Assigns a name to the container (e.g., if image is `nginx`, container name becomes `nginx-container`).
        
    *   `"$image_name"`: Specifies which image to run.
        

* * *

### 4\. stop\_container Function

```bash
stop_container() {
    local container_name="$1"
    echo "Stopping the Docker container: $container_name"
    docker stop "$container_name"
}
```

*   This function stops a running container.
    
*   It takes one argument — the container name.
    
*   `docker stop` gracefully stops the container.
    

Example:  
If your container name is `nginx-container`, this command becomes:  
```bash
docker stop nginx-container
```

* * *

### 5\. main Function (Main Workflow)

```bash
main() {
    read -p "Enter the name of the Docker image to pull and run: " image_name

    pull_image "$image_name"
    run_container "$image_name"

    read -p "Do you want to stop the container? (yes/no): " stop_choice
    if [ "$stop_choice" == "yes" ]; then
        stop_container "$image_name-container"
    else
        echo "Container is still running. You can manually stop it later."
    fi
}
```

*   The **main function** acts like the central controller of the script.
    
*   It performs the following:
    
    1.  Asks the user to enter an image name.
        
    2.  Calls the `pull_image` function to download it.
        
    3.  Calls the `run_container` function to start the container.
        
    4.  Asks if the user wants to stop the container.
        
    5.  If the user says “yes,” it calls `stop_container` to stop it.
        

* * *

### 6\. Starting the Script

```bash
main
```

*   This line **calls** the `main` function.
    
*   Without this line, the script won’t run anything.
    

* * *

## 4\. Running the Script

Follow these steps to run your Bash script:

### Step 1: Save and Exit

In the nano editor:

*   Press **Ctrl + O** → then press **Enter** to save.
    
*   Press **Ctrl + X** to exit.
    

* * *

### Step 2: Make the Script Executable

Before running, you must make the script executable:

```bash
chmod +x docker_modular_script.sh
```

* * *

### Step 3: Run the Script

Now execute it:

```bash
./docker_modular_script.sh
```

* * *

### Step 4: Provide Inputs

You’ll be asked:

```arduino
Enter the name of the Docker image to pull and run:
```

Type any Docker image name, for example:

```nginx
nginx
```

Then the script will:

1.  Pull the image (`docker pull nginx`)
    
2.  Run the container (`docker run -it --rm --name nginx-container nginx`)
    
3.  Ask if you want to stop the container:
    
    ```vbnet
    Do you want to stop the container? (yes/no):
    ```
    

If you type `yes`, it will stop the container using:

```bash
docker stop nginx-container
```

* * *

## 5\. Conclusion

In this lab, you learned how to:

*   Create and use **functions in Bash**.
    
*   Make your **Docker scripts modular** and organized.
    
*   Pass values (parameters) to functions.
    
*   Build a full workflow combining **Docker pull**, **run**, and **stop** operations.
    

This modular approach is very useful in **real-world automation** — you can reuse and modify these functions for any future Docker-related automation tasks.

* * *

## 6\. Practice Tasks

Try completing these additional exercises to strengthen your understanding:

1.  **Task 1:** Add a function that lists all currently running containers using `docker ps`.
    
2.  **Task 2:** Modify the script to let the user choose between pulling, running, or stopping a container (instead of doing all automatically).
    
3.  **Task 3:** Add error checking — if the image name is empty, show a warning and ask again.
    
4.  **Task 4:** Add a function to remove an image (`docker rmi`) after confirmation.
    
5.  **Task 5:** Modify the `run_container` function to allow the user to choose a custom container name instead of automatically adding “-container”.
