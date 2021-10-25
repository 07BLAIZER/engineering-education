---
layout: engineering-education
status: publish
published: true
url: /how-to-run-contentful-cms-as-headless-with-nextjs/
title: How to run Contentful CMS as headless with Nextjs
description: In this guide we will learn how we can use Next.js to run WordPress as a headless CMS. Next.js uses generators that render a static spage.
author: catherine-macharia
date: 2021-09-20T00:00:00-14:00
topics: [Languages]
excerpt_separator: <!--more-->
images:

  - url: /engineering-education/how-to-run-contentful-cms-as-headless-with-nextjs//hero.jpg 
    alt: How to run Contentful CMS as headless with Nextjs Image
---
Contentful is a headless CMS. This means that it's built as a content repository making content available via an API (RESTful or GraphQL) to be displayed on any device. This article will set up simple blog posts on the CMS and then display them to the users using Next.js. We will query the data from the CMS using an API. We will use the content model. A content model refers to a Schema, which will allow the generation of similarly structured pieces of content. For example, for every blog post, we want to have a title, an excerpt, a description, a cover image, and a date. The composition of all these fields to a blog post makes up the Schema.
<!--more-->
### What is a headless CMS
A content-oriented CMS consists of the backend and the frontend. This way, they both run as a monolithic system. It combines the various modules and features and runs as one. So when you run a CMS as a headless system, both the content and the presentation are decoupled. AS a headless system, the content presentation layer is now flexible. And the backed can now be driven with raw data using APIs (application programming interface).

This way, you still have the CMS for content creation and management. Its API is used to deliver raw content. This means that you can create a website, a mobile app, or any application that uses the raw CMS content. Then represent it in the way that you want your users to see this data.

### Reasons for running WordPress as a headless CMS
- This allows you to modify the content delivery channels that you present to users. It lets you change the way content is presented without re-authorizing it.
- Since the data is delivered as raw data, you can use any technology that fits your needs. API-driven data promotes omni-channel architecture.
- It simplifies the work of developers by separating the frontend and backend components. This means you can create a faster efficient, and scalable application.

### Goal
This article will interact with Contentful as a headless CMS by creating a simple posts app with Next.js.

