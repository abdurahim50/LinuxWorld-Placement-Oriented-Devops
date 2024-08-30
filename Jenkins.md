# Task 1

## Scenario : Setting Up a Basic Build Job for a Python Script
You need to create a Jenkins job to run a Python script from a GitHub repository every time a change is pushed to the main branch. The script
should:
+ Clone the following repository from GitHub. https://github.com/Pramod123789/jenkins-python.git
+ Run the Python script named app.py. Script is in the above repo.

_______________________________________________________________________________________________________________________________________________________________________________________________________________________
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
