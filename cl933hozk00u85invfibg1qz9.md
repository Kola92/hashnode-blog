# Using Redux Toolkit to Handle Asynchronous Data Requests

Before the arrival of the Redux Toolkit (initially named Redux Starter Kit) in October 2019, fetching data asynchronously from the backend via Redux is always too much of a hassle. Developers had to settle with the Redux Thunk middleware package to handle asynchronous logic, which involves quite some amount of boilerplate code to be set up and some installation of packages to be made before executing the async logic.

Redux Thunk middleware is a function that intercepts actions dispatched from the system, triggered by users’ actions on the interface like clicking a post button, and checks if the action is a function, if so it calls that function by returning it. The function, in this case, is an asynchronous function returning a promise, once the promise is resolved or rejected as the case may be, an appropriate action creator will be dispatched to the reducer which ultimately conveys a response back to the component the action was initially executed on.


```
// async action creator which returns a function as an action
const addPost = (userId, post) => async (dispatch, getState) => {
  try {
    const response = await fetch('https://jsonplaceholder.typicode.com/posts', {
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({ userId, post })
    });
    
    const data = await response.json();
    dispatch({ type: 'ADD_POST', payload: data });
  } catch(error) {
    console.log(error.message);
  }
}
``` 


The code syntax above is a perfect example of how the Redux Thunk middleware can be implemented. **addPost** function is an asynchronous action creator which serves as the middleware that returns the action function by intercepting the **ADD\_POST** action from the frontend and subsequently executes a fetch post request to the backend, and then ultimately dispatches (with the **dispatch** method) the action type and payload from the form controls on the frontend to the reducer to execute the appropriate logic for adding a post to the view. This is just a concise way of using the Redux Thunk middleware to give you an idea of how it works. But the configuration flow is whole another process in and of itself.

The configuration process of the Redux Thunk middleware is mostly unnecessarily complex and time-consuming, the time used to properly configure this library can be better used on writing the async logic.

This is where the **createAsyncThunk** API (Application Programming Interface) comes in to save the day by saving you time and energy. This powerful API abstracts away most of the unnecessary parts of the Redux Thunk middleware for better developer productivity. **createAsyncThunk** API accepts two arguments: an **action type string** and a **payload creator callback function**. The action type string argument generates corresponding action types for the Promise lifecycle; **pending**, **fulfilled**, and **rejected.** The payload creator callback function handles the asynchronous responses and requests to and from the backend and ultimately returns a Promise, the kind of Promise depends on its lifecycle status: pending, fulfilled or rejected.

To further solidify our understanding of the createAsyncThunk API, we will be building a lightweight web application that can create and read posts.

## Setup the Redux Store

Create a file named: ***postsSlice.js*** (this is where most of the Redux logic will live). ***postsSlice.js*** will contain a slice of state for the posts data coming from the API. This file also gives access to the reducer that will be added to the store and the actions we will need to dispatch on any user action (clicking the **Post** button to add a post).


```
postsSlice.js

import { createSlice, createAsyncThunk } from '@reduxjs/toolkit';
import axios from 'axios';

const initialState = {
  postItems: [],
  status: 'idle',
  error: null
}

// Get all the posts from the API
export const getPosts = createAsyncThunk('posts/getPosts', async (thunkAPI) => {
  try {
    const res = await axios.get(url)
    return res.data
  } catch (err) {
    return thunkAPI.rejectWithValue({ error: err.message })
  }
})

// Handle POST request to create a new post
export const addPost = createAsyncThunk(
  // The name of the action
  'posts/addPost',
  // The payload creator
  async (initialPost, thunkAPI) => {
    try {
      const res = await axios.post(url, initialPost)
      return res.data
    } catch (err) {
      return thunkAPI.rejectWithValue({ error: err.message })
    }
  }
)

const postSlice = createSlice({
  /* The name of the slice[this will also be used as the action type string 
    in combination with the extraReducer name i.e posts/getPosts or posts/addPost] 
  */
  name: 'posts',
  // initialState: initialState[ES6 destructuring syntax]
  initialState,
  // Add reducers for the synchronous actions on the UI[we are not using this property for this tutorial]
  reducers: {},
  // Add extraReducers for the asynchronous actions on the UI 
  extraReducers: {
    [getPosts.pending]: (state, action) => {
      // When data is being fetched
      state.status = 'loading'
    },
    [getPosts.fulfilled]: (state, action) => {
      // When data is fetched successfully
      state.status = 'successful'

      // Concatenate the new data to the existing data in the array
      state.postItems = state.postItems.concat(action.payload)
    },
    [getPosts.rejected]: (state, action) => {
      // When data is fetched unsuccessfully
      state.status = 'failed'

      // Update the error message for proper error handling
      state.error = action.error.message
    },
    [addPost.fulfilled]: (state, action) => {
      // Add the new post created on the UI to the existing posts
      state.postItems.push(action.payload)
    },   
  }
})

// Export the reducer logic from the slice
export default postsSlice.reducer
``` 


