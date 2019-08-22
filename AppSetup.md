# Application Setup #

## Overview
By now, you should have your Cloud9 instance configured and ready to go. In this section we will install the required libraries in our Cloud9 instance and bootstrap our Amplify Application. We will then create several Amplify environments which will form a part of our deployment pipelines. We will then finish off the section be commiting our new application into a github repository in preperation for the next step of the workshop.

## Bootstrap Project
1. To get started, make sure you are logged into your AWS Cloud9 environment. If you have followed the instuctions outlined in the previous part, you can find a list of your Cloud9 environments by visiting the Cloud9 management console [here](https://us-east-1.console.aws.amazon.com/cloud9/home?region=us-east-1#).

2. You should now be presented with your Cloud9 IDE as shown below.
![Cloud9](/images/step1_2_cloud9ide.png)

3. From the bash terminal (located in the bottom of the IDE) we can now start installing and configuring our application. Start by installing the <b>create-react-app</b> npm package.
```bash
npm install -g create-react-app
```

4. Next, we can bootstrap our new react application. for the purposes of this workshop, we I am naming the project "<b>notes with amplify</b>".
```bash
create-react-app noteswithamplify
```

5. cd into the directory.
```bash
cd noteswithamplify
```

6. Install the AWS Amplify CLI tools so that we can interact with our Amplify application. Will also need to install a new other packages that we will need throughout the rest of the project.
```bash
npm install -g @aws-amplify/cli
npm install --save aws-amplify aws-amplify-react uuid bootstrap
```

7. Next we need to configure our Amplify environment by providing it with our AWS region and IAM user details. AWS Amplify will do most of the work for us and will only require us to complete the IAM user creation and provide the accessKeyId and secretAccessKey.
```bash
amplify configure
```
The Amplify CLI will prompt us for some answers. For the purposes of the workshop, go ahead and use the values shown below.
- <i>Specify the AWS Region:</i> <b>us-east-1</b>
- <i>Specify the username of the new IAM user</i> <b>amplify-workshop-user</b>
At this stage the Amplify CLI will provide us a link to open to complete the creation of the IAM user it will use to interact with our AWS environment. Right-Click on the link and follow the process to complete the creation of the new user. Copy the <b>accessId</b> and <b>secretAccessKey</b> and enter them in the following prompts. You can also define a profile to use.

## Initialise Amplify Project
8. We are now ready to initialise our new AWS Amplify Project. Like the <b>aws configure</b> command above, this command will also prompt for some values. No ahead and enter the values as shown below, most of which are the default values.
```bash
amplify init
```
- Enter a name for the project: <b>noteswithamplify</b>
- Enter a name for the environment: <b>prod</b>
- Choose your default editor: <b>None</b>
- Choose the type of app that you’re building: <b>javascript</b>
- Please tell us about your project
- What javascript framework are you using: <b>react</b>
- Source Directory Path:  <b>src</b>
- Distribution Directory Path: <b>build</b>
- Build Command: <b>npm run-script build</b>
- Start Commend: <b>npm run-script start</b>
- Do you want to use an AWS profile: <b>yes</b>
- Please choose the profile you want to use: <b>default</b>

9. This will go ahead and configure our base Amplify project and create a <b>prod</b> environment. This environment will host our production environment, however we will also need an environment to run our test branch and develop our features in. To achieve this we can add two additional environments to our configuration as shown below.
```bash
amplify env add
```
- Do you want to use an existing environment? <b>No</b>
- Enter a name for the environment: <b>test</b>
- Do you want to use an AWS profile: <b>Yes</b>
- Please choose the profile you want to use: <b>default</b>
```bash
amplify env add
```
- Do you want to use an existing environment? <b>No</b>
- Enter a name for the environment: <b>dev</b>
- Do you want to use an AWS profile: <b>Yes</b>
- Please choose the profile you want to use: <b>default</b>

10. Now that we have our Amplify baseline created and our 3 environments created it's time to commit our code to a git repository. In order to work with the next part of the workshop, one of the following git repositories is required to be used:
- AWS CodeCommit
- GitHub
- BitBucket
- GitLab

## Next Steps

These instructions are for committing your repository to GitHub.  If you want to use one of the other Git repositories, you'll need to change the remote URL to the URL of your preferred repository host.

First of all, you'll need to create a Git repository and commit all of your files to it.  You can do this using the following commands:

```
git init .
git add .
git commit -m "Initial commit"
```

Next, you'll need to create a new repository in GitHub.  Head over to [this page](https://github.com/new), and create a new repository called `amplify`.  Make sure that the 'Initialise this repository with a README' box is unchecked, and that you're not adding a `.gitignore ` page or a license.  When you've done that, hit the 'Create repository' button.

Now, you can push your code to GitHub using the following commands:

```
git remote add origin https://github.com/<your-github-username>/amplify
git push -u origin master

```

Then, create and push a new branch called `test`.  We'll need this for the next section of the workshop.

```
git branch test
git push -u origin test
```

Once this has been done, you're ready to move onto the next part of the workshop where we will configure our Amplify Deploy processes. Click [here](/DeploymentSetup.md) to process to the next part.
