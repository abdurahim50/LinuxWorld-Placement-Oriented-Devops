# Task 1

## Scenario : Setting Up a Basic Build Job for a Python Script
You need to create a Jenkins job to run a Python script from a GitHub repository every time a change is pushed to the main branch. The script
should:
+ Clone the following repository from GitHub. https://github.com/Pramod123789/jenkins-python.git
+ Run the Python script named app.py. Script is in the above repo.

_____________________________________________________________________________________________________________________________________________________________________________________________

# Solution
Assuming you have Jenkins setup.
## 1. Create a New Jenkins Job:
- Go to Jenkins Dashboard and click on "New Item."
- Enter a name for your job **PythonScriptBuild** and select "Freestyle project."
  
#### Source Code Management:
- In the job configuration, under "Source Code Management," select "Git."
- Enter the repository URL: https://github.com/Pramod123789/jenkins-python.git.
- In the "Branches to build" section, specify */main to track changes on the main branch.

  ![image](https://github.com/user-attachments/assets/51f0a0e2-ca10-4f84-b6ea-72e29b99d727)

  
#### Build Triggers:
- Check the "GitHub hook trigger for GITScm polling" option to trigger the build when a change is pushed to the repository.
  
  ![image](https://github.com/user-attachments/assets/493508fb-7022-4e00-9874-32c4ebc2a099)

To set up a GitHub hook trigger for the "GITScm polling" option in Jenkins, follow these steps:
- After enable GitHub Webhook in Jenkins, go to github follow these steps set up Webhook:

- Go to your GitHub repository.
- Click on "Settings" in the top menu.
- In the left sidebar, click on "Webhooks."
- Click "Add webhook."
- In the "Payload URL" field, enter your Jenkins URL followed by /github-webhook/, for example: http://your-jenkins-url/github-webhook/.
- Set the "Content type" to application/json.
- In the "Which events would you like to trigger this webhook?" section, select "Just the push event."
- Click "Add webhook."

#### Build Step:
- In the "Build" section, click on "Add build step" and select "Execute shell."
- Enter the following command to run the Python script:
```
python3 app.py
```
Save and Build:
Save the configuration and test the setup by pushing a change to the repository.

#### Proof of concept:

![image](https://github.com/user-attachments/assets/96ee15fd-4073-4a5c-baff-c029afab200d)



# Task 2

## Scenario : Email Notification on Build Failure
- Set up a Jenkins job to send an email notification to the development team if a build fails. Assume the project is a simple Java application, and the build step
runs mvn compile.
_____________________________________________________________________________________________________________________________________________________________________________________________
# Solution

### Email Notification on Build Failure
Configure SMTP Server
- Go to Manage Jenkins > Configure System.
- Scroll down to the Extended E-mail Notification section.
- Configure your SMTP server settings:
     - SMTP server: Your mail server (smtp.gmail.com for Gmail).
     - Default user e-mail suffix: e.g., @yourdomain.com.
     - Click on the dropdown in advance
     - Click add credentials and add
     - User Name: Your email address.
     - Password: Your email password (you may need an App Password if using services like Gmail)
     - Use SSL: Check this box if your SMTP server requires SSL ( Gmail uses SSL on port 465).
     - SMTP Port: Enter the appropriate port (465 for SSL, 587 for TLS).
- Scroll down to E-mail Notification section and follow similar steps
- Click Test configuration by sending test e-mail to verify your settings.

![image](https://github.com/user-attachments/assets/f0a71c4c-091c-40b9-a60b-39ea2eb717a6)

![image](https://github.com/user-attachments/assets/18534d58-4566-444e-acd0-b91d5a54d6d9)


- Create a new Freestyle job for the Java project.
### Source Code Management:

- Set up the repository under "Source Code Management" using Git.
### Build Step:

- Add a build step and select "Invoke top-level Maven targets."
- Enter compile as the goal.

### Build settings
- Depending on the version of jenkins, configure as below:
  
![image](https://github.com/user-attachments/assets/6fb7cf99-f374-4ee6-a5af-6ae518596a5b)

#### Post-build Actions:

- Scroll down to "Post-build Actions" and click on "Add post-build action."
- Select "E-mail Notification" or "Editable Email Notification."
- Configure the recipient list, e.g., team@example.com.
- Ensure to set the option to send emails only when the build fails.
- Save and Build:

- Save the job, and on build failure, an email will be sent to the configured recipients.

### Proof of concept:

![image](https://github.com/user-attachments/assets/ef7a81a9-45c7-4cc4-a835-454e8ab603d5)




# Task 3
## Scenario : Scheduling a Job to Run Periodically
- Set up a Jenkins job that runs a backup script located on the server every night at midnight.
____________________________________________________________________________________________________________________________________________________________________________________________

# Solution
## Scheduling a Job to Run Periodically
- Create a New Jenkins Job:
- Create a new Freestyle job named NightlyBackup.
  
## Build Triggers:
- In the "Build Triggers" section, select "Build periodically."
- Enter the cron expression for midnight:
  
```
H 0 * * *
```
## Build Step:

- Add a build step and select "Execute shell."
- Enter the command to run the backup script:
  
```
DATE=$(date)
TIMESTAMP=$(date +"%H-%M-%S")

# Save the backup job status to a file with a timestamped filename
echo "Backup job ran at $DATE" > /tmp/backup_test_$TIMESTAMP.txt
```
- Save the job, and it will run the backup script every night at midnight.

![image](https://github.com/user-attachments/assets/af780f13-21ab-4076-a4b7-422bd5162f79)




# Task 4 
## Scenario : Simple Deployment to a Local Server
- Create a Jenkins job that deploys a static website (HTML/CSS/JS files) to a local Apache server whenever a change is detected in the repository.
_____________________________________________________________________________________________________________________________________________________________________________________________

# Solution
## 1. Prepare Your Environment
Install Apache Server: Make sure Apache is installed and running on your local server. You can install it using the following commands:
```
sudo apt update
sudo apt install apache2
````
- Start and Enable Apache: Start the Apache service and ensure it starts on boot:
  
```
sudo systemctl start apache2
sudo systemctl enable apache2
```

## 2. Create a Jenkins Job

Navigate to your Jenkins dashboard.

- Click on New Item.
- Enter a name for your job (e.g., "Deploy Static Website").
- Select Pipeline and click OK.
- Configure Pipeline:
    - In the job configuration page, scroll down to the Pipeline section.
    - Select Pipeline script from SCM
    - Replace https://github.com/your-username/your-repo.git with the URL of your GitHub repository.
- Adjust the targetDir variable if your Apache document root is different.
- Click Save to save your job configuration.

## 3. Set Up Build Triggers
- Configure SCM Polling
   - In the job configuration page, go to the Build Triggers section.
   - Select Poll SCM and set a schedule (e.g., * * * * * to poll every minute).
- Set Up Webhooks (Optional)
## 4. Test the Deployment
- Click Build Now on the job’s page to test the deployment.
## 5. Verify Deployment
- Visit your local Apache server’s IP address or domain to check if the static website is deployed correctly.
  

![image](https://github.com/user-attachments/assets/033c1d43-5f13-4774-b3ee-e0b804b4a0ad)





# Task 5
## Scenario : Building a Simple Job with Parameters
- Set up a Jenkins job that accepts a parameter (e.g., a branch name) and checks out the specified branch from a Git repository. The job should then run a shell script to print the branch name.
____________________________________________________________________________________________________________________________________________________________________________________________


# Solution

## Create a New Jenkins Job
- Open Jenkins Dashboard: Go to your Jenkins instance.
- Click on "New Item".
- Enter a Job Name: For example, ParameterizedJob.
- Select "Freestyle project": Choose the Freestyle project option and click OK.
## 2. Add Parameters
- Check "This project is parameterized": In the job configuration page, check the "This project is parameterized" option.
- Click on "Add Parameter" and select "String Parameter."
   - Name: Enter BRANCH.
   - Default Value: Enter main (or any default branch name you prefer).
     
![image](https://github.com/user-attachments/assets/8ad81d39-f995-4627-bfd0-27561ab562c9)

## Source Code Management
- check git and add your git repository. 


## 3. Add a Build Step
- Scroll down to the "Build" section.
- Click on "Add build step" and select "Execute shell."
```
git checkout ${BRANCH}
echo "Checked out branch: ${BRANCH}"
```
## 4. Save and Build
- Click "Save" at the bottom of the configuration page.
- Click on "Build Now" from the job's main page. You will be prompted to enter the branch name. The default value will be main, but you can change it as needed.
- Click "Build" to start the job.

  ![image](https://github.com/user-attachments/assets/b40d00b6-797e-4ce8-99a8-1826039479cc)

## 5. Verify the Job
Once the job completes, check the build logs to see the output. It should show the branch that was checked out and confirm that the specified branch was used.

![image](https://github.com/user-attachments/assets/5628a62e-1b39-4dca-947a-99be3aa29d39)