From the contents of the ***postSlice.js*** file above, you can see a lot is going on there, so I will break it down in detailed steps (also pay attention to the comments above each line of code).

1.  We import the ***createSlice*** & ***createAsyncThunk*** API from Redux Toolkit to create the slice of state for the posts coming from the API and createAsyncThunk to handle the asynchronous requests to and from the API. The ***axios*** package is to deal with the HTTP ***get*** and ***post*** requests effortlessly.

2.  Create an ***initialState*** object for the reducer to work with the first time it is called from the store. We initialized the object with properties such as ***postItems*** array, status string(‘idle’), and an error object(null).

3.  Get the posts coming from the API and handle them asynchronously. This can be done inside the ***getPosts*** function using the ***createAsyncThunk*** API we imported from the Redux Toolkit earlier. ***createAsyncThunk*** contains two arguments; action type string (***posts/getPosts***) and the payload creator function (this is where we handle the HTTP ***get*** request for the API using the axios package we imported earlier). The ***thunkAPI*** parameter is an object provided by the ***createAsyncThunk*** API to help handle rejected Promise responses with one of its properties, ***rejectWithValue.***

4.  Add new posts created from the UI (User Interface) to the API. The ***addPosts*** function handles this using the ***createAsyncThunk*** API. The syntax structure is the same as the ***getPosts*** function, the only slight difference is that we are implementing an HTTP post request to the backend API, and an ***initialPost*** parameter is added. This parameter stands for the existing posts already contained in the backend API, it’s kinda like we are using a spread operator to copy the existing posts’ state and concatenate it to the new post.

5.  A slice of state for posts is created using the ***createSlice*** API, this API creates a reducer logic and exports it as a reducer function which can be added to the store. createSlice makes it easier for developers to create action-type strings & action creators rather than manually creating them, making the code verbose. The ***name*** property declares a string value that specifies the state name: ***posts***, the combination of the name property with the function name for the ***reducers*** or ***extraReducers*** automatically produces the action-type strings: ***posts/getPosts*** or ***posts/addPosts*** and the action creators: ***getPosts*** & ***addPosts*** functions. ***initialState*** property takes in initial data the store can work with when the UI is rendered. ***reducers*** property takes in functions that handle synchronous actions dispatched from the UI while the ***extraReducers*** property (we are more concerned about this property) is used to handle asynchronous actions dispatched to the backend API, a Promise is returned for these types of actions and it needs to be handled gracefully. The Promise returned goes through the lifecycles: pending, fulfilled & rejected. With each of these lifecycles, a logic is written that handles the data flow when it is loading from the backend, when it loads successfully and when the data request fails to be loaded. This is the purpose ***\[getPosts.pending\]***, ***\[getPosts.fulfilled\]***, ***\[getPosts.rejected\]*** & ***\[addPosts.fulfilled\]***

6.  ***\[getPosts.pending\]*** method handles the logic for when the data is being fetched from the backend API, at this point the initial state for ***status*** is updated from ***loading*** to ***idle. \[getPosts.fulfilled\]*** method deals with after the data has finished loading from the backend API, the ***status*** state is updated to ***successful*** and the newly fetched data is added to the empty ***postItems*** array which will contain all the posts coming from the API. ***\[getPosts.rejected\]*** method updates the ***status*** state to ***failed*** and the ***error*** state to the error message coming from the ***thunkAPI.rejectWithValue*** line in the ***getPosts*** function created earlier. ***\[addPosts.fulfilled\]*** only deals with when a new post is successfully added to the backend from the UI, the pending & fulfilled lifecycles are already handled through the getPosts.pending & getPosts.rejected methods since the logic for pending and rejected lifecycles are the same for both actions.

7.  The createSlice API contains five properties: ***actions, caseReducers, getInitialState, name & reducer***. To initialize a redux store a reducer needs to be added to it, which will serve as the initial data the UI can work with the moment it mounts. To be able to do this, a reducer needs to be pulled from the createSlice API logic. This is achieved by exporting the ***postsSlice*** object as a reducer, ***export default postsSlice.reducer***

## Add Data to Store


