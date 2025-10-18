# Lab 5: Accepting User Inputs for Docker Operations

## Objective

In this lab, you will learn how to **accept user inputs in a Bash script** and use that input to perform **Docker operations dynamically**.  
You will write a script that asks the user for a Docker image name, pulls that image, and then runs a container using it.

* * *

## Why This Lab Is Important

In real-world DevOps or automation tasks, we often need to make our scripts **interactive**, so they can **take input from users** rather than being hardcoded.  
For example, instead of fixing one Docker image name inside a script, we can let the user type the image name at runtime.  
This makes scripts **reusable**, **flexible**, and **dynamic**.

* * *

## Prerequisites

Before starting, make sure you have completed or have access to the following:

1.  ✅ An **AWS account** with administrative access (if you’re working on EC2 or cloud environments).
    
2.  ✅ **Docker Deep Dive Course** – You should understand Docker basics like images, containers, and commands such as `docker pull` and `docker run`.
    
3.  ✅ **Bash Script Deep Dive Course** – You should be familiar with writing and running Bash scripts.
    
4.  ✅ Completed **previous labs** (especially those involving Docker and Bash basics).
    

* * *

## 1\. Introduction

In this lab, you will create a Bash script that does the following:

*   Asks the user to type a Docker image name.
    
*   Pulls the Docker image from Docker Hub.
    
*   Runs a container based on that image.
    

This helps you understand how to use **user inputs** with **Docker commands** inside Bash scripts.

* * *

## 2\. Script Implementation

Let’s write the script step by step.

### Step 1: Create the Script File

Open your terminal and type the following command:

```bash
nano docker_operations.sh
```

This will open a text editor where you can type your script.

* * *

### Step 2: Write the Script

Copy and paste the following code:

```bash
#!/bin/bash

# Task 1: Accept an image name as input
read -p "Enter the name of the Docker image: " image_name

# Task 2: Pull the Docker image
echo "Pulling the Docker image: $image_name"
docker pull $image_name

# Task 3: Run the Docker container
echo "Running the Docker container based on $image_name"
docker run -it --rm $image_name
```

* * *

### Step 3: Explanation of the Script (Line by Line)

Let’s understand what each line does in simple terms.

#### Line 1:

`#!/bin/bash`

*   This is called a **shebang line**.
    
*   It tells the system to use **bash shell** to run this script.
    

* * *

#### Line 2:

`# Task 1: Accept an image name as input`

*   This is a **comment**.
    
*   Comments are ignored by the system but help humans understand the purpose of the code.
    

* * *

#### Line 3:

`read -p "Enter the name of the Docker image: " image_name`

*   The **`read`** command is used to take input from the user.
    
*   The **`-p`** flag allows you to display a prompt message to the user.
    
*   The part in quotes (`"Enter the name of the Docker image: "`) is shown to the user on the screen.
    
*   Whatever the user types is stored in the **variable `image_name`**.  
    Example:  
    If the user types `nginx`, then `image_name="nginx"`.
    

* * *

#### Line 4–5:

`echo "Pulling the Docker image: $image_name" docker pull $image_name`

*   The **`echo`** command displays a message on the screen.
    
*   The **`$image_name`** prints the value stored in the variable (for example, `nginx`).
    
*   Then **`docker pull $image_name`** pulls (downloads) that Docker image from **Docker Hub**.  
    Example:  
    If you entered `ubuntu`, this command becomes `docker pull ubuntu`.
    

* * *

#### Line 6–7:

`echo "Running the Docker container based on $image_name" docker run -it --rm $image_name`

*   Again, **`echo`** prints a message.
    
*   The **`docker run`** command runs a new container from the pulled image.
    
*   The flags used:
    
    *   **`-it`** → makes the container interactive (you can type commands inside it).
        
    *   **`--rm`** → automatically removes the container when you exit.
        
*   Example: If you entered `alpine`, it runs `docker run -it --rm alpine`.
    

* * *

## 3\. Running the Script

Follow these steps to execute your script.

### Step 1: Save and Exit

Press **Ctrl + O** to save the file, then **Enter**,  
and **Ctrl + X** to exit the nano editor.

### Step 2: Make the Script Executable

Before you can run it, you need to give it permission:

```bash
chmod +x docker_operations.sh
```

### Step 3: Run the Script

Now run it using:

```bash
./docker_operations.sh
```

### Step 4: Provide Input

When prompted, type any Docker image name, such as:

```nginx
nginx
```

You’ll see output like:

```vbnet
Pulling the Docker image: nginx
Using default tag: latest
latest: Pulling from library/nginx
Digest: sha256:...
Running the Docker container based on nginx
```

Docker will then start a new container for you.

* * *

## 4\. Conclusion

In this lab, you learned:

*   How to use **user inputs** in Bash scripts using the `read` command.
    
*   How to **pull and run Docker images** dynamically based on user input.
    
*   How to make your scripts more **interactive and reusable**.
    

By the end of this lab, you can easily automate Docker operations without hardcoding values — giving users the flexibility to run any image they want.

* * *

## 5\. Practice Tasks

To strengthen your understanding, complete the following tasks:

1.  **Task 1:** Modify the script to also ask for a **container name** from the user and run the container with that name using the `--name` flag.
    
2.  **Task 2:** Add a check to verify if Docker is installed. If not, display a message saying “Docker is not installed. Please install it first.”
    
3.  **Task 3:** After running the container, display a message showing “Container is now running” and the container ID.
    
4.  **Task 4:** Allow the user to enter multiple image names (comma-separated) and pull all of them in a loop.
    
5.  **Task 5:** Add error handling — if `docker pull` fails, show a message like “Image not found or cannot be pulled. Please try again.”
