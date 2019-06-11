# Trigger and Automated Deployment #

## Overview
We now have an application that's working with our backend infrastructure and is ready to be deployed into our testing environment.

## Merging our Code
We now want to copy the changes we've made in our "dev" branch, into our "test" branch so that it can be deployed and made available to our testers prior to being merged into production. Given our current setup, the easiest way to do this is through the GitHub Web UI.

1. Open you GitHub project in a new browser tab/window and you should see something similar to the below:
![GitHub](/images/step4-1_github.png)

2. Click on the "Compare & pull request" button and then make sure to select the "test" branch from the "base" dropdown.
![pull request](/images/step4-2_pull_request.png)

3. Once GitHub confirms that it's able to be merged, go ahead and click "Create pull request".
4. This will open a new window where you can simply click "Merge pull request", and then "Confirm merge".
![Merge Pull Request](/images/step4-3_merge_pull_request.png).

once you've confirmed the merge, the status should automatically change to "Merged".
![Merged](/images/step4-4_merged.png)

## Amplify Deploy
1. If we then take a look our our amplify console https://console.aws.amazon.com/amplify/home?region=us-east-1#/ and select our application, we should see that our "test" environment has automatically triggered a deployment.
![Amplify Deploy](/images/step4-5_amplify_deploy.png)

2. The deployment process will slowly work through each of the steps and should eventually pass verification. We can tell it's worked, as we can see the login screen in the screenshots at the bottom of the page.
![Deployment Finished](/images/step4-6_deployment_finished.png)

3. Clicking on the "Domain" link should open our newly deployed application and first present us with the "Access-Control" username and password prompt we set up earlier (unless your browser still has it cached) before sending you to the login page for the application.
![Access Control](/images/step4-7_access_control.png)
![Application Login](/images/step4-8_application_login.png)

It's important to note that the test users we setup in our "dev" environment will NOT exist in our "test" environment as it's a different Cognito User Pool.

4. Go ahead and create a new user and test the application out. It should work exactly the same way as it did when it was in our "dev" environment.

## Deploy to "Production"
the process of deploying our application is the same as deploying into "test" only we'd use the "test" branch as the source and "master" as the destination.

## Next Steps
We have our application, we have our deployment process. Now it's time to add some additional functionality to our application. In the next and final step (available [here](AddingSearch.md)) we will add search functionality by using an Elasticsearch domain.