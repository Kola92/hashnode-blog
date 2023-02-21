# Demystifying React 18

## Introduction
React 18 is a major improvement in the rendering mechanisms of React. The focus was on improved performance of user interfaces for a much better experience for the user on the website.

The newly added features are completely user-centred, to make sure users don’t wait too long before interacting with the website, which translates to significantly less bounce rate on the site, more lead conversion and increased revenue for businesses. This is a win-win for users and businesses, everybody is happy and the world is all the better for it😀.

## Migration to React 18
Upgrade to React 18 only applies to React projects built before React 18 was released. Projects created after React 18 release do not need to bother about a manual upgrade, as the React project will automatically come with the new React 18 features.

To begin with, React and ReactDOM has to be re-installed in the project to get the new APIs and the concurrent rendering mechanism (we will talk about this soon enough) that comes with React and ReactDOM.

```
// NPM install
npm install react react-dom

// yarn install
yarn add react react-dom
``` 
To start enjoying the new shiny features that were shipped with React 18, we need to bring into the project the new root API. The new root API is the main feature that introduces the concurrent rendering mechanism, which is the coolest thing about the React 18 upgrade if you ask me.

```
// Before React 18
import React from 'react'
import ReactDOM from 'react-dom';
import App from 'App';

ReactDOM.render(
  <React.StrictMode>
    <App />
  </React.StrictMode>,
  document.getElementById('root')
)


// After React 18
import React from 'react'
import { createRoot } from 'react-dom/client';
import App from 'App';

const root = createRoot(document.getElementById('root'))

root.render(
  <React.StrictMode>
    <App />
  </React.StrictMode>
);
``` 
[Rick Hanlon’s discussions](https://github.com/reactwg/react-18/discussions/5) on the differences between the two root APIs stated that the Legacy Root API is the API we have been using to create our web application’s root pre-React 18 release, and the New Root API is the upgraded API that introduces the most efficient and functional performance on React projects’ rendering mechanism yet.

The **root** in React is like a container that houses all the elements/nodes/components in a React project. It is a top-level data structure that serves as a reference point from which the rest of the components in the DOM tree are descended.

## Concurrent Rendering
According to the Merriam-Webster dictionary, concurrent means ***operating or occurring at the same time***.

Concurrent Rendering is a rendering mechanism which involves making urgent user actions (like clicking and typing) a priority in the UI rendering process. This means any less urgent task on the UI can be interrupted and put on hold in its rendering process while the more urgent task takes precedence.

Dan Abramov’s [*concise explanation of concurrency*](https://github.com/reactwg/react-18/discussions/46#discussioncomment-846650) is a perfect analogy to comprehend this workflow. Imagine this scenario; you are having a chat with your friend, and unexpectedly a message was sent by your boss, the tone of this message seems urgent. The boss needs some information sent to her immediately. Well, what do you do? You tell your friend to wait because you need to send something urgent to your boss. Moments later you are done with the task your boss requested, and then you resume chatting with your friend.

Concurrency in this context allows you to put your friend on hold and respond to your boss, which is an efficient way to handle communication with multiple parties at the same time. Non-concurrency on the other hand makes sure you conclude the conversation with your friend before responding to your boss, which is not ideal. I can assure this course of action can get you fired.

The messages sent in this case are the state updates that happen behind the scenes, say a website a loading all DOM nodes in the component tree and a user wants to navigate to the contact page from the navigation bar, this latest user action will take precedence over the rendering of the component tree at the moment, the rendering process will be interrupted and the website will display the contact page accordingly.

The debut of concurrency brought about the reveal of the transition feature when the transition feature is implemented.

## Transitions
Transitions are a massive upgrade for React in handling slow user interactions in the user interface due to high latency in the network connection of the end user or the complexity and large size of the data packets that go through the TCP (Transmission Control Protocol) connection layer.

With that said, it is vital to understand how big a problem transitions are solving. Until React 18, the rendering process regarding state updates has been done indiscriminately by the React engine. However, with the debut of the concurrent rendering mechanism, as we discussed earlier transitions can work perfectly as the transition feature was built to work with concurrency.

React 18 now provides an API called ***startTransition***, this API provides the option of prioritizing the urgency of user interactions in the user interface. Users’ actions like clicking, typing or pressing can be prioritized as urgent over less urgent tasks like UI changes such as the display of search results.

For instance, you visit a website that displays software development jobs and decides to search for frontend jobs, intermediate roles, and remote & hybrid jobs. Let’s say these filters as represented as checkboxes on the UI, when you click these checkboxes you would expect them to respond by displaying a checkmark in the box, although it is okay for the search results to take a few moments to display.

***startTransition*** API helps improve the user’s experience on the UI by making their interactions intuitive. Imagine you are trying to open the Twitter app on your phone, the moment you tap on the app you would expect it to respond by showing the splash screen, at this point, you would expect the display of UI content to take a moment.

However, on the flip side if you were to tap on the app it takes a while to show a splash or some sort of visual feedback to affirm your tap action you will assume your phone is frozen right? I wager, if only the Twitter app keeps acting this way, it won’t take long before you remove it from your phone.

This is the problem transition is solving, making sure the user interface doesn’t freeze due to response to multiple state updates based on user actions. To enjoy the transition feature, wrap less urgent tasks, like UI transitions from one view to another, inside the ***startTransition*** feature.

```

import { UpButton, DownButton } from "./Icons";
import { useEffect, useState, useTransition } from "react";
import Spinner from "./Spinner";

const BOOK_URL = "http://localhost:3000/books.json";

const Book = ({ title, author, publicationYear }) => {
  return (
    <div className={styles.book}>
      <h2 className={styles.bookTitle}>{title}</h2>
      <p className={styles.bookDescription}>
        Published by <strong>{author}</strong> in <em>{publicationYear}</em>
      </p>
    </div>
  );
};

const Transition = () => {
  const [books, setBooks] = useState(null);
  const [search, setSearch] = useState("");
  const [loading, setLoading] = useState(false);
  const [startTransition, isPending] = useTransition();

  // Handle search change
  const handleChange = (e) => {
    let lowerCaseSearch = e.target.value.toLowerCase();

    setSearch(lowerCaseSearch);
  };

  useEffect(() => {
    setLoading(true);
    setTimeout(fetchBook, 2000);
  }, []);

  const fetchBook = async () => {
    try {
      const res = await fetch(BOOK_URL);
      const data = await res.json();
      setBooks(data.books);
      setLoading(false);
    } catch (err) {
      console.log(err);
    }
  };

  // Filter books by search query
  const filteredBooks = books?.filter((book) => {
    /* 
      Relegate the transition of the UI based on the 
      filtering process to the background
    */
    startTransition(() => {
      return (
        book.title.toLowerCase().includes(search) ||
        book.author.toLowerCase().includes(search)
      );
    });
  });

  /* 
    Render Spinner if fetching data or state update
    will take a while
  */
  if (isPending || loading) return <Spinner />;

  return (
    <div>
      <header className={styles.header}>
        <input
          type='search'
          id='search'
          placeholder='Search books...'
          className={styles.search}
          onChange={handleChange}
        />
      </header>
      <main>
      
        {/* Render books */}
        {filteredBooks && (
          <div style={isPending ? styles.pending : styles.done}>
            {filteredBooks.map((book, index) => (
              <Book
                title={book.title}
                author={book.author}
                publicationYear={book.publicationYear}
                key={index}
              />
            ))}
          </div>
        )}
      </main>
    </div>
  );
};

export default Transition
``` 
Let’s say we want to create a page where books coming from an API url can be displayed. This page will have a search field where the value typed into it will filter the list of books displayed on the page. Now assume that the amount of data coming from the API is ginormous and the network connection (get the data from the server the API is stored) is very slow. This means while the user is typing in the input field, the letters would be slow to appear.

This is because the React engine is rendering both the filter query state update and the transition of the UI based on the characters typed into the input all at the same time. Doing this will result in a lag in the webpage performance, developers have always handled this in the past with [debouncing and throttling](https://blog.webdevsimplified.com/2022-03/debounce-vs-throttle). But even with these concepts state updates will still take a certain amount of time (based on the ***setTimeout*** delay value) before a user action reflects on the page. This kind of delay is very crucial regardless of how short it is, it leaves the user thinking the web page is broken.

In our case, all we need to do is just wrap code that filters the book lists with ***startTransition***.

```
// Filter books by search query
const filteredBooks = books?.filter((book) => {
  /* 
    Relegate the transition of the UI based on the 
    filtering process to the background
  */
  startTransition(() => {
    return (
      book.title.toLowerCase().includes(search) ||
      book.author.toLowerCase().includes(search)
    );
  });
});
``` 
Now you might wonder how you may show the user a visual clue that the data they requested is on its way, loading in the background. React 18 provides another feature called ***isPending***, which we can pull from the ***useTransition*** hook API along with the ***startTransition*** feature.

```
import { useTransition } from 'react';
import Spinner from './Spinner';

const { startTransition, isPending } = useTransition();

// Filter books by search query
const filteredBooks = books?.filter((book) => {
  /* 
    Relegate the transition of the UI based on the 
    filtering process to the background
  */
   startTransition(() => {
     return (
       book.title.toLowerCase().includes(search) ||
       book.author.toLowerCase().includes(search)
     );
   });
});

/* 
  Render Spinner if fetching data or state update
  will take a while
*/
if (isPending || loading) return <Spinner />;

{/* Books should be rendered here */}
{filteredBooks && (  
  <div>
    {filteredBooks.map((book, index) => (
      <Book
        title={book.title}
        author={book.author}
        publicationYear={book.publicationYear}
        key={index}
      />
    ))}
   </div>
 )}
``` 
With the ***useTransition*** hook API, we pulled in ***isPending*** then we implemented a conditional rendering to handle situations when the filtering process or pulling data from API on the backend might take a while. The ***isPending*** feature is mainly to optimize the user experience so that they don’t have to be wondering what is happening in the background.

## useId
***useId*** hook API was created mainly to help developers build better accessible websites for more inclusivity in the usage of websites. Also to tackle the issue of inconsistency in client and server rendering of the component tree.

When React is displaying the UI to the user it works with a process called [**hydration**](https://github.com/reactwg/react-18/discussions/37), this means the server spills out the components as dry HTML and then the client (browser) hydrates the HTML with the water of interactivity via the event handlers coming from JavaScript. For this hydration to work appropriately, the UI output on the browser has to match the HTML output from the server. useId helps to solve these challenges pre-React 18.

With useId, we can generate unique IDs for accessibility attributes whenever we are dealing with form controls.

```

import { useId } from "react";

const FormControl = () => {
  // Generate unique IDs for the input fields
  const formControlId = useId();
  const passwordHintId = useId();

  return (
    <form>
      <div>
        <label htmlFor={`${formControlId}-firstName`}>First Name:</label>
        <input id={`${formControlId}-firstName`} type='text' />
      </div>

      <div>
        <label htmlFor={`${formControlId}-lastName`}>Last Name:</label>
        <input id={`${formControlId}-lastName`} type='text' />
      </div>

      <div>
        <label htmlFor={`${formControlId}-password`}>Password:</label>
        <input
          id={`${formControlId}-password`}
          type='password'
          aria-describedby={passwordHintId}
        />
      </div>

      <p id={passwordHintId}>
        The password should contain at least 6 characters
      </p>

      <button type='submit'>Submit</button>
    </form>
  );
};

export default FormControl
``` 
The ***htmlFor*** prop on the label elements aids screen-reader users in accessing form fields easily because the screen-reader will read out loud the label when the user focus on the input field. To ensure no two label/input groups are sharing the same ids, we implement the useId hook.

The ***useId*** is also used to create a unique id value for the ***aria-describedby*** prop which allows us to provide rich and descriptive information about the password input field for screen-reader users. The id value returned from ***useId*** is something like ***:r1:*** or ***:r2:***

*One last thing, do not make the mistake of using the useId value as a substitute to generate keys in a list.*

## useDeferredValue
***useDeferredValue ***hook API is another feature that works similarly to ***startTransition***, which is regarding the way state updates are handled gracefully to optimize the user’s experience. ***useDeferredValue*** takes any value passed to it as an argument, returns the previous state of the value while the rendering process is going on in the background then displays the new state of value on the UI the moment the rendering process is completed.

It defers display of the new state until rendering is complete, while the rendering is happening it shows the previous state to the user. This deferred value can trigger visual feedback for the user to keep track of the rendering taking place in the background.

```
import { useDeferredValue } from 'react'

function DeferValue() {
  /*
    The current query is controlled by some state that we don't control. It may even be managed 
    by an external store
  */
   const query = useSearchQuery();

   /*
     We don't need the suggestions to update immediately. We just need to keep showing
     the previous results until the new results are accessible
   */
   const deferredQuery = useDeferredValue(query);
      
   return (
     <>
       {/* The search input updates immediately */}
       <SearchInput query={query} />

       {/* The suggestions update in a later frame */}
       <Suspense fallback="Loading results...">
         <SearchSuggestions query={deferredQuery} />
       </Suspense>
     </>   
   );
}
``` 
***useDeferredValue*** might seem very similar to startTransition, but there is one major difference:

startTransition is used when triggering an update (i.e. ***setState***) based on a change in an event that our code can control like ***onClick***, ***onChange***, ***onFocus***
useDeferredValue is used when receiving new data from a parent component or based on an event change controlled by an external store like a change in url query or parameter.

## Automatic Batching
Batching in React aid in grouping two or more state updates into one re-render. This creates a much better performance of React projects ultimately optimizing the user’s experience.

With React 17 multiple state updates are only batched when placed in event handlers, anything outside React event handlers is not batched which would have led to unnecessary re-renders. React 18 takes care of this issue by automatically grouping any set of state updates into a re-render in one fell swoop.

```

function AutoBatch() {
  const [count, setCount] = useState(0);
  const [flag, setFlag] = useState(false);

  // Before React 18: only React events were batched.
  setTimeout(() => {
    setCount((prevCount) => prevCount + 1);
    setFlag((prevFlag) => !prevFlag);
    // React will render twice, once for each state update (no batching)
  }, 1000);

  /*
  After React 18: updates inside of timeouts, promises,
  native event handlers or any other event are now automatically batched.
*/
  setTimeout(() => {
    setCount((prevCount) => prevCount + 1);
    setFlag((prevFlag) => !prevFlag);
    /* 
    React will only re-render once at the end by grouping
    the count & flag state updates
  */
  }, 1000);
  return (
    <div>
      <h1 style={{ color: flag ? "blue" : "black" }}>{count}</h1>
    </div>
  );
}
``` 
Imagine a scenario where you went out to the nearest grocery store to purchase milk, you came back home and immediately you arrived home you remembered you forgot to get some eggs, and then back at home again you did not remember to buy the peanut butter spread. I think we can all agree going back and forth to the grocery store in one day is pretty inefficient and unproductive. However, let’s say before heading to the store you decide to sit down and write down all the items that you need to procure for that day or the next. Afterwards, you head to the store with your head held high confident of what you want and your item list. This will save you time, money and energy going back and forth.

It’s the same concept as how automatic batching works and the problem it solves, which is preventing wasted re-renders.

## Suspense
React 18 came with a major upgrade in how the [*<Suspense/>*](https://github.com/reactwg/react-18/discussions/37) component feature works. Now it’s not only meant for code splitting with *React.lazy* but integration with the newly improved Server-Side Rendering (SSR) architecture for React.

*<Suspense/>* is now used to solve critical problems in the server-side rendering process, which involves:

- Fetch data for the entire app from the server.
- Render that data to HTML and send it in the response.
- Load the JavaScript code from the client for the entire app.
- Connect the JavaScript logic to the server-generated HTML for the entire app (this is **hydration**).

For the whole process outlined above to work smoothly, each step needs to be completed before the next step can pick up. In such instances, UX will be affected causing a sort of bottleneck in the whole operation. This is where *<Suspense/>* comes in to combat this SSR problem. It does this by breaking each stage in the flow apart so that there is no need for the next stage to rely on the preceding stage to execute its part in the flow.

With React 18, two critical SSR features are made available by *<Suspense/>*:

- Streaming HTML on the server.
- Selective Hydration on the client.

![A visual representation of streaming HTML](https://cdn.hashnode.com/res/hashnode/image/upload/v1665939597572/iG47Ke2oD.png align="left")

**Streaming HTML** on the server simply means the server will keep sending HTML-rendered data to the client (browser) even if some components of the entire app might be experiencing a lag in transfer due to a slow database or API layer, which might be out of your control. Let’s say we are building an app that renders a post with comments, so instead of waiting to collect all the data for the app on the server before sending any HTML to the client. We can decide to implement the *<Suspense/>* SSR feature, which streams the HTML output from the server to the client for display on the UI while the comment component can be fetched in the background and replaced by a Spinner fallback placeholder until all the comment data are available for the user’s view.

```
<Layout>
  <NavBar />
  <Sidebar />
  <RightPane>
    <Post />
    <Suspense fallback={<Spinner />}>
      <Comments />
    </Suspense>
  </RightPane>
</Layout>
``` 
Wrapping the comments component with the *<Suspense/>* feature helps us save face from our users, which guarantees they will be back to view more posts on our web app 👌.

**Selective Hydration** allows your users to start interacting with components on your website even though the JavaScript code for some components might not be available yet. This prevents awkward situations where users have to wait for your web app to complete its entire rendering process before they can start interacting with it.

With *<Suspense/>* the browser can selectively hydrate any component whose JavaScript code is accessible and hydrate the bare-bone HTML output with functionality and event handlers that makes the app come alive. The moment the JavaScript code for the comments component is obtainable, then the browser starts hydrating the component for the web app interactivity.

![Selective Hydration](https://cdn.hashnode.com/res/hashnode/image/upload/v1665939815076/vk4ddXqgz.png align="left")

## Conclusion
The release of React 18 is already a game changer in how developers build digital products and quality of experience for the users. Kudos to all the contributors and maintainers of this fantastic library, they made sure nothing was taken away from the DX (Developer Experience)and UX (User Experience), and instead added much more to it.

Concurrent rendering mechanism, automatic batching and improving on SSR architecture prove React is getting smarter and better as it matures. I am here for React growth.

Now let me go start conceptualizing an exciting project that will enjoy all the joys React 18 brings to the interwebs 😉.