```
store.js

// Pull in configureStore API
import { configureStore } from '@reduxjs/toolkit';

// Pull in the postsSlice reducer and rename it to postsReducer
import postsReducer from '../features/posts/postsSlice';


// Create the Redux store and pass in the postsReducer as the initial data
export const store = configureStore({
  reducer: {
    posts: postsReducer,
  },
})
``` 


In any web application that handles state management with Redux, without the Redux store, nothing can be achieved with Redux. It is the same as building an application without using Redux. The Redux store is what ties in everything together and it’s from the store that the UI can pull data via the reducers.

Updating the state through the application happens in the store after an action has been dispatched to the store, the reducer(s) inside can decide how to update data either by adding data to or removing it from the store based on the logic contained in the reducer function.

From the logic above, to create the Redux store, it is the ***configureStore*** API imported from the Redux Toolkit that is bringing everything together. The reducer we exported from ***postsSlice.js*** is now imported and renamed as ***postsReducer***. One of the great things about the ***configureStore*** API is that it automatically passes more than one reducer to the combineReducers utility behind the scenes. This utility was initially used to bring together two or more reducers before the arrival of the Redux Toolkit. This is one of the ways the Redux Toolkit help make the code much cleaner.

The ***posts: postsReducer*** object communicates to the application globally that the data/state coming in from and added to the backend will be consumed as ***state.posts*** on the frontend.

## Consuming State/Data on the Frontend


```
PostsList.js

import { useEffect } from 'react'
import { useDispatch, useSelector } from 'react-redux'
import { getPosts } from '../features/posts/postsSlice'

const PostsList = () => {
  const dispatch = useDispatch()
  
  // Get the posts from the store
  const posts = useSelector((state) => state.posts)

  // Pull the post properties
  const { postItems, status, error } = posts

  useEffect(() => {
    // eslint-disable-next-line no-unused-vars
    let isMounted = true

    // If status is 'idle', then fetch the posts data from the API
    if (status === 'idle') {
      dispatch(getPosts())
    }

    // Cleanup function
    return () => {
      isMounted = false
    }
    // eslint-disable-next-line react-hooks/exhaustive-deps
  }, [status, dispatch])

  let bodyContent

  if (status === 'loading') {
    bodyContent = <div className="loader"></div>
  } else if (status === 'successful') {
    // Sort the posts by id in descending order
    const sortedPosts = postItems.slice().sort((a, b) => b.id - a.id)

    // Map through the sorted posts and display them
    bodyContent = sortedPosts.map((post) => (
      <div key={post.id}>
        <h3>{post.title}</h3>
        <p>{post.body}</p>
      </div>
    ))
  } else {
    // Display the error message
    bodyContent = <div>{error}</div>
  }

  return <div>{bodyContent}</div>
}

export default PostsList
``` 


Implementing the data/state coming from the store we will need to import a couple of packages. The ***useSelector*** hook aid in selecting the specific data/state we need from the store instead of pulling everything including the ones we don’t need. This ensures our application is unnecessarily bloated affecting performance as a result. ***useDispatch*** hook helps with dispatching actions (based on users’ actions on the UI) to the store for the reducer function to handle.

Before the arrival of the ***useSelector*** and ***useDispatch*** hooks in 2019, the ***connect()*** method was used instead to wrap components that need the state/data coming from the Redux store. ***mapStateToProps*** and ***mapDispatchToProps*** functions were initially fulfilling the functions of ***useSelector*** and ***useDispatch*** respectively. To drastically reduce the boilerplate code used to connect a component to the Redux store, React-Redux released these hooks to improve developers’ experience.

We initialize the ***useDispatch*** hook for use later, ***useSelector*** is pulling the posts data from the store with ***state.posts.*** The posts data content (***postItems***, ***status***, ***error***) are pulled from the posts data we just got from the store via object destructuring.

***useEffect*** hook is used to handle the side effects as a result of the asynchronous actions dispatched to the store and the Promise returned. We declare a condition to make sure no data fetch is happening before we dispatch the ***getPosts*** action to the store, where the reducer function deals with the fetch request coming in by going through the Promise lifecycles (*pending/fulfilled/rejected*) and rendering the appropriate logic for each lifecycle. After the fetch request is resolved or rejected, we clean up our code cancelling any asynchronous tasks before the component unmounts. This prevents any sort of memory leaks.

A conditional statement is created to properly handle the actions from Promise lifecycles. When the status state is pending (the data is being fetched) then a loader is shown to the user, then when the data finish loading and is successfully fetched, the posts are displayed in UI in a reverse chronological order to ensure any newly created post is displayed first on the page. However, if the data was unable to be fetched, then the error message returned by ***thunkAPI.rejectWithValue()*** in the reducer function should be displayed instead which is something like **“Cannot read properties of undefined (reading ‘rejectWithValue’)”.**


