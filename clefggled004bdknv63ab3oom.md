# Nextjs 13

# **Introduction**

Next.js is a popular JavaScript framework for building server-side-rendered React applications. Next.js v13 was released by Vercel at the Next.js conference in October 2022, bringing many new features and improvements. Understanding the complexities of this latest version is essential for developers to utilize its capabilities and build efficient and scalable applications effectively. This article will cover various aspects of the new version, including its unique features, changes from previous versions, and tips for overcoming any challenges that might arise while working with it.

## **Next.js’s Significance in Web Development**

Before diving into the new shiny features of Next.js v13, let’s first explore how much of an impact Next.js has had on web development, and how the developer and user experience alike have been taken to the next level ever since v1.0 was released in October 2016. This little bit of history would also provide the appropriate perspective for developers that might just be getting introduced to Next.js for the first time.

The ability of Next.js to automatically handle server-side rendering (SSR) of React components is one of its core capabilities. This means that the server will render the first HTML and deliver it to the browser when a user visits a Next.js application, providing a fast and smooth experience for the user. This is important because it improves the time-to-first-paint (which refers to the point at which the first pixel renders on a screen after a user navigates to a web page)and overall application load time, which is crucial for user engagement and SEO.

The built-in development server of Next.js is a key component. The browser will automatically refresh to reflect changes made to the code while using the development server’s hot-reloading feature. Because developers can view their changes in real-time, the development process can proceed more efficiently.

A built-in file system-based routing system is also included in Next.js. As a result, every file in the directory of the page corresponds to a route in the application. This makes it easy to comprehend the application’s structure and create new pages without having to configure routes manually.

With a large selection of plugins and packages, Next.js has a robust community and ecosystem. This makes it simple to integrate new functionality into the application and identify fixes for frequent issues. It is a potent framework for creating React.js-based web applications. It appeals to developers because it supports server-side rendering, integrated development servers, file system-based routing, and a vibrant community. It can enhance web applications’ functionality, hasten their development, and enhance user experience.

## **Overview of Next.js v13’s upgrades and new features**

Next.js is a popular JavaScript framework that is widely used for building server-rendered React applications. The latest version, Next.js v13, brings several new features and improvements that make it even more powerful and easy to use.

The **app directory**, although still in the beta phase, has been improved upon as regards the kind of features that can be made available from its directory. Features such as:

* **Layouts** for sharing UI across pages (or routes) in an application, like having the header and the footer components persist on all page UIs. This prevents the constant rerendering of these components anytime the UI changes which can cause expensive issues in the app’s performance.
    
* **Server Components** provides an option for developers to utilize a server in rendering components by handling the heavy computation on the server-side, consequently sending a light-weight JavaScript to the client leading to optimum performance of the application in the process.
    
* **Streaming** enhances users’ experience on a page by progressively rendering content, which involves instantly displaying parts of the UI that do not require fetching data from the server while showing a loading state for sections of the page awaiting data from the server.
    
* **Data Fetching** utilizes the newly improved and extended fetch() API by providing one of its key features yet, **deduping** (or deduplication) which is an optimization that prevents the same data from being fetched more than once during a rendering pass.
    

Next.js v13 also replaced the famous bundling and building tool, Webpack with a Rust-based bundling tool known as, **Turbopack**, it’s currently the most efficient build tool out there as regards transpiling and launch time.

The introduction of the new Image Component is a more improved version of the old `next/image` component. With this component, Cumulative Layout Shift (CLS) can be eliminated by automatically determining the `width` and `height` of your image based on the imported file. This can boost the SEO performance of your website.

Another improvement is the `@next/font` component introducing a new font system. This font system is heavy on optimization and security by reducing network requests made to the Google fonts server, this will aid in faster load times and top privacy for Google font API.

`next/link`component does not contain any significant upgrade besides the retention of the experimental option introduced in v.12.2, which is eliminating the need to manually add the `<a>` tag inside the &lt;`Link>` component tag.

## **app Directory**

Although this feature is still in the beta phase it’s one of the significant upgrades that came with v.13. Developers are still at liberty to use the old routing system with the pages directory file system, this is for a progress adoption of the new routing system that comes with the app directory file system.

