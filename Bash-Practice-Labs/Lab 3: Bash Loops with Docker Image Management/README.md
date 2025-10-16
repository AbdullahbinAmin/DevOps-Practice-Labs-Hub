🧠 **Lab 3: Bash Loops with Docker Image Management**
=====================================================

This version explains **every command and word clearly**, assumes no prior experience, and includes **all missing details** for smooth execution.

🎯 **Objective**
----------------

In this lab, you will learn how to use **Bash loops** to manage **Docker images** automatically.

You’ll understand:

*   How to use **loop structures** in Bash.
    
*   How to list Docker images from the command line.
    
*   How to remove Docker images **interactively** using loops.
    

📘 **Overview**
---------------

This lab demonstrates how Bash scripting and Docker can work together to automate repetitive tasks.You will write a script that:

1.  Lists all available Docker images.
    
2.  Asks for user confirmation before deleting images.
    
3.  Removes images one by one using a **Bash loop**.
    

⚙️ **Prerequisites**
--------------------

Before you start this lab, make sure you have the following:

1.  **AWS Account (with administrative access)** This ensures you can experiment freely in a safe cloud environment.

2.  **Docker Installed and Running** Docker must be installed on your system. You can verify by running:

    ```bash
    docker --version
    ```
    If it shows a version number, Docker is ready.
    
3.  **Basic Bash Knowledge** Completion of the **Bash Script Deep Dive Course** will help you understand loops, conditions, and variable handling.
    
4.  **Docker Knowledge** Completing the **Docker Deep Dive Course** ensures you understand what Docker images are and how they work.
    

🧩 **Implementation Steps**
---------------------------

### **Step 1: Create a Script File**

Create a new Bash file named **docker\_image\_management.sh**.
You can do this using the command:

```bash
nano docker_image_management.sh
```

### **Step 2: Write the Script**

Copy and paste the following code into the file:

```bash   
#!/bin/bash

# Task 1: List all Docker images
echo "Listing all Docker images:"
docker images

# Task 2: Remove each image interactively using a loop
echo "Do you want to remove the Docker images? (yes/no)"
read confirmation

if [ "$confirmation" == "yes" ]; then
    # Loop through the images and remove them one by one
    echo "Removing Docker images..."
    for image_id in $(docker images -q); do
        echo "Removing image $image_id"
        docker rmi $image_id
    done
    echo "All Docker images have been removed."
else
    echo "No Docker images have been removed."
fi
```

### **Step 3: Make the Script Executable**

Before running the script, you must give it permission to execute:

```bash
chmod +x docker_image_management.sh
```

This tells Linux that your script can be executed like a program.

### **Step 4: Run the Script**

Execute the script with:

```bash
./docker_image_management.sh
```

You will see a list of all Docker images on your system.Then the script will ask:

```nginx
Do you want to remove the Docker images? (yes/no)
```

Type:

*   yes → to remove all Docker images.
    
*   no → to keep them.
    

🧱 **Word-by-Word Script Explanation**
--------------------------------------

Let’s break down every line of the code so you understand exactly what it does.

### #!/bin/bash

This is called a **shebang**.It tells your computer that the file should be executed using the **Bash shell** (the command-line interpreter).

### echo "Listing all Docker images:"

The echo command displays text on the terminal.It’s useful for showing progress messages while the script runs.

### docker images

This command lists all the Docker images available on your computer.You’ll see columns like:

*   **REPOSITORY** – the image name (e.g., nginx, ubuntu).
    
*   **TAG** – version of the image (e.g., latest).
    
*   **IMAGE ID** – a unique identifier for each image.
    
*   **CREATED** – when the image was created.
    
*   **SIZE** – the image’s disk size.
    

### echo "Do you want to remove the Docker images? (yes/no)"

This prints a question asking whether you want to delete the images.

### read confirmation

The read command takes input from the user and saves it in a variable.Here, the variable is named **confirmation**.Whatever the user types (like yes or no) gets stored in that variable.

### if \[ "$confirmation" == "yes" \]; then

This checks if the variable **confirmation** is equal to the word **“yes”**.

*   If it is, then the script runs the commands inside the **if block**.
    
*   If not, it skips to the **else block**.
    

### for image\_id in $(docker images -q); do

This line is a **loop**.

Let’s understand it step by step:

*   docker images -q → Lists only the **image IDs** (quiet mode, no headers).
    
*   $( ... ) → Runs the command inside the parentheses and returns its output.
    
*   for image\_id in ... → Loops through each image ID one by one and assigns it to the variable **image\_id**.
    

Example:If there are three image IDs — A, B, and C —the loop runs three times:

*   1st time: image\_id=A
    
*   2nd time: image\_id=B
    
*   3rd time: image\_id=C
    

### echo "Removing image $image\_id"

Displays which image is being deleted.$image\_id is replaced with the current image ID in the loop.

### docker rmi $image\_id

Removes the image from your local system using its ID.

*   docker rmi stands for **remove image**.
    
*   If a container is using that image, you might get an error — in that case, you need to stop and remove the container first.
    

### echo "All Docker images have been removed."

This message is displayed after the loop finishes removing all images.

### else

If the user types anything other than **yes**, the script runs this block instead.

### echo "No Docker images have been removed."

This confirms that the user chose not to delete any images.

⚠️ **Important Note**
---------------------

Be **very careful** when running this script!Deleting Docker images cannot be undone.

If you remove important or large base images (like Ubuntu, Python, or Nginx), Docker will have to re-download them later, which may take time and bandwidth.

Avoid running this script on production systems or servers that host important containers.

✅ **Summary**
-------------

In this lab, you learned:

*   How to use **loops** in Bash (for loop).
    
*   How to use the **read** command for user input.
    
*   How to use **conditions** (if statements) to control flow.
    
*   How to use Docker commands inside a script to **list** and **remove** images.
    

This is the foundation of **automation scripting** in DevOps — small scripts that make repetitive Docker management tasks easier.

🧠 **Practice Tasks (Try These for Hands-On Learning)**
-------------------------------------------------------

### **Task 1:**

Modify the script to **count** how many images are currently on your system before asking for deletion.Hint:

```bash 
docker images -q | wc -l
```

### **Task 2:**

Change the script so it removes **only “dangling” images** (unused ones).Hint:

```bash
docker images -f dangling=true -q
```

### **Task 3:**

Add a **confirmation step for each image** before deleting it.For example:

```bash
Do you want to delete image 12345? (yes/no)
```

### **Task 4:**

Save all deleted image IDs into a file named **deleted\_images.log** for record-keeping.Hint:Use >> deleted\_images.log to append output.

### **Task 5:**

Modify the script to **check if Docker is running** before doing anything.If Docker is not running, print a message and exit.Hint:

```bash
systemctl is-active docker
```
