
Let's first understand what Jenkins, GitHub, Webhook, S3, and Maven are:

Jenkins is an open-source automation tool written in Java with plugins built for Continuous Integration and Continuous deployment/delivery purposes.

GitHub is a code hosting platform for collaboration and version control. It lets you and others work together on projects.

Webhook is a mechanism to automatically trigger the build of a Jenkins project in response to a commit pushed to a Git repository.

Maven is a powerful project management tool that is based on POM (project object model). It is used for project build, dependency, and documentation.

JUnit is an open-source Unit Testing Framework for JAVA. It is useful for Java Developers to write and run repeatable tests.

Amazon S3 (Simple Storage Service) is an object storage service that offers industry-leading scalability, data availability, security, and performance.

1. Launch an EC2 instance and Install Jenkins
First set up the Jenkins server to create a pipeline. Launch an EC2 instance and install Java and Jenkins into it.

Follow this article to set up Jenkins server

You need to install Maven as well to build the project. SSH into the instance and install maven using the below command.


COPY

COPY

COPY

COPY

COPY

COPY
sudo apt-get install maven -y
Set JAVA_HOME and MAVEN_HOME using the below commands. To set the variable permanently, you should add it to the bashrc file in your home directory.


COPY

COPY

COPY

COPY

COPY

COPY
echo "export JAVA_HOME=/usr/lib/jvm/java-11-openjdk-amd64" >> ~/.bashrc

echo "export MAVEN_HOME=/usr/share/maven" >> ~/.bashrc
Check if the variables are exported correctly.


COPY

COPY

COPY

COPY

COPY

COPY
tail -3 ~/.bashrc
2. Install Plugins
Install plugins to integrate Jenkins with GitHub, Maven, and AWS S3. Go to Manage Jenkins, and select Manage plugins. Under available plugins search for the below plugins and Install without restart

GitHub

Maven Integration

S3 publisher



3. Configure Java and Maven in Jenkins
Go to Manage Jenkins, select Global tool configuration, and scroll down to add JDK and Maven path that we exported in the above steps as shown below. Uncheck the Install automatically checkbox and click Save.





4. Create S3 bucket to store artifacts
Search for S3 in AWS services and click Create bucket. Give a unique name to the bucket and choose the region in which you want your bucket to reside. Leave all the setting default and click Create bucket at last.

Go to Jenkins dashboard, select Manage Jenkins then configure System. Scroll to the end to add Amazon S3 profiles. Give S3 bucket name in the profile name and access key and secret access key as shown below and click Save.



5. Create Webhook
Webhook in Jenkins triggers the pipeline automatically when any changes are done in the GitHub repository like commit and push. Go to Jenkins dashboard and copy the URL in the browser. Now go to GitHub repository settings. In the left pane select Webhooks.



Click Add webhook and paste the Jenkins URL in the Payload URL by appending the URL with /github-webhook/ as shown below. Select the events when you want to trigger the pipeline, I have selected Just the push event.



6. Create Jenkins pipeline
Go to Jenkins dashboard and click New item. Enter an item name and select Freestyle project and click Ok. Give a small description of your project and select GitHub project, and enter the URL of your GitHub project. Scroll down and select Git as source code management and enter the repository URL of your project. If your repository is private then add credentials. Enter your branch name under Branches to build section.



Select the GitHub hook trigger for GITScm polling under build triggers. In Build Steps select Invoke top-level Maven targets. Select MAVEN_HOME which we configured in the maven version and enter the clean install test in Goals.

In Post-build Actions, select Publish artifacts to S3 Bucket. Select your AWS profile name and in the source enter the artifact path that you want to store in the S3 bucket. Give the name of your S3 bucket in the Destination bucket. Select the bucket region in which you have created your bucket. Keep all other settings default and click apply save.



Finally, click Build now in the left panel, and you will see the pipeline run successfully.



Check the artifact stored in the S3 bucket after the successful build.



Congratulations 🥳, the pipeline ran successfully and artifacts got stored in S3
