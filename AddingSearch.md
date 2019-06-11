# Adding and Deploying Search Functionality #

## Overview
As our final task, we will look at adding some search functionality to our application as it's somthing almost everybody needs when working with notes. We will then close out the work by working this update through our deployment process.

## Adding Search
The process of adding search is very simple and consists of only 3 steps. Before we start, we need to browse back to our Cloud9 IDE [here](https://us-east-1.console.aws.amazon.com/cloud9/home?region=us-east-1#).

1. Firstly, we need to open our GraphQL schema file which is <b>/amplify/backend/api/amplifynotes/schema.graphql</b> and replace the contents with the below:
```
type Note @model @auth(rules: [{allow: owner}]) @searchable{
   id: ID!
   note: String!
}
```
Tha'ts right, all we've done is add the @searchable directive to our Note object type. And that's our GraphQL endpoint taken care of.

2. Next, we need to update out backend infrastructure as we will need to deploy our Elasticsearch service. To do this, all we need to do is tell Amplify to update our cloud environment by running 'amplify push' again.
```bash
amplify push
```
when we do this, we can see that Amplify has identified that an update is required to our API configuration. We can do ahead and hit "Enter" to continue.
![Amplify Push](/images/step5-1_amplify_push.png)

NOTE: this will take some time to deploy.

3. The final step is to update our <b>/src/App.js</b> file with some additional code to add a search button as well as handle/display the results. Replace the contents of the file with the below.

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

const searchNote = `query searchNotes($search: String){
  searchNotes(filter:{note:{match:$search}}){
    items{
      id
      note
    }
  }
}`

class App extends Component {
  constructor(props){
    super(props);
    this.state={
      id:"",
      notes:[],
      searchResults:[],
      value:"",
      displayAdd:true,
      displayUpdate:false,
      displaySearch:false
    };
    this.handleChange = this.handleChange.bind(this);
    this.handleSubmit = this.handleSubmit.bind(this);
    this.handleUpdate = this.handleUpdate.bind(this);
    this.handleSearch = this.handleSearch.bind(this);
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
  async handleSearch(event) {
    event.preventDefault();
    event.stopPropagation();
    const search = {"search":this.state.value};
    const result = await API.graphql(graphqlOperation(searchNote, search));
    this.setState({searchResults:result.data.searchNotes.items,notes:[],displaySearch:true,value:""});   
    if(JSON.stringify(result.data.searchNotes.items) === '[]'){
      this.setState({searchResults:[{note:"No Match: Clear the search to go back to your Notes"}]});
    };
  }
  selectNote(note){
    this.setState({id:note.id,value:note.note,displayAdd:false,displayUpdate:true});
  }
  async listNotes(){
    const notes = await API.graphql(graphqlOperation(readNote));
    this.setState({notes:notes.data.listNotes.items,searchResults:[],displaySearch:false});
  }
  
  render() {
    const data = [].concat(this.state.notes)
      .map((item,i)=> 
      <div className="alert alert-primary show" role="alert">
        <span key={item.i} onClick={this.selectNote.bind(this, item)}>{item.note}</span>
        <button key={item.i} type="button" className="close" data-dismiss="alert" aria-label="Close" onClick={this.handleDelete.bind(this, item.id)}>
          <span aria-hidden="true">&times;</span>
        </button>
      </div>
      )
    const searchResults = [].concat(this.state.searchResults)
      .map((item,i)=> 
        <div className="alert alert-success show" role="alert">
          <span key={item.i}>{item.note}</span>
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
            <form>
              <div className="input-group mb-3">
                <input type="text" className="form-control form-control-lg" placeholder="New Note" aria-label="Note" aria-describedby="basic-addon2" value={this.state.value} onChange={this.handleChange}/>
                <div className="input-group-append">
                  <button className="btn btn-primary border border-light" type="button" onClick={this.handleSubmit}>Add Note</button>
                  <button className="btn btn-primary border border-light" type="button" onClick={this.handleSearch}>Search</button>
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
          {searchResults}
          {this.state.displaySearch ?
            <button className="button btn-success float-right" onClick={this.listNotes.bind(this)}>
              <span aria-hidden="true">Clear Search</span>
            </button>
          : null }
          {data}
        </div>
      </div>
    );
  }
}
export default withAuthenticator(App, { includeGreetings: true });
```
So, what did we just change? well, their isn't that many lines of code that we've added:
* <b>Lines 62-69</b> Contain a new const for "searchNote". This is just another GraphQL query like the others.
* <b>Lines 77, 80 and 81</b> add some additional variables for our scope handling.
* <b>Line 86</b> add the handle for our search binding.
* <b>Lines 118-127</b> is the new async handle for our search</b>
* <b>Lines 133, 139, 146-151, 154, 155 and 184-189</b> Handle the display of the notes
* <b>Lines 61, 165 and 166</b> add the search button to our form.

4. And we're ready to test. Like before, we can test our new code by using the Amplify CLI.
```bash
amplify serve
```

You can now log in create some sample notes and then try to search for it.
![Test Notes](/images/step5-2_test_notes.png)
![Search](/images/step5-3_search.png)

## Deploy search function
By following the steps outlined in the [Application Development](ApplicaitonDevelopment.md) part of the workshop you should be able to commit your code back to your "dev" branch.

Once you've done that, create a pull request to the "test" branch as outlined in [Trigger Deployment](TriggerDeployment.md) and once you approve it, the new Elasticsearch functionality will be deployed into "test".

Finally, you can perform the same tasks to move it into Production.

## What to from here?
And that's it, you now have a fully working Multi-Tenant Notes Application with the beginnings of an automated deployment pipeline. But where can you go from here? well, their are a number of things that could be done to expand the functionality of the solution and some are listed below for reference:

* Customize the login screen and functions - https://aws-amplify.github.io/docs/js/authentication#customize-your-own-components
* Add Federation to allow social logins - https://aws-amplify.github.io/docs/js/authentication#identity-pool-federation
* Enable MFA - https://aws-amplify.github.io/docs/js/authentication#enabling-mfa
* Leverage Amazon Pinpoint for application analytics - https://aws-amplify.github.io/docs/js/analytics#analytics
* Add a service worker to support offline experiences - https://aws-amplify.github.io/docs/js/service-workers#service-workers