![Posts list from the API](https://cdn.hashnode.com/res/hashnode/image/upload/v1665426506857/mC_SAkFtP.png align="left")

## Creating New Posts


```
CreatePost.js

import { useState } from 'react'
import { useDispatch } from 'react-redux'
import { useNavigate } from 'react-router-dom'
import { addPost } from '../features/posts/postsSlice'

const CreatePost = () => {
  // Set the initial state for the form
  const [title, setTitle] = useState('')
  const [body, setBody] = useState('')
  const [addPostRequestStatus, setAddPostRequestStatus] = useState('idle')

  // Get the dispatch function
  const dispatch = useDispatch()

  // Get the navigate function [replace the history.push() method]
  const navigate = useNavigate()

  // Handle form field value changes
  const onTitleChange = (e) => setTitle(e.target.value)
  const onBodyChange = (e) => setBody(e.target.value)

  /* 
    Get the Boolean value based on whether the form is empty or not && the post request status.
    We use the Boolean value returned to toggle the disabled status submit button
  */
  const canSavePost =
    [title, body].every(Boolean) && addPostRequestStatus === 'idle'

  // Handle form submission
  const handleAddPost = async (e) => {
    e.preventDefault()
    const post = { title, body }
    if (canSavePost) {
      try {
        setAddPostRequestStatus('pending')
        await dispatch(addPost(post)).unwrap()
        setTitle('')
        setBody('')

        navigate('/')
      } catch (err) {
        console.error('Unable to create post:', err)
      } finally {
        setAddPostRequestStatus('idle')
      }
    }
  }

  return (
    <div className="create-post">
      <div className="create-heading">
        <h1>Create Post</h1>
      </div>
      <div className="form-container">
        <h2>Add New Post</h2>
        <form onSubmit={handleAddPost}>
          <div className="form-group">
            <label htmlFor="title">Title</label>
            <input
              type="text"
              id="title"
              name="title"
              onChange={onTitleChange}
              value={title}
            />

            <label htmlFor="bodyContent">Content</label>
            <textarea
              id="bodyContent"
              name="bodyContent"
              cols="30"
              rows="10"
              onChange={onBodyChange}
              value={body}
            />

            <button type="submit" className="btn" disabled={!canSavePost}>
              Post
            </button>
          </div>
        </form>
      </div>
    </div>
  )
}

export default CreatePost
``` 


We go ahead to import the needed packages to handle dispatch, navigation and local state management. You might be wondering why the ***useState*** hook is been used here in this component. Well, the reason is that updating the change of value in HTML form inputs is a local state that is only needed inside the CreatePost component and nowhere else.

The ***handleAddPost*** function deals with what happens when the Post button has been clicked for submission to the backend.

First, we check if the post can be saved based on whether any of the input is empty or not and if the status is ***idle***, then the ***title*** and the ***body*** content from the input field are added to a ***post*** variable as an object.

Afterwards, the ***addPosts*** method imported from the ***postSlice.js*** file earlier is dispatched as an action to the Redux store along with the ***post*** object carrying the ***title*** and ***body*** content from the form as a payload to be added to the backend.

When all this is done the ***title*** and ***body*** input field is cleared by setting the state back to an empty string and the ***useNavigate*** hook is used to take the user to the home page displaying all the posts.

Subsequently, we handle any possible error that might come up and set the request status back to its initial state, ***idle.***

![Create Post Interface](https://cdn.hashnode.com/res/hashnode/image/upload/v1665425744412/AM8pTqBBJ.png)

![Newly Added Post](https://cdn.hashnode.com/res/hashnode/image/upload/v1665425745697/RDDf-4Dbp.png)

A new post has been added to the backend and sent back from the store to be displayed on the UI. Although the newly added post will disappear from UI when the page is reloaded, this lack of persistence of data is because the data was not added to an actual database on the backend.

## Conclusion

Redux Toolkit being an improved extension of the Redux state management has made life super easy for developers. The createAsyncThunk API helps you save a lot of time that would otherwise have been wasted in configuring so many boilerplate codes to handle asynchronous requests. That saved time can be better invested in writing code that truly matters. Please let this post be the little nudge you need to start using Redux Toolkit. Trust me you never regret it.

*PS: You can find the complete code here:* [*https://github.com/Kola92/redux-toolkit-tutorial*](https://github.com/Kola92/redux-toolkit-tutorial)