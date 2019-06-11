# Application Development #

## Overview
By now, we have:
* Our Cloud9 IDE with the required packages installed and ready for use.
* Our bootstrapped application committed to our Git repository with our "master" and "test" branches configured
* Two Amplify Deploy pipelines configured and had the complete their initial deployments.

In this part of the workshop, we will start building out our applications functionality by adding in some basic authentication and a GraphQL endpoint that will be responsible for providing access to our users notes.

## Configuring Dev Branch
The first thing we need to do is setup our new "dev" branch as point to our "dev" Amplify environment.

1. Firstly, browse back to your Cloud9 IDE and make sure that your bash session is in the right folder.
![Cloud9 IDE](/images/step3-1_Cloud9_IDE.png)

2. here we can see that we are currently in our "test" branch (as indicated by the (test) immediately before the $ symbol). We first need to create ourselves a new branch called "dev" so that we can develop our new functionality before merging it back into our test environment.
```bash
git checkout -b dev
```
you should now have (dev) immediately before the $ rather than (test) as shown below/
![Git Branch](/images/step3-2_git_branch.png)

3. The next thing we need to do is tell Amplify that we want to be using our "dev" environment that we created earlier. This is pretty simple and is just a single command.
```bash
amplify env checkout dev
```
![Amplify Environment Checkout](/images/step3-3_amplify_env_checkout.png)

We are now ready to start editing our application.

## Adding Authentication
The next thing we are going to do is add some authentication to our application. To do this we will use the "amplify auth" feature that will automatically add in a cognito user pool for us to use. We can then make a few simple changes to our React application to leverage this feature.

4. To add Authentication, we firstly need to tell Amplify to add the required components to our applications codebase. To do this, we simply run the "amplify add" command from the bash terminal.
```bash
amplify add auth
```
The Amplify CLI will then go through a series of questions to define how our solution is configured. For our example we can simply accept the defaults, however it's important to note that when working on a production application, these setting can not be changed once deployed.... So some thought might need to go into their answers.
![Amplify Auth Config](/images/step3-4_ampligy_auth_config.png)

5. Now that we've added authentication to our code base, we need to tell Amplify to deploy the required backend resources so that we can leverage them (in this case Amazon Cognito). As stated in the console, this easiest way to do this is to perform an "amplify push" which will use cloudformation templates to deploy the required resources.
```bash
amplify push
```
We immediately get presented with information saying that Amplify is going to need to "Create" the resources required for our "Auth" component and it will do this in our "dev" environment. this is what we want, so we can simply hit enter to proceed. As soon as you hit Enter, the cloudformation deployment will start and take a couple of minutes to complete.

![Amplify Push](/images/step3-5_amplify_push.png)

6. We have our configuration defined, CloudFormation deployed... Time to leverage it in our React application. to do this is as easy as adding the following lines to our <b>/src/App.js</b> file (Do not do anything yet, the below is for illustration purposes).
```js
import { withAuthenticator } from 'aws-amplify-react'; // or 'aws-amplify-react-native';
import Amplify from 'aws-amplify';
// Get the aws resources configuration parameters
import awsconfig from './aws-exports'; // if you are using Amplify CLI

Amplify.configure(awsconfig);

// ...

export default withAuthenticator(App);
```

to make life easy, open the <b>/src/App.js</b> file and replace it with the below code.
```js
import React from 'react';
import { withAuthenticator } from 'aws-amplify-react'; // or 'aws-amplify-react-native';
import Amplify from 'aws-amplify';
import logo from './logo.svg';
import './App.css';
// Get the aws resources configuration parameters
import awsconfig from './aws-exports'; // if you are using Amplify CLI

Amplify.configure(awsconfig);

function App() {
  return (
    <div className="App">
      <header className="App-header">
        <img src={logo} className="App-logo" alt="logo" />
        <p>
          Edit <code>src/App.js</code> and save to reload.
        </p>
        <a
          className="App-link"
          href="https://reactjs.org"
          target="_blank"
          rel="noopener noreferrer"
        >
          Learn React
        </a>
      </header>
    </div>
  );
}

export default withAuthenticator(App, { includeGreetings: true });
```
don't forget to save the file once your done.

