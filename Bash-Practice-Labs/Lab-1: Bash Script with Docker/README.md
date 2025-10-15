**Bash Script with Docker**
=================================

🎯 **Objective**
----------------

In this lab, you will learn how to use **Bash scripting** to automate simple Docker operations.You will understand:

*   How to pull Docker images using a Bash script.
    
*   How to run and verify Docker containers using commands inside a script.
    

🧩 **Lab Overview**
-------------------

We will write a **Bash script** that performs two main actions:

1.  Pulls an **Nginx** Docker image from Docker Hub.
    
2.  Runs a container from that image automatically.
    

📄 **Bash Script: lab2\_script.sh**
-----------------------------------

```bash
#!/bin/bash

# Task 1: Pull Nginx Docker image
echo "Task 1: Pulling Nginx Docker image"
docker pull nginx

# Check if image pull was successful
if [ $? -eq 0 ]; then
	echo "Image pull successful: Nginx Docker image"
else
	echo "Image pull failed: Nginx Docker image"
	exit 1
fi

# Task 2: Run Docker container
echo "Task 2: Running Docker container"
docker run -d -p 8080:80 --name nginx-container nginx

# Check if container is running
if docker ps | grep -q nginx-container; then
	echo "Nginx container is running and accessible at http://localhost:8080"
else
	echo "Failed to start Nginx container."
	exit 1
fi

# End of script
echo "Lab 2 tasks completed."
```

🧱 **Step-by-Step Explanation**
-------------------------------

### 1\. #!/bin/bash

This line is called a **shebang**.It tells the system to execute the file using the **Bash shell**.

### 2\. echo "Task 1: Pulling Nginx Docker image"

The echo command prints a message to the terminal.It helps you track which step is currently running.

### 3\. docker pull nginx

This command downloads the latest **Nginx image** from Docker Hub to your local system.

*   docker pull → Used to download a Docker image.
    
*   nginx → Name of the image to download.
    

### 4\. if \[ $? -eq 0 \]; then

This checks the **exit code** ($?) of the last command.

*   If the value is 0, it means the previous command was **successful**.
    
*   If not 0, it means there was an **error**.
    

### 5\. docker run -d -p 8080:80 --name nginx-container nginx

This command starts a new **Docker container** from the **nginx** image.

Explanation:

*   docker run → Create and start a new container.
    
*   \-d → Run the container in **detached mode** (in the background).
    
*   \-p 8080:80 → Map port **8080** on your computer to port **80** in the container.
    
*   \--name nginx-container → Assigns a custom name to the container.
    
*   nginx → The image used to start the container.
    

Once the container starts, you can open your browser and visit:

```arduino
http://localhost:8080
```

You should see the **Nginx welcome page**.

### 6\. docker ps | grep -q nginx-container

*   docker ps lists all **running containers**.
    
*   grep -q searches quietly for the container name **nginx-container**. If it’s found, it means the container is running successfully.
    

### 7\. echo "Lab 2 tasks completed."

This confirms that the script has finished executing all tasks successfully.

⚙️ **How to Run the Script**
----------------------------

Follow these steps carefully:

### Step 1: Save the Script

Create and open a file named **lab2\_script.sh**

```bash
nano lab2_script.sh
```

Paste the script code into it, then save and close the file.

### Step 2: Make the Script Executable

```bash
chmod +x lab2_script.sh
```

This command gives the file **execution permission**, allowing it to run as a program.

### Step 3: Run the Script

```bash
./lab2_script.sh
```

You should see messages showing the progress of image pulling and container creation.Finally, check the running container with:

```bash
docker ps
```

🧾 **Summary**
--------------

*   You learned how to **automate Docker tasks** using a Bash script.
    
*   The script checks for errors and gives clear status messages.
    
*   You also learned how to verify that a Docker container is running.
    

🧠 **Practice Tasks (For Better Understanding)**
------------------------------------------------

After completing the main lab, try these exercises to strengthen your skills:

1.  **Task 1:** Modify the script to **pull and run the “httpd” (Apache)** image instead of Nginx. Make sure it runs on port **8081** and verify it using curl http://localhost:8081.

2. **Task 2:** Add a new step in your script that stops and removes the container after testing Use:

    ```bash
    docker stop nginx-container
    docker rm nginx-container
    ```
    
3.  **Task 3:** Add logic to check if the image **already exists** before pulling it again.
   Hint: Use   docker images | grep nginx

4.  **Task 4:** Redirect all output (success and error logs) from your script to a file named **lab2_output.log**.

   	```bash
   	./lab2_script.sh > lab2_output.log 2>&1
    ```

5.  **Task 5:** Modify the script to display the container’s IP address after it starts.
Hint: Use:

	```bash
	docker inspect -f '{{range.NetworkSettings.Networks}}{{.IPAddress}}{{end}}' nginx-container
	```
