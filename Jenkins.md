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
  
#### Build Triggers:
- Check the "GitHub hook trigger for GITScm polling" option to trigger the build when a change is pushed to the repository.
#### Build Step:
- In the "Build" section, click on "Add build step" and select "Execute shell."
- Enter the following command to run the Python script:
```
python3 app.py
```
Save and Build:
Save the configuration and test the setup by pushing a change to the repository.

# Task 2

## Scenario : Email Notification on Build Failure
- Set up a Jenkins job to send an email notification to the development team if a build fails. Assume the project is a simple Java application, and the build step
runs mvn compile.
_____________________________________________________________________________________________________________________________________________________________________________________________
# Solution



# Task 3
## Scenario : Scheduling a Job to Run Periodically
- Set up a Jenkins job that runs a backup script located on the server every night at midnight.
_____________________________________________________________________________________________________________________________________________________________________________________________

# Solution



# Task 4 
## Scenario : Simple Deployment to a Local Server
- Create a Jenkins job that deploys a static website (HTML/CSS/JS files) to a local Apache server whenever a change is detected in the repository.
_____________________________________________________________________________________________________________________________________________________________________________________________

# Solution




# Task 5
## Scenario : Building a Simple Job with Parameters
- Set up a Jenkins job that accepts a parameter (e.g., a branch name) and checks out the specified branch from a Git repository. The job should then run a shell script to print the branch name.
_____________________________________________________________________________________________________________________________________________________________________________________________

# Solution