### Pre-requisites
To follow along with this article, it is crucial to have the following:
- [Node.js](https://nodejs.org/en/) installed on your computer.
- [Git](https://git-scm.com/) installed on your computer.
- Have some working knowledge with JavaScript and Next.js.
- Have some working knowledge with Git.

### Table of contents
- [What is a headless CMS](#what-is-a-headless-cms)
- [Reasons for running WordPress as a headless CMS](#reasons-for-running-wordpress-as-a-headless-cms)
- [Goal](#goal)
- [Pre-requisites](#pre-requisites)
- [Table of contents](#table-of-contents)
- [Creating an account with Contentful](#creating-an-account-with-contentful)
- [Setting up the content model](#setting-up-the-content-model)
- [Adding posts to Contentful](#adding-posts-to-contentful)
- [Setting up the Next.js app](#setting-up-the-nextjs-app)
- [Adding Contentful credentials to the Next.js app](#adding-contentful-credentials-to-the-nextjs-app)
- [Querying the added posts](#querying-the-added-posts)
- [Displaying the Posts](#displaying-the-posts)
- [Testing simpleBlogPostCollection](#testing-simpleblogpostcollection)
- [Solving the single post 404 error](#solving-the-single-post-404-error)
- [Fetching a single post data](#fetching-a-single-post-data)
- [Displaying a single post](#displaying-a-single-post)
- [Testing simpleBlogPost](#testing-simpleblogpost)
- [Preview mode](#preview-mode)
- [Hosting to Vercel](#hosting-to-vercel)
- [References](#references)

### Creating an account with Contentful
You need to have an account with Contentful. If you already have an account, just [login](https://be.contentful.com/login) and continue to the next step, else if you do not follow the below steps:

- Visit the [signup](https://www.contentful.com/sign-up/) page.
- You can signup manually, or you can also use your Google or GitHub account. Feel free to do any.
- Answer through the questions that follow.
- Then click **Start With Contentful**

At this point, you are ready for the next step.

### Setting up the content model
A content model refers to a Schema, which will allow the generation of similarly structured pieces of content. For example, for every blog post we want to have a ***title***, an ***excerpt***, a ***description***, a ***cover-image***, and a ***date***. The composition of all these fields to a blog post makes up the Schema.

To set up the above Schema, follow the following steps:

- On the top bar, click on ***Content model***.
- And then ***Design your content model***.
- Then ***Create content type*** in the modal that pops up.
- Give the ***content type*** a name of ***simple blog post***, the API identifier field is auto-populated, then give a brief description in the descriptions field like ***simple blog post content type***.

After that, it is time to add the fields to make up the Schema. We will add the fields we have discussed above one by one by following the following steps:

- Hit the ***Add field*** button to add ***title***,  choose ***text***, give it a name of *title*, then click ***create***.

- Hit the ***Add field*** button to add ***excerpt***, choose ***text***, give it a name of ***excerpt***, select ***Long text, full-text search***, then click ***Create***.

- Hit the ***Add field*** button to add ***description***, choose ***Rich text***, give it a name of ***description***, and then click ***Create***.

- Hit the ***Add field*** button to add *cover image*, choose ***Media***, give it a name of ***cover image***, and then click ***Create***.

- Lastly, hit the ***Add field*** button to add ***date***, choose ***Date and time***, give it a name of ***date***, and then click ***Create***.

- Hit the ***Save*** button to the top right section.

At this point, we have defined our Schema by creating a content type and adding fields to that content type. We now move on to adding Content to our content type in the next step.

### Adding posts to Contentful
After adding the content type, it is now time to add the data that we want to see on our app. To do this, we will follow the following steps:
- On the top navigation bar, click *** Content ***.
- Click the ***Add simple blog post*** button.
- On the resulting form, key in a title, key in an excerpt, and key in a description. Also, you can choose to add dummy text or not.

Let us now add the cover image. To do so, click on ***Add new media***, give it a title like ***simple cover image*** or something else; by hitting ***Open file selector***, you can upload an image from your computer or upload from different sources listed there. On selecting a file, hit the ***Publish*** button, which is to the right. From there, use the back icon on the top left to navigate back to the initial form.

Also, remember to add a date, choose the current date. Finally, hit the ***Publish*** button to the right. You will see a notification message that your blog post was added successfully. From there, click on the back icon to the left to go to the Content's page.

On the Content's page, you should be able to see your published blog post. You can repeat the process and add several posts to have several posts to fetch. After adding the posts, it is now time to set up our Next.js environment in the proceeding step.

### Setting up the Next.js app
To set up the Next.js environment, we will use [create-next-app](https://nextjs.org/docs/api-reference/create-next-app). This is a one-time command that allows you to generate a Next.js template. It generates a ready structured project that allows you to scale up quickly and add more code as you go. To generate this Next.js template app, open a terminal (command line) and run the following command from the directory to save the template.

```bash
npx create-next-app contentful-nextjs-app
```

The above command will create the Next.js app inside the `contentful-nextjs-app` folder. Now navigate to this project folder using the change directory command as shown below;

```bash
cd contentful-nextjs-app
```

### Adding Contentful credentials to the Next.js app
For Next.js to communicate with the Contentful API, we will need to add credentials to validate and facilitate this. In your `contentful-nextjs-app` project folder create a `.env.local` file. It will host the environmental variables to connect to Contentful. Add the following to the file;

```bash
CONTENTFUL_SPACE_ID=your_contentful_space_id
CONTENTFUL_ACCESS_TOKEN=your_contentful_space_token
CONTENTFUL_PREVIEW_ACCESS_TOKEN=your_contentful_preview_access_token
CONTENTFUL_PREVIEW_SECRET=your_contentful_preview_secrets
```

To get the above variables, we will follow the following steps:

- From your Contentful dashboard page, click on ***Settings***.
- And then under ***Space settings***, click on ***API keys***.

If you have not added an ***API key*** yet, click on ***Add Api Key*** on the right hand.

- In the resulting form:
- Copy the ***Space ID*** and paste it to the `CONTENTFUL_SPACE_ID` field.
- Copy the *Access token* and paste it to the `CONTENTFUL_ACCESS_TOKEN` field.
- Copy the *Preview access token* and paste it to the `CONTENTFUL_PREVIEW_ACCESS_TOKEN` field.
- For the `CONTENTFUL_PREVIEW_SECRET`, key in any random string.

Our app is now fully set up, and we are ready for the proceeding step.

### Querying the added posts
At this point, we can now fetch the posts that we added earlier. To query the posts we added, we will first set up the API, which we are going to call.:

Start by creating a `lib` folder in the root of the project folder. Then, inside the `lib` folder, create an `api.js` file. In the `api.js`, we will define the fields to fetch, as shown below.

```js
const POST_GRAPHQL_FIELDS = `
    sys {
        id
    }  
    title
    coverImage {
        url
    }
    date
    excerpt
    description {
        json
    }
`
```

From above, we are defining the fields we want for every specific blog post following the Schema we created.

Still in the `api.js`, create a custom method for establishing contact with the Contentful API:

```js
async function fetchGraphQL(query, preview = false) {
    return fetch(
        `https://graphql.contentful.com/content/v1/spaces/${process.env.CONTENTFUL_SPACE_ID}`,
        {
        method: 'POST',
        headers: {
            'Content-Type': 'application/json',
            Authorization: `Bearer ${
            preview
                ? process.env.CONTENTFUL_PREVIEW_ACCESS_TOKEN
                : process.env.CONTENTFUL_ACCESS_TOKEN
            }`,
        },
        body: JSON.stringify({ query }),
        }
    ).then((response) => response.json())
}
```

The above method will form the base for querying the posts. We will send a `POST` request to the ***Contentful API*** with the `URL` and `headers` set, but the `body` is dynamic for every request.

Then create a method for extracting data from the posts by adding the following:

```js
function extractPostEntries(fetchResponse) {
    return fetchResponse?.data?.simpleBlogPostCollection?.items
}
```

For every response from the Contentful API query, the data will be returned with various metadata, so we have to narrow it down to the data we want as above.

Now create a method to query the posts from the Contentful API side:

```js
export async function getAllPostsForHome(preview) {
    const entries = await fetchGraphQL(
        `query {
        simpleBlogPostCollection(order: date_DESC, preview: ${preview ? 'true' : 'false'}) {
            items {
            ${POST_GRAPHQL_FIELDS}
            }
        }
        }`,
        preview
    )
    return extractPostEntries(entries)
}
```

From above, we are using the custom method we defined above to get posts from the CMS. A point to note is that when we are querying more than one blog post, we will use `simpleBlogPostCollection`. If we were querying one blog post, we would use `simpleBlogPost`.

### Displaying the Posts
We are now set to show the posts. In `pages/index.js`, we will fetch the posts using [getStaticProps](https://nextjs.org/docs/basic-features/data-fetching#getstaticprops-static-generation). It will simply imply that data will be pre-rendered at build time. This helps Next.js to serve a user this data ahead of time. Furthermore, this makes the web app render the Content very fast, as if it was static.

To implement this, import the `getAllPostsForHome` function from *lib/api.js* at the top of your in `pages/index.js`.

```js
import {getAllPostsForHome} from "../lib/api"
```

Also, add the following code after the `Home` function:

```js
export async function getStaticProps({preview = false}){
    let allPosts = (await getAllPostsForHome(preview))  ?? [];

    return {
        props: { preview, allPosts }
        
    }
    
    }
```

Since we are now fetching the posts, we will modify the `Home` function as follows so that we can receive and show the posts:

```js
export default function Home({allPosts}) {
    return (
        <div className={styles.container}>
        <Head>
            <title>Blog app</title>
            <meta name="description" content="Simple blog app with Contentful CMS" />
            <link rel="icon" href="/favicon.ico" />
        </Head>

        <main className={styles.main}>

            <div className={styles.grid}>
            {
                allPosts.length > 0 ? (
                allPosts.map((post) => (
                    <div className={styles.card} key={post.sys.id}>
                    <div className={styles.imageHolder}>
                        <img src={post.coverImage.url} alt={post.title} />
                    </div>
                    <div className={styles.details}>
                        <Link href={`posts/${post.sys.id}`}>
                        <a>
                        {post.title} &rarr;
                        </a>
                        </Link>
                        <p>{post.excerpt}</p>
                    </div>
                    </div>
                ))
                ) : (
                <div className={styles.card}>
                <p>No posts added!</p>
            </div>
                )
            }
            </div>
        </main>

        <footer className={styles.footer}>
            <p>Simple blog app</p>
        </footer>
        </div>
    )
    }
```

From above, we check if we have posted; if we have them, we loop through displaying each of them; otherwise, we display a message if we do not have posts. Modify the `styles/Home.module.css` as follows to cater for the changes we have made to the `Home` function:

```css
.container {
  min-height: 100vh;
  padding: 0 0.5rem;
}

.main {
  padding: 5rem 0;
  min-height: 85vh;
}

.footer {
  width: 100%;
  padding: 1rem;
  border-top: 1px solid #eaeaea;
  display: flex;
  justify-content: center;
  align-items: center;
}

.footer a {
  display: flex;
  justify-content: center;
  align-items: center;
  flex-grow: 1;
  font-family: "Segoe UI", Tahoma, Geneva, Verdana, sans-serif;
}

.title a {
  color: #0070f3;
  text-decoration: none;
}

.title a:hover,
.title a:focus,
.title a:active {
  text-decoration: underline;
}

.title {
  margin: 0;
  line-height: 1.15;
  font-size: 1rem;
}

.title,
.description {
  text-align: left;
  padding: 12px;
}

.description {
  line-height: 1.5;
  font-size: 1.5rem;
}
.grid {
  max-width: 800px;
  margin: 0px auto;
  /* display:flex;
    justify-content: left; */
}

.card {
  margin: 1rem;
  text-align: left;
  color: inherit;
  text-decoration: none;
  border: 1px solid #eaeaea;
  border-radius: 10px;
  transition: color 0.15s ease, border-color 0.15s ease;
  width: 90%;
  display: flex;
  justify-content: space-between;
}

.imageHolder {
  width: 15%;
}

.imageHolder img {
  width: 100%;
  height: 100%;
  margin-right: 10px;
  border-radius: 10px 0px 0px 10px;
}

.details {
  width: 85%;
  padding: 1.5rem;
}

.details a {
  margin: 0 0 1rem 0;
  font-size: 16px;
  font-weight: bold;
  font-family: "Segoe UI", Tahoma, Geneva, Verdana, sans-serif;
}

.details p {
  margin: 0;
  font-size: 14px;
  font-family: "Segoe UI", Tahoma, Geneva, Verdana, sans-serif;
  line-height: 1.5;
}

@media (max-width: 600px) {
  .grid {
    width: 100%;
    flex-direction: column;
  }
}
```

After doing the changes, we are now ready to view the posts.

### Testing simpleBlogPostCollection
We are now ready to test if we can fetch all the posts we added to the contentful API. To do so, ensure that your development server is started by running the following command from the terminal at the root of the `contentful-nextjs-app` project root folder.

```bash
npm run dev
```

Open your browser and go to `http://localhost:3000`; you should be able to view the posts now. In case of any error, kindly revisit the steps. Your page should be similar to:

![home-page](/engineering-education/how-to-run-contentful-cms-as-headless-with-nextjs/home-page.png)

### Solving the single post 404 error
When you click on any specific post, it gives you a `404` error because we have not worked on that yet. Let us get into it by following the following steps:

### Fetching a single post data
In the `lib/api.js` file, we will add the method of extracting a single post as follows:

```js
function extractPost(fetchResponse){
    return fetchResponse?.data?.simpleBlogPost?.items[0];
}
```

To avoid the metadata, we will derive what we want using the above function.

In the `lib/api.js` file, we add another function to fetch the single post and other related posts as follows:

```js
export async function getPostAndMorePosts(preview,postId){
        
    // Get a singlepost/entry

    const entry = await fetchGraphQL(
        `query{
            title(id:"${postId}",preview:${preview ? true : false}){                
                ${POST_GRAPHQL_FIELDS}                
            }
        }`
    );

    // Get entries

    const entries = await fetchGraphQL(
        `query{
            titleCollection(preview:${preview ? true : false}, limit:2){
                items{
                    ${POST_GRAPHQL_FIELDS}
                }
            }
        }`
    );

    // Extract a post

    const post = extractPost(entry);

    // Get the related posts

    const relatedPosts = extractPostEntries(entries).filter((_post) => _post.sys.id !== post.sys.id);

    return {
        post,
        relatedPosts
    };
}
```

In the above function, we fetched a single post and the related posts by filtering the specific post.

In the `pages` folder, create another folder and call it `posts`. In the `posts` folder, create a `[postId].js` file. The `[postId]` to signify that the `post id` will be dynamic. In the `[postId].js` file:

- Start by importing the `getPostAndMorePosts` function from `lib/api.js` file:

```js
import {getPostAndMorePosts} from "../../lib/api";
```

- Fetch the post using `getStaticProps` and `getStaticPaths` method as follows:

```js
// Fetch for a single post

export async function getStaticProps({ params: { postId } }) {
    let { post, relatedPosts } = await getPostAndMorePosts(false, postId);
    return {
        props: {
            post,
            relatedPosts
        }
    }
}

// Fetch the other posts done at build time

export async function getStaticPaths() {

    const posts = await getAllPostsForHome(false);

    let paths = posts.map((post) => ({
        params: {
            postId: post.sys.id
        }
    })
    );

    return {
        paths,
        fallback: true
    }
}
```

The `getStaticProps()` function will receive the `postId` from the `params` of the `context` then fetch the data of that specific post.

The `getStaticPaths` will create dynamic pages based on the posts from Contentful so that the next time you are fetching a specific post, you will get the dynamic page created earlier.

### Displaying a single post
Create a `Post` function to display the post. Start by importing the necessary modules as shown below:

```js
import Head from "next/head";
import Link from "next/link";
import {useRouter} from "next/router";
import styles from "../../styles/Home.module.css";
```

For the `Post` function, add the following:

```js
export function Post({posts, relatedPosts}){

    const router = useRouter();

    return (
        <div className={styles.container}>
            <Head>
            <title>Blog app</title>
            <meta name="description" content="Simple blog app with Contentful CMS" />
            <link rel="icon" href="/favicon.ico" />
            </Head>

        <main className={styles.main}>

        {
            router.isFallBack ? (
                <div styles={styles.title}>
                    Loading
                </div>
            ): (
            <>
            <div className={styles.content}>

            <div styles={styles.title}>
                {post.title}
            </div>


            <p className={styles.meta}>
                {new Date(post.date).toDateString()}
            </p>


            <div className={styles.coverImage}>
                <img src={post.coverImage.url} alt={post.title} />
            </div>

            </div>

            <div className={styles.grid}>
            {
                relatedPosts.length > 0 ? (
                    <>
                        <div className={styles.title}>
                            Related posts
                        </div>
                        {
                        relatedPosts.map((post) => (
                            <div className={styles.card} key={post.sys.id}>
                            <div className={styles.imageHolder}>
                                <img src={post.coverImage.url} alt={post.title} />
                            </div>
                            <div className={styles.details}>
                                <Link href={`posts/${post.sys.id}`}>
                                <a>
                                {post.title} &rarr;
                                </a>
                                </Link>
                                <p>{post.excerpt}</p>
                            </div>
                            </div>
                        ))
                        }
                    </>
                ) : null
            }
            </div>
                </>
            )
        }

            
        </main>

        <footer className={styles.footer}>
            <p>Simple blog app</p>
        </footer>
        </div>
    )
}
```

From above, we are receiving the `post` and the related posts from the `getStaticProps` to our function. To cater for the build process, we are checking for the `isFallBack` from the router and displaying a loading text; else, we are displaying the `title`, `date`, `coverimage`, and the `related posts`.

We need to add the following styles to the `Home.module.css` file since we are referencing them:

```js
.content{
    max-width: 800px;
    margin: 0px auto;
    padding: 12px;
}
.meta{
    font-size:12px;
    color: #aaa;
}

.coverImage {
    width:100%;
    height:300px;
}

.coverImage img{
    width: 100%;
    height: 100%;
}
```

### Testing simpleBlogPost
At this point, if you click on any of the posts, you will be redirected to a single post page. To test this, first ensure your server is up and running. If not, run the following command from the terminal at the root of the `contentful-nextjs-app` project root folder.

```bash
npm run dev
```

Open your browser, go to `http://localhost:3000`, and click on any of the posts on the home page. This will load that post to your screen.

![initial-post-page](/engineering-education/how-to-run-contentful-cms-as-headless-with-nextjs/initial-post-page.png)

It seems pretty okay, but there is something we are missing, the description part. So let us handle that in the next step. Since we added our description as rich-text, we will have to use a separate package to show it in the UI. The package is [@contentful/rich-text-react-renderer](https://www.npmjs.com/package/@contentful/rich-text-react-renderer). Open a separate tab in your terminal and install the package using:

```bash
npm install @contentful/rich-text-react-renderer
```

After installing it, import it in the `[postId].js` file:

```js
import { documentToReactComponents } from '@contentful/rich-text-react-renderer'
```

After the `cover image` section, add the following:

```js
<div className={styles.contentBody}>
    {documentToReactComponents(post.description.json)}
</div>
```

From above, we are now embedding the rich text data we added to a React component inside a `div`. In the `Home.module.css`, let' give this `div` some margin as follows:

```css
.contentBody{
    margin:10px auto;
}
```

Now check if your server is still running. If not, use:

```bash
npm run dev
```

Refresh your page; you should now see the description as follows:

![post-page-with-description](/engineering-education/how-to-run-contentful-cms-as-headless-with-nextjs/post-page-with-description.png)

Now, only one step is remaining. We need to be able to transition to the home page easily. For this, we will add a home link inside the main component, but before the content, as follows:

```js
<div className={styles.homeLink}>
<Link href="/">
    <a>Home</a>
</Link>
</div>
 ```

Add its style to the `Home.module.css` as follows:

```css
.homeLink {
  max-width: 800px;
  margin: 10px auto;
  padding: 12px;
}

.homeLink a {
  color: #0070f3;
}

```

On the posts page, now you should have a visible home page link which you can click to go to the home page. Your page should be similar to the following:

![final-post-page](/engineering-education/how-to-run-contentful-cms-as-headless-with-nextjs/final-post-page.png)

At this point now, the posts are now visible to the user as composition and independent.

### Preview mode
When you want to view the changes made to your blog posts that are not yet published, you need to use the preview mode. To do this, you will just need to pass the preview option to *true* whenever you are making the API call.

### Hosting to Vercel
To host the project to Vercel, follow the following steps:

- Create a GitHub repository and name it any name of your choice.
- Push the code to your GitHub repository.
- Log on to [Vercel](https://vercel.com/).
- If you do not have an account, simply sign up with any provider.
- From your dashboard, click on [new project](https://vercel.com/new).
- Under the ***import Git repository*** section, select the repository you previously created and click ***import***.
- Skip the create team section onto the *Configure project* section.
- Under the environmental variables tab, add all the variables from your project's `.env.local`, each with its name and value. Then, simply copy-paste to avoid errors.
- After copy-pasting all of them, then click ***Deploy***.
- Wait a few seconds for everything to be set up.
- From your dashboard, you will be able to visit your project. You can also share with fields to showcase what you have built.

### References
In this article, we have used various concepts revolving around Next.js and Contentful. To gain more knowledge and build on top of what you have learned, kindly consider the following resources:

- [Data fetching in Next.js](https://nextjs.org/docs/basic-features/data-fetching)
- [Css in Next.js](https://nextjs.org/docs/basic-features/built-in-css-support)
- [Image component in Next.js](https://nextjs.org/docs/api-reference/next/image)
- [Contentful and react.js intergration](https://www.contentful.com/developers/docs/javascript/tutorials/getting-started-with-react-and-contentful/)
- [Git cheat sheet](https://www.atlassian.com/git/tutorials/atlassian-git-cheatsheet)

All the source files and code used in this project are hosted on [GitHub](https://github.com/Catemacharia/How-to-run-Contentful-CMS-as-Headless-with-Next.js).