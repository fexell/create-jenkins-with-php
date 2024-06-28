Installing PHP on a Jenkins server running from a Docker container involves creating a custom Docker image that includes both Jenkins and PHP. Here's a step-by-step guide to achieve this:

### Step 1: Create a Custom Dockerfile
Create a Dockerfile that installs Jenkins and PHP. Here’s an example:

```Dockerfile
# Use the official Jenkins base image
FROM jenkins/jenkins:lts

# Switch to the root user to install PHP
USER root

# Update the package list and install PHP
RUN apt-get update && \
    apt-get install -y php php-cli php-mbstring php-xml php-zip php-curl php-gd && \
    apt-get clean

# Switch back to the Jenkins user
USER jenkins
```

### Step 2: Build the Docker Image
Navigate to the directory containing your Dockerfile and build the image:

```bash
docker build -t jenkins-php .
```

### Step 3: Run the Docker Container
Run a container from the custom image:

```bash
docker run -d -p 8080:8080 -p 50000:50000 --name jenkins-php-server jenkins-php
```

### Step 4: Verify PHP Installation
After the container is running, you can verify that PHP is installed correctly.

1. Access the Jenkins container:
    ```bash
    docker exec -it jenkins-php-server bash
    ```
2. Check the PHP version:
    ```bash
    php -v
    ```

### Step 5: Configure Jenkins to Use PHP
1. Open Jenkins in your web browser (typically at `http://localhost:8080`).
2. Go to **Manage Jenkins** -> **Global Tool Configuration**.
3. Scroll down to **PHP** and click **Add PHP**.
4. Provide a name for your PHP installation and specify the path to the PHP executable (usually `/usr/bin/php` inside the Docker container).
5. Click **Save**.

### Step 6: Create a Jenkins Job to Run PHP Scripts
1. Click **New Item** to create a new job.
2. Enter a name for the job, select **Freestyle project**, and click **OK**.
3. In the job configuration:
    - **Source Code Management**: Configure your source code repository (e.g., Git).
    - **Build**: Click **Add build step** and select **Execute shell**.
    - In the command box, enter the command to run your PHP script. For example:
      ```bash
      php path/to/your-script.php
      ```
4. Click **Save**.

### Step 7: Run the Job
1. Go to the job page and click **Build Now**.
2. Monitor the build process and check the console output to ensure that your PHP script is running correctly.

### Additional Configuration (Optional)
For more comprehensive PHP development, you can install additional tools like PHPUnit, PHP CodeSniffer, and PHP Mess Detector. Add the installation commands to your Dockerfile as needed. For example:

```Dockerfile
RUN apt-get update && \
    apt-get install -y phpunit php-codesniffer phpmd && \
    apt-get clean
```

### Example: Adding PHPUnit Build Step
1. In the Jenkins job configuration, click **Add build step** and select **Execute shell**.
2. Enter the command to run PHPUnit:
    ```bash
    phpunit path/to/your-test-directory
    ```

By following these steps, you can set up a Jenkins server running in a Docker container with PHP installed, enabling you to automate your PHP project builds and tests efficiently.
