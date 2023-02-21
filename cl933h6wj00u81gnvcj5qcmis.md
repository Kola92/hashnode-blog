# Redux Toolkit: A More Concise Approach To Dealing With State Management

Redux is one of the major state management tools used in efficiently dealing with states through single-page applications. Single Page Applications also known as SPAs have revolutionized the way websites on the internet are now built. These type of applications optimizes website load times, achieved as a result of the dynamic reload of the data from the server instead of a full page reload experience by users in the past.

JavaScript frameworks and libraries like React, Vue & Angular implement state management for efficient data flow all throughout the application.

1.  **Redux & MobX** are used for **React** state management.
2.  **ngrx/store** for **Angular** state management.
3.  **Vuex** for **Vue** state management

## What is State Management?

State Management deals with an efficient way to deal with the state in SPAs. There are only two to work with data in React: state and props. **State** in React is the current data contained in a component at a certain moment in time in the lifecycle of a React web application. **Props** provide a way to share data (i.e. state) with another component.

A web application such as Twitter uses React on the frontend, say you want to search for a person, topic or keyword, and each feature of the app is fragmented into reusable components like the search functionality, so this means the search component is separated from other components like your Twitter home page. Getting the search term from the component that displays your home page would need a sort of communication between the search bar component and the home component so that the search term data can be made available to the home component.

Analyzing the situation will cause some headaches, though it can be solved by creating a functional component of the search functionality in the home page component, and then lifting the state to the parent component, the home component, but this means the search component won’t be isolated as a reusable component, also solving the problem will create a sort of a busy and inefficient code.

This is where **Redux** comes in to ensure a much cleaner code. Redux does this by allowing you to create a global store where changes in data are available all across the application, whether the home, search or any other component. Redux makes it possible for all the components in the codebase to be able to keep up with all the state changes.

## Difference between Redux & Redux Toolkit

The difference between **Redux** and **Redux Toolkit** is kinda synonymous with a comparison between vanilla JavaScript and React Vue or Angular. Redux Toolkit helps developers write less Redux logic and also implements minimal packages when configuring the Redux store. It helps developers save time and energy and aids in maximal efficiency.

In the times past, configuring Redux has always been a hassle for developers especially when productivity is what watchword for projects that need to be built real quick. Redux Thunk is a package that was usually a nightmare for developers to implement, due to the time and effort it takes to get it up and running. It is a middleware that handles asynchronous requests and responses between the action creator function and reducer after an action is dispatched. With the Redux Toolkit, an API called configureStore is used to configure the Redux store and with comes a thunk middleware by default.

One less package in your codebase helps make up a very lightweight build which in turn optimizes the web application performance for a better user experience.

In one sentence: Redux Toolkit abstracts away most of the complicated aspects of Redux.

## Merits of Redux Toolkit

Redux Toolkit comes with a bunch of APIs to ease developers’ workflow. Let’s try to understand the APIs that do most of the heavy lifting:

1.  **configureStore():** we mentioned this one briefly, it wraps around the **createStore** API for Redux and combines reducers, so instead of using the **combineReducers** helper function from Redux we just include all our reducers as an argument for the **configureStore** API to reduce the reducers(see what I did there😎) into one single function. Plus the middleware it provides by default amongst which is the thunk middleware.
2.  **createSlice():** in addition to the **configureStore** API, this API is among the most used APIs when developers are implementing Redux Toolkit. It accepts the initial state and reducers object as arguments, inside this reducers object slice reducer function logic can be implemented plus the action types which are by default gotten from the syntax (name/action reducer function name). createSlice will join the ***name*** argument and ***slice reducer function*** in the code below to generate an action type like this: *name/action creator function.* In the case of the two creator functions below, their respective action types will be ***posts/postAdded*** and ***posts/postUpdated.*** Redux Toolkit generates these action types and the action creator functions automatically from the slice reducer function, which saves us the stress of having to implement an action creator function logic and action types.

![createSlice logic implementation](https://cdn.hashnode.com/res/hashnode/image/upload/v1665425720038/0qnET2dfs.png)

3\. **createAsyncThunk():** This API accepts two arguments: an action type string and a payload creator callback function that makes asynchronous AJAX requests to the backend API and then returns a promise containing some data which will be the payload.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1665425721715/e8Le-DkuY.png)

Aside from the APIs explained, there are more Redux Toolkit APIs that can be used on any of your future projects, like the Redux Toolkit Query which is a great feature in Redux Toolkit that is used for fetching and caching data coming from an API server. Caching of data from the backend helps to optimize the website performance by storing the fetched backend data on the user’s computer memory so that when next they want to access the data it loads immediately without the need for another HTTP GET request to the server.

## Conclusion

A lot of developers are already implementing Redux Toolkit in their web applications for maximal efficiency in state management across the entire application. Redux Toolkit is de going to get better as time progress, and one thing is for sure it has changed the workflow involving state management for the better.