7. Time to test it. The quickest and easiest way to do this is to just run "Amplify serve" which will start a local development instance that you can use.
```bash
amplify serve
```
![Amplify Serve](/images/step3-6_amplify_serve.png)

But Argh! how do we get to local host when we are developing on a remote Cloud9 instance? The easiest way is to click on the "Preview" dropdown at the top of the page, and select "Preview Running Application". This will open up a new tab with your site. If you'd like to view it in a full window, click the button on the far right of it's address bar.
![Cloud9 Preview](/images/step3-7_preview_running.png).

Once you've open the preview page you should be presented with a login screen.
![Login Page](/images/step3-8_login_page.png)

Go ahead and click "Create account" and go through the process. The default configuration requires a verification code to be confirmed via email before a user can login so pick an email address you have access to. You can also test out the "Reset Password" flow as that should also be working at this point.

Once you've verified an account you should be able to login using a newly created username and password and be presented with our application once again. Only this time, their is a Menubar at the top displaying your username and a "SIGN OUT" Button (the menu bar came from the "includeGreeting: true" parameter we passed in the last line of our <b>/src/App.js</b> file).
![Sign Out](/images/step3-9_sign_out.png)

## Adding a GraphQL API
So, we have an application that has fully working authentication (ok, it's not the prettiest login screen ... but it works) but doesn't do much other than that. Now would be a good time to actually add some functionality to our application and we'll do that by adding in a GraphQL endpoint which will store our notes in a dynamoDB table. We'll also need to add some basic UI bootstrap to present the notes back to the user.

1. To add Authentication, we do much the same thing we did when we added authentication. We simply run the "amplify add" command from the bash terminal.
```bash
amplify add api
```

The Amplify CLI will then go through a series of questions to define how our solution is configured. For our example, we will only deviate from the defaults for out authorization, where we will select "Amazon Cognito User Pool" rather than "API Key".
![Amplify Api Config](/images/step3-10_amplify_api_config.png)

Once you get to the "Do you want to edit the schema now?" question, it will prompt you to edit the schema file it has created. Simply click on the link it provides and select "Open". Then replace everything in the file with the below. Don't forget to save the file before returning to the terminal.
```
type Note @model @auth(rules: [{allow: owner}]){
   id: ID!
   note: String!
}
```

press "Enter" to complete the configuration process.
![API Config Finished](/images/step3-11_amplify_api_config_finished.png)

2. Now that we've added an API to our code base, we need to tell Amplify to deploy the required backend resources so that we can leverage it. As stated in the console, this easiest way to do this is to perform an "amplify push" which will use cloudformation templates to deploy the required resources.
```bash
amplify push
```
We immediately get presented with similar information to what we did last time, however now it shows that we will be creating our API, while making no changes to our Auth resources. Press "Enter" to continue.

![Amplify Push](/images/step3-12_amplify_push.png)

The next question asks us if we want to generate code for our newly created GraphQL API. For our example we will be writing our own so answer "n" to this question. After that, the Amplify CLI will again go and deploy a series of CloudFormation templates responsible for our API. Note that this does take a couple of minutes, but make sure it completes before proceeding to the next step.

3. Before we go ahead and add some more code to our <b>/src/App.js</b> file, lets make sure that our new API endpoint is working correctly. To do this, go ahead and open up a new tab and browse to https://console.aws.amazon.com/appsync/home?region=us-east-1 and open up your new API.
![AppSync](/images/step3-13_appsync.png)

4. Click on "Queries" from the Left hand menu, and then click the "Login with User Pools" button.
![Queries](/images/step3-14_queries.png)

We need to provide AppSync with three pieces of information before we can start making queries against our API:
* <b>ClientID</b> this can be found in the <b>/src/aws-exports.js</b> file of our project. It's the "aws_user_pools_web_client_id" value.
* <b>Username</b> is simply the username of the test user we created earlier
* <b>Password</b> and it's password.
Enter the information and click "Login". At this point you should be back at the "Queries" page.
![Queries Logged In](/images/step3-15_queries_loggedin.png)

5. Firstly, lets try and create a new note. To do this, enter the below query into the window (feel free to delete the code that's already their) and click the play button.
```
mutation createNote {
  createNote(input: {
    note: "Test Note Number 1"
  }) {
    id note
  }
}
```

if everything is working correctly, you should get a response confirming the creation of your new note as shown below.
![Query Successful](/images/step3-16_successful_query.png)

go ahead and create a few more notes using the below queries. Make sure to run each query separately!
```
mutation createNote {
  createNote(input: {
    note: "Test Note Number 2"
  }) {
    id note
  }
}
```
```
mutation createNote {
  createNote(input: {
    note: "Test Note Number 3"
  }) {
    id note
  }
}
```

we can also test retrieving notes by running a query request.
```
query listNotes {
  listNotes {
    items {
      note
    }
  }
}
```
this should return a list of all of our notes without the id field.
![List Notes](/images/step3-17_list_notes.png)

6. Now that we know our API is working correctly, It's time to update our application. Back in our Cloud9 IDE,replace the contents of your <b>/src/App.js</b> file with the code below and don't forget to save it:
```js
import React, {Component} from 'react';
import { withAuthenticator } from 'aws-amplify-react'; // or 'aws-amplify-react-native';
import Amplify, {API,graphqlOperation} from 'aws-amplify';
// Get the aws resources configuration parameters
import awsconfig from './aws-exports'; // if you are using Amplify CLI
import 'bootstrap/dist/css/bootstrap.min.css';

Amplify.configure(awsconfig);

const headerStyle = {
  'background-color': '#282c34',
  'min-height': '10vh',
  display: 'flex',
  'flex-direction': 'column',
  'align-items': 'center',
  'justify-content': 'center',
  'font-size': 'calc(10px + 2vmin)',
  color: 'white'
};

const createNote = `mutation createNote($note: String!){
  createNote(input:{
    note: $note
  }){
    __typename
    id
    note
  }
}`;

const readNote = `query listNotes{
  listNotes{
    items{
      __typename
      id
      note
    }
  }
}`;

const updateNote = `mutation updateNote($id: ID!,$note: String){
  updateNote(input:{
    id: $id
    note: $note
  }){
    __typename
    id
    note
  }
}`;

const deleteNote = `mutation deleteNote($id: ID!){
  deleteNote(input:{
    id: $id
  }){
    __typename
    id
    note
  }
}`;

class App extends Component {
  constructor(props){
    super(props);
    this.state={
      id:"",
      notes:[],
      value:"",
      displayAdd:true,
      displayUpdate:false
    };
    this.handleChange = this.handleChange.bind(this);
    this.handleSubmit = this.handleSubmit.bind(this);
    this.handleUpdate = this.handleUpdate.bind(this);
  }

  async componentDidMount(){
    const notes = await API.graphql(graphqlOperation(readNote));
    this.setState({notes:notes.data.listNotes.items});
  }

  handleChange(event) {
    this.setState({value:event.target.value});
  }
  async handleSubmit(event) {
    event.preventDefault();
    event.stopPropagation();
    const note = {"note":this.state.value}
    await API.graphql(graphqlOperation(createNote, note));
    this.listNotes();
    this.setState({value:""});
  }
  async handleDelete(id) {
    const noteId = {"id":id};
    await API.graphql(graphqlOperation(deleteNote, noteId));
    this.listNotes();
  }
  async handleUpdate(event) {
    event.preventDefault();
    event.stopPropagation();
    const note = {"id":this.state.id,"note":this.state.value};
    await API.graphql(graphqlOperation(updateNote, note));
    this.listNotes();
    this.setState({displayAdd:true,displayUpdate:false,value:""});
  }
  selectNote(note){
    this.setState({id:note.id,value:note.note,displayAdd:false,displayUpdate:true});
  }
  async listNotes(){
    const notes = await API.graphql(graphqlOperation(readNote));
    this.setState({notes:notes.data.listNotes.items});
  }
  
  render() {
    const data = [].concat(this.state.notes)
      .map((item,i)=> 
      <div className="alert alert-primary alert-dismissible show" role="alert">
        <span key={item.i} onClick={this.selectNote.bind(this, item)}>{item.note}</span>
        <button key={item.i} type="button" className="close" data-dismiss="alert" aria-label="Close" onClick={this.handleDelete.bind(this, item.id)}>
          <span aria-hidden="true">&times;</span>
        </button>
      </div>
      )
    return (
      <div className="App">
        <header style={headerStyle}>
          <h1 className="App-title">Notes App</h1>
        </header>
        <br/>
        <div className="container">
          {this.state.displayAdd ?
            <form onSubmit={this.handleSubmit}>
              <div className="input-group mb-3">
                <input type="text" className="form-control form-control-lg" placeholder="New Note" aria-label="Note" aria-describedby="basic-addon2" value={this.state.value} onChange={this.handleChange}/>
                <div className="input-group-append">
                  <button className="btn btn-primary" type="submit">Add Note</button>
                </div>
              </div>
            </form>
          : null }
          {this.state.displayUpdate ?
            <form onSubmit={this.handleUpdate}>
              <div className="input-group mb-3">
                <input type="text" className="form-control form-control-lg" placeholder="Update Note" aria-label="Note" aria-describedby="basic-addon2" value={this.state.value} onChange={this.handleChange}/>
                <div className="input-group-append">
                  <button className="btn btn-primary" type="submit">Update Note</button>
                </div>
              </div>
            </form>
          : null }
        </div>
        <br/>
        <div className="container">
          {data}
        </div>
      </div>
    );
  }
}
export default withAuthenticator(App, { includeGreetings: true });
```
Theirs a lot going on here so lets break down the changes we just made:
* <b>Lines 1-5</b> we've added a couple of extra parts we need brought in from the Amplify libraries.
* <b>line 6</b> we've added our bootstrap.css to add some pretty to our application.
* <b>lines 10-19</b> is just a CSS block to clean up the menu. We've also removed the reference to our App.css.
* <b>lines 21-60</b> are the GraphQL queries that we are going to need. These are the same as the queries we ran in the previous steps.
* <b>lines 63-112</b> adds some GraphQL logic and state handling. This is mainly to refresh the page and notes are added/updated or removed.
* <b>lines 115-156</b> is just HTML used to display the page.

And that's it, we've just added all of the code we need to interact with our GraphQL API.

7. Time to test it. Like earlier, all we need to do is to just run "Amplify serve" which will start a local development instance that you can use.
```bash
amplify serve
```
![Amplify Serve](/images/step3-6_amplify_serve.png)

either follow the same process we used before to open the preview window, or refresh it if it's still open. Once you've logged in, you should be presented with a list of the notes we created using AppSync.
![Application with Notes](/images/step3-18_app_with_notes.png)

8. To finish off this section, lets make sure that nobody else can access our notes. Click the "SIGN OUT" button at the top of the application and create a second user account using the same process we followed earlier. When you sign in, you should be presented with no notes.
![Different User](/images/step3-19_different_user.png)

But where did we define this? Well, back in our GraphQL schema, we defined the "allow: owner" parameter as shown below
```
type Note @model @auth(rules: [{allow: owner}]){
   id: ID!
   note: String!
}
```
This automatically ensures that users can only see the notes that they created. What's not clear from our schema file is that AppSync is adding an "owner" field to every record it creates and this is what's it's checking as a part of the authentication against the API. We can confirm this by taking a look at the records, AppSync has written to the DynamoDB table.
![Confirm User](/images/step3-20_confirm_owner.png)

## Committing our Code
The final step is to commit our dev branch back to our repository.

1. we can simply go a "git add ." to stage all of the files we've created ready for a commit.
```bash
git add .
```

2. Next we can commit our code to the branch
```bash
git commit -m "Added Authentication and API"
```

3. And then a git push, to upload it back to our repository. Note that we'll need to set the up stream endpoint.
```bash
git push -u origin dev
```
Our branch has now been uploaded back to our git repository ready for use.

![Git Commit](/images/step3-21_git_commit.png)

## Next Steps
And their we go. We've added some authentication to our application and integrated a GraphQL endpoint to it. We are now ready to move onto the next step [here](/TriggerDeployment.md) where we will merge the code back to our "test" branch and trigger the automated deployment.