![App & pages directory file system side-by-side](https://miro.medium.com/max/770/1*vp0l52JqMHLLLrSkaMF-OA.png align="left")

The app directory contains folders and files, where the folders are used to define the routes for the application relative to the root folder (app directory). Files on the other hand handle the UI which displays to the user contents for consumption or interaction.

![Folder-based routing system](https://miro.medium.com/max/770/1*3IN_Q9H_mK41aLfRNbV_iQ.png align="left")

From the illustration above, we can see that the URL path for the routing system is based on the folder names as opposed to the files' names in the old page directory system. With this type of file structuring, nested routes are easy to implement.

Each folder that is a child of the app directory is known as a **route segment** and the child of the route segment is called a **leaf segment**, only if the child does not have a child of its own. Say the **settings** folder above was to have a folder named **theme** nested inside it, the settings folder would become a route segment and the theme folder becomes the leaf segment since it has no child of its own.

The file hierarchy structure was modelled after a tree layout for a simpler and relatable analogy for developers to grasp the concept. The matriarchy, in this case of the app family, is the app folder which is known as the **root segment**. Let’s continue with our addition of the theme folder inside the settings folder, say we want to navigate to the theme page, our URL path will become [`acme.com/dashboard/settings/theme`](http://acme.com/dashboard/settings/theme) where the `/` after the domain name ([`acme.com`](http://acme.com)) represents the root segment (app folder), then `dashboard/` represents the dashboard folder, `settings/` represents the settings folder and `theme` ultimately depicts the contents of the theme page.

Every folder must contain a file known as **page.js**, this is the file that makes it possible for users to be able to view the contents of a route. Without the page.js file if we navigate to [`acme.com/dashboard/settings/theme`](http://acme.com/dashboard/settings/theme) the theme page will just be blank without any content.

![page.js is always the leaf segment for any route segment](https://miro.medium.com/max/770/1*wcBckcpdF5oCOJxyhqhZVw.png align="left")

%[https://gist.github.com/Kola92/e2bc879a11b97fe4913c29c6377ff501] 

More files can be added to each folder or route segment, each of these files is named according to their function in the project relative to their parent folder:

* `layout.js` contains the UI for elements and contents that are common to a route segment and its children. This file eliminates the redundancy of having to repeat certain components or contents on every page of a segment and its descendants.
    

![layout.js file in the app directory](https://miro.medium.com/max/770/1*MhHxGf9Qc0C6MIQGI6laig.png align="left")

%[https://gist.github.com/Kola92/db1a37e6e8e3abceb5c13a21438fecd5] 

* `loading.js` encompass the UI for the loading state when data is being fetched from the server. React 18 Suspense feature makes this possible to implement to boost your user’s experience. It wraps a route segment and its descendants in the [React 18 Suspense Boundary](https://beta.reactjs.org/apis/react/Suspense#suspense).
    

![loading.js file in the dashboard route segment](https://miro.medium.com/max/770/1*yOpyiZIPDpF7i49MCvHxmw.png align="left")

%[https://gist.github.com/Kola92/9c69efb9c76d277449693192e5b6573a] 

* `error.js` handles the UI content for scenarios where errors are caught may be due to a wrong request or bug in code. This file will contain the design for the content to display to users with necessary contextual information. It wraps a route segment and its descendants in the React Error Boundary feature.
    

![error.js file in the dashboard route segment](https://miro.medium.com/max/770/1*CGtPZNrTwtSNZq5wfcWlJQ.png align="left")

%[https://gist.github.com/Kola92/4e8476bab0ab1be72ad864b83f09224b] 

* **not-found.js** handles the display of a 404 error page, this file invokes the notFound function which injects a `<meta name="robots" content="noindex" />` tag and throws an `NEXT_NOT_FOUND` error and also terminates rendering the UI of the route segment it is contained.
    

%[https://gist.github.com/Kola92/99ecffb16214275d15ff58c39f45434b] 

### **Layouts**

Layout in Next.js is a UI shared between several pages or children files contained within its route segment. For example, in the image below based on the file structure, we can see that there is a`layout.js` file in both the app directory and its child directory, **dashboard**. So what happens here is that the first `layout.js`layout.js file which is known as the **root layout** will wrap all the pages in the application and subsequently share a user interface, that your users can interact with, across the pages in the application.

The dashboard route segment can also have its own `layout.js` file like in the image below, this file is known as a **nested layout.** This layout will be shared across all pages in that route segment, in this case, the dashboard directory. A parent layout wraps child layouts (or nested layouts) below it using the React `children` prop.

![Nested Layout in Root Layout](https://miro.medium.com/max/770/1*NUxDNccencXtfUwh10Niiw.png align="left")

Root layout must contain `<html>` and `<body>`tags since all the pages for the application will be rendered inside this layout, and Next.js does not automatically create these tags.

%[https://gist.github.com/Kola92/bd8197b9681c2bc57c629ecf92b9d081] 

![Visualization of nested layouts](https://miro.medium.com/max/770/1*IUOpt_DVBdsFb2j_rZMJTw.png align="left")

From the visual representation, we see how the parent layout, and root layout, wrap the dashboard layout below, and then the dashboard layout wraps all the route segments contained within its parent folder.

Layouts eliminate redundancy in code, developers won’t have to be repeating HTML syntax for header and footer on all pages when it can just be defined in one file and then replicated across the whole application UI.

### **Server Components**

The introduction of hydration in [React 18](https://medium.com/bitsrc/demystifying-react-18-155f51a4593d) helps improve the performance of the application as a result of the rendering environment. The rendering environment pre-React 18 has been via the client solely which led to a considerable amount of JavaScript being sent to the browser for computation. With Next.js 13, you can choose your rendering environment either client or server.

The combination of hydration and rendering components through the server will ensure that the browser only needs to download the HTML markup and styles and then allow the server to hydrate the non-interactive page with the JavaScript to make the page come to life by making it dynamic and interactive, consequently resulting to a swifter load time of the page.

By default, all the components found in the app directory — including layout, loading, and error files among others — are React Server Components. This means you can start enjoying the benefits of using server components without having to do any configuration to activate them. Although you will have to upgrade your Next.js application to version 13 by running the command: `npm i next@latest react@latest react-dom@latest eslint-config-next@latest`in your command line.

Hydration is when the server spills out the components as dry HTML and then the client (browser) hydrates the HTML with the water of interactivity via the event handlers coming from JavaScript, this means the user doesn’t have to wait for all the JavaScript files to be pulled from the server to view UI of the page. The page UI design as a result of HTML & CSS will be displayed first, while the UI is displaying the browser is asynchronously pulling the JavaScript files to start hydrating the static HTML & CSS with interactivity made possible with JavaScript event handlers. At this point, the user can start interacting with the page.

With Server Components, the initial page load is super faster, and the JavaScript bundle size in the browser is reduced or even almost nonexistent. The JavaScript that would be present in the browser is *only added* as client-side interactivity is used in your application through Client Components.

### **Streaming**

Streaming is a sort of derivative feature of the React 18 [Suspense](https://beta.reactjs.org/reference/react/Suspense) feature. This feature was released to optimize users’ experience as they are viewing a page. It allows developers to progressively render content to their users while pulling asynchronous data from the backend server.

A loading state can be displayed to the user in sections of the page that are waiting to be populated with data from the server while the other sections that have static content, mostly HTML & CSS, will be rendered to the user, allowing for instant interaction. The streaming mechanism can be compared with the hydration mechanism in React 18, these features are interwoven with each other and combine to provide the finest experience for users and developers alike.

The loading state or UI is handled with the `loading.js` file we talked about earlier that can be found in each Route Segment.

![Data Streaming visual](https://miro.medium.com/max/770/1*J6bP_7f18YkmHHktAzaPBA.png align="left")

### **Data Fetching**

Next.js v13 extends the `fetch` API to achieve a more efficient way to fetch data from the server using Server Components. The `fetch` API was customized to fit the new React rendering environment, Server Components which, piggybacking off what we talked about it earlier is a rendering mechanism where JavaScript and asynchronous data are pulled from the server reducing its bundle size in the client.

Data Fetching with Server Components aids in a lot:

* Pulling data directly from the backend without the need for the browser to fetch requests.
    
* Discarding any concern regarding the exposure of sensitive information, such as access tokens, user credentials, payment information and API keys among others, via the client.
    
* Fetching and rendering data in one environment, server eliminating the need for multiple roundtrips or back-and-forth between the client and the server.
    

One of the top-selling points for Next.js v13 is ***deduping*** or ***deduplication***, which is eliminating ***duplicates*** of a `GET` request for data that is needed in a component tree. For example, a user’s data is needed in the header, home and user profile components, and these are sharing a common parent layout which ties them all together, even though each component will be fetching the same data individually, however, behind the scenes, the first `GET`request data will be cached to prevent fetching the same user’s data more than once which would cause some performance issues.

![Deduplicated fetch requests](https://miro.medium.com/max/770/1*6QRPA2R-K741rOT5oyCA1Q.png align="left")

Next.js v13 `fetch` API merges the best features of Static Site Generation (SSG), Server-Side Rendering (SSR), and Incremental Static Regeneration (ISR), and now these awesome features are available through one API, hmm…how glorious 😎.

%[https://gist.github.com/Kola92/d567e9ef15e213d6cb79c76620419245] 

## **Turbopack**

[Turbopack](https://turbo.build/pack) is a derivative of the top module bundler, **Webpack**. Like everything in life, there comes a time when the top dog becomes the underdog or the first gets relegated to the second position, one can be at the top forever there is always someone or *something* hungrier and more innovative. Well, this is the case of the successor to Webpack, Turbopack, built by the creators of Webpack and Next.js at Vercel, utilizing the power of Vercel’s build system, [**turborepo**](https://turbo.build/repo)**.**

It is a next-generation incremental bundler with out-of-the-box support for TypeScript, JavaScript, JSX, CSS, CSS Modules, WebAssembly, and more. Also optimized for various environments such as Browser, Server, Edge, SSR, React Server Components & Client Components.

Turbopack is 11x faster than Webpack and 6x faster than the Vite module bundler, for Hot Module Replacement (HMR) when files are edited. Regarding cold starting the dev server, Turbopack is 7x faster than Webpack and 3x faster than Vite.

Currently, it is in its alpha phase where developers can experiment with it and experience its unparalleled performance, considering the module bundler is not stable yet be cautious with the type of project you experiment with it. It goes without saying not to use it on corporate and business projects that a lot of users use daily. Although, developers can use it on personal projects just to explore the benefits it provides.

Turbopack can be used in Next.js v13. Release of a standalone CLI, plugin API and support for other frameworks such as Svelte and Vue would be included in future upgrades. Next.js v13 project can be created with Turbopack with the following command: `npx create-next-app@latest -e with-turbopack`or `npx create-next-app@latest --example with-turbopack` and the local development server can be launched with: `next dev --turbo`

## `next/image`

The Image Component comes with the `next/image` API in Next.js v13 has been immensely amplified with:

* Predetermine the correct size of imported image for each device or screen size, using modern image formats like WebP and AVIF. Developers now have the option of either manually setting the `width` and `height` for an image or allow Next.js to decide automatically the appropriate size.
    
* The automatic implementation of image size by Next.js eliminates the risk of a Cumulative Layout Shift (CLS), which is when elements on a page are shifted after initially being rendered by the DOM. Abrupt layout shifts as the page loads can lead to accidental user error and distraction, which can as a result cripple your website’s SEO performance with the Google search engine.
    
* Page loads are faster with the lazy loading feature which is now native to the browser, so the page does not have to wait for all the images to be fetched from the server to display its content, optional blur-up placeholders are displayed like skeleton loaders while the images are pulled from the server into the viewport.
    

%[https://gist.github.com/Kola92/0dbf2481995623875ccea3e18b64ce73] 

The `blurDataURL` is an optional prop that is by default enabled with Next.js Image Component, its value must be a Data URL, this is a URL prefixed with a `data:`scheme allowing content creators to embed small files inline in documents. It only takes effect when the `placeholder=”blur”` prop is manually added. The image must be a base64-encoded image, `next/image` API automatically enlarges and blurs the image, so a small image (10px or less) is recommended.

## `@next/font`

This new font system introduced by Next.js v13 comes with a built-in self-hosting for font files. Fonts can be loaded with zero layout shift, this is possible on account of the underlying CSS `size-adjust` property used.

Google fonts are downloaded into the application at build time and self-hosted with other static assets such as HTML, CSS and images among others. This means no need for any external requests to be made to Google by the client to fetch fonts, consequently improving performance and privacy. `@next/font` can be introduced to your project by running: `npm install @next/font`

%[https://gist.github.com/Kola92/4bfa8e70e37e33015f079c6bba1d89f0] 

## `next/link`

`next/link` API has permanently gotten rid of adding the `<a>` tag as a child in the `<Link>` component. This was implemented in Next.js v12.2 as an experimental option, but now it has been removed for good. I remember I always wonder what is the need of adding a `<a>` tag when the `<Link>` component can always render it. Well, I guess I finally got what I wanted now 🙏🏽

The `next/link` API also provides prefetching of components during page navigation. This works by preloading a route in the background before it’s visited, and then the rendered result of prefetched routes is added to the router’s client-side cache. This makes navigating to a prefetched route near-instant.

%[https://gist.github.com/Kola92/97d63acedfc8a7b7e1b78e7539637f1f] 

## **Conclusion**

Next.js v13 is a major release that significantly benefits and improves the popular React-based framework for building web applications. With swifter performance, improved development experience, and new features like the app directory structure, the next-generation module bundler Turbopack, extension and upgrade of the Image Component and font system, and the simplification of `next/link` API, all these make it easier and faster for developers to build and maintain optimal websites for their users.

The future outlook for Next.js is bright, as it continues to gain popularity and support among the web development community. Its role in web development is growing, as more and more developers adopt the framework for its ease of use, flexibility, and powerful features.

To take advantage of the new features and benefits in Next.js v13, developers should update to the latest version of the framework as soon as possible. By doing so, you can improve your development experience, build high-performance and engaging websites, and stay ahead of the curve in web development. So, don’t wait any longer, update to Next.js v13 today and start taking advantage of its powerful new features!