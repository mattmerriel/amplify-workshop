# Getting Started: Intro to AWS Amplify: build a multi-tenant SaaS app #

## What is this?
This is the <b>Intro to AWS Amplify: Build a multi-tenant SaaS app</b> repository as part of the event organised by the [Melbourne AWS Programming and Tools Meetup](https://www.meetup.com/Melbourne-AWS-Programming-and-Tools-Meetup/events/261692367/).

This repository contains instructions and assets to build your first AWS Amplify application with React and Node JS hosted on AWS. It is split up into 6 Parts, each containing instructions to get your first Amplify project published.

| Part | Description |
|:---:|:---|
| <b>Preparation</b> | This Branch (Current Page) provides an introduction and instructions on creating the required AWS environment. |
| <b>App Setup</b> | This Section outlines the steps to setup out new Amplify application and host it in a repository. |
| <b>Deployment Setup</b> | Now that we have our base application configured, we can setup our deployment pipelines |
| <b>Application Development</b> | In this part, we start building out our applications functionality |
| <b>Trigger Deployment</b> | Next we can go ahead and push our changes into our automated pipelines |
| <b>Adding Search</b> | Finally, we can add some search functionality to our application and deploy our changes to production |

## What is AWS Amplify

AWS Amplify is an Open Source library under Apache 2.0 for developers looking to build cloud connected applications with JavaScript on web or mobile platforms. AWS Amplify is designed to give a declarative interface to client developers looking to perform common actions using cloud services in a scalable and secure manner. 

![https://aws-amplify.github.io/](/images/m-amplify_header.png)

The AWS Amplify library modules are broken down into categories (Auth, Analytics, Storage, APIs, Caching) to quickly add features such as User SignUp/SignIn, MFA, tracking or metric analytics, content management or Serverless API integration. The library also has internationalization and localization for multi-language support, as well as caching capabilities. Finally, there are components and extensions for React and React Native, allowing developers writing applications on those platforms to add these capabilities with framework specific standards. JavaScript developers can also use the library to build their own custom UI components, such as custom authentication flows. AWS Amplify is designed to be extended with different implementations among community and partner contributors. 


## Preparation:
### Before we get started
in order to follow along with this workshop, you'll need to already have the following:
- <b>AWS Account</b> We will be using AWS as our Cloud provider. sign-up [here](https://aws.amazon.com) if you don't already have one
- <b>Github Account</b> to download this repository into your Cloud9 instance.

for the purposes of this workshop, we will be deploying all of our resources in the <b>us-east-1</b> region.

The first step once you've got your AWS account is to create a Cloud9 instance which will house the project and provide an IDE from which to work. More information on Cloud9 can be found at it's [product page](https://aws.amazon.com/cloud9/) .

1. Browse to the Cloud9 Management https://console.aws.amazon.com/cloud9/home/product 
![Cloud9 Landing Page](/images/m-cloud9_landingPage.png)

2. Create a new Cloud9 environment by clicking the <b>"create environment"</b> button available on the right hand side of the landing page.
3. Give your environment a name and click <b>"Next step"</b>. For this example i'm using "Amplify-Workshop".
![Cloud9 Name Environment](/images/m-cloud9_step3.png)

4.here we can configure your environment settings, but for this workshop we will can just click the <b>"Next step"</b>.
![Cloud9 Configure Settings](/images/m-cloud9_step4.png)

5. Review the environment details and click <b>"Create environment"</b>.
![CLoud9 Environment Review](/images/m-cloud9_step5.png)

6. Wait for the creation of your Cloud9 instance to complete. This can take a few minutes.
![Cloud9 Environment Creation](/images/m-cloud9_step6.png)

7. Once you see your new Cloud9 IDE, your ready to move onto Part 1 of the workshop.
![Cloud9 Setup Complete](/images/m-cloud9_finished.png)

## Conclusion
you should now have your AWS account with a Cloud9 instance running in the <b>us-east-1</b> region. At this point in time, your ready to move onto [Part 1](/AppSetup.md) of the workshop where we will start to install and configure our new Note taking application.

