# Using JSON Server and Postman to Mock Client/Server Communication

## Learning Goals

- Set up JSON Server as a mock backend
- Use Postman to mimic front end responses
- Practice the client/server request/response cycle

## Setup

This is a code-along lesson. Please fork and clone this lesson down to your
computer so that you can follow along with the reading.

## Introduction

In typical full-stack applications, a frontend (the client) and a backend (the
server) work together.

The frontend initiates communication, often either asking for data or sending
some data to be stored. The backend is actively listening for these requests,
and when one is received, it will do some work for us and send a response back.

This response may include requested data, or it could include a confirmation
that data was stored. This request/response cycle is a critical piece of web
development and the backbone of most modern websites.

In the upcoming lessons, we'll start to explore the first half of this
request/response cycle — initiating requests from the frontend. Before we start
practicing in JavaScript, though, we need to explore how this cycle works.

Luckily, we have some tools that can mimic both frontend requests and backend
responses. For the frontend, we have [Postman][postman], an app that can be used
to build requests without writing code. For the backend, we have [JSON
Server][json server], a Node application that mimics the behavior of a full
backend server.

Combined, we can practice sending requests from Postman to the JSON server and
see how the server responds.

## Review: What is JSON Again?

JSON, JavaScript Object Notation, is a _data interchange format_. We use JSON to
send structured data between clients and servers.

There are a few formats available to handle this task, but JSON has some
specific advantages that make it a great choice for our purposes:

- **It is human-readable**: JSON data is stored as a `String`, but structured in
  a way that looks very similar to a JavaScript object.
- **It is easy to convert into a JavaScript object**: JavaScript has built-in
  methods for turning objects into JSON and vice versa. Very handy!
- Despite having JavaScript in the name, **the format is compatible with many
programming languages**: Common backend languages like Ruby and Python have
  their own methods for handling JSON and converting it into object-like data
  structures.

Below is an example of what a piece of JSON looks like when sent from client to
server (or vice versa):

```text
'{ "name": "Annie Easley", "occupation": "Computer Scientist" }'
```

The data above is a `String`, but it contains what looks like key/value pairs
within an object.

Whenever we work with JSON, this is what we're working with — strings that
include notation that mimics JavaScript data structures, like objects and
arrays. Hence JavaScript **Object Notation** — JSON.

Notice that the keys and values are both wrapped in quotes while other
characters, `{`, `}`, and `:`, are not. This is required syntax for JSON. All
text-based data, even keys, must be wrapped in quotes within the larger
`String`. Numbers are the only exception to this rule.

When working with JSON, the outside quotes are not always shown. In addition,
instead of a single line like above, we may see JSON like this:

```json
{
  "name": "Annie Easley",
  "occupation": "Computer Scientist"
}
```

Luckily, you will not often need to concern yourself with the specifics of how
JSON is structured. As mentioned above, we will use built-in tools to convert
JavaScript objects into JSON and vice versa. However, it is important to
recognize JSON when you see it.

If you are curious about JSON syntax, there are many JSON validators online like
[this one](https://jsonlint.com/) that will confirm if your JSON is formatted
correctly.

## What is JSON Server?

JSON Server is a freely available tool that can be included as a Node package in
a JavaScript project. It can turn a JSON file on your computer into mock data
storage.

When JSON Server is running, we can send requests to get data from storage or
add data to it, as though we were talking to a server with a database. It allows
us to build a Frontend application that includes code for communicating with a
server without actually needing a server!

This frees us up to focus on writing Frontend code without having to spend a
long time setting up a mock server.

## Setting up JSON Server

To start using JSON Server, you need to install it, then provide a basic set of
data to practice with.

First, you'll install JSON Server globally on your machine:

```console
$ npm install -g json-server
```

With the command above, you should now be able to spin up a mock server from any
directory on your computer. Alternatively, if you run npm install json-server
(without the -g option) from inside a folder with a `package.json` file,
`json-server` will be added as a dependency in the file.

> **Note**: For users of the [Live Server VSCode extension][live-server], you'll
> need to do a bit of extra configuration so that the `json-server` plays nicely
> with Live Server in future lessons. Follow the steps in [this
> gist][live-server settings] (you'll only need to do this once), then come back
> to this lesson. (Windows users - just run `control + shift + p` instead of
> `command + shift + p` when following the instructions.)

[live-server]:
  https://marketplace.visualstudio.com/items?itemName=ritwickdey.LiveServer
[live-server settings]:
  https://gist.github.com/ihollander/cc5f36c6447d15dea6a16f68d82aacf7

Next, we'll need to create a file that will act as our data storage. We'll
create it within the repository that you cloned down in this code-along.

```console
$ touch db.json
```

Open this file in your text editor and paste in the following content:

```json
{
  "articles": [
    {
      "id": 1,
      "title": "Example Article",
      "content": "This is an example."
    },
    {
      "id": 2,
      "title": "Second Article",
      "content": "This is also an example."
    }
  ]
}
```

Here, we've created one top-level key, `"articles"`, in our JSON, which points
to an array. This array contains two elements, both of which are objects with
three keys: `"id"`, `"title"`, and `"content"`. Our first goal will be to access
this data.

## Start the Server

To start JSON Server, run the following command from the same directory that
your `db.json` file is in:

```console
$ json-server --watch db.json
```

When run, you'll see some messaging about how to access our JSON data. By
default, JSON Server will start up on port `3000`. You should see a notice that
you can access the server at `http://localhost:3000`.

Once json-server is up and running, open your browser and paste the
`http://localhost:3000` URL in. If the server is running correctly, you should
be presented with a page of information provided by JSON Server.

> **Note:** If you see an error saying something like `EADDRESSINUSE`, that
> likely means you already have something running on port `3000`. To avoid this
> error, it's a good idea to shut down json-server whenever you switch to
> working on a different project. You can do so by holding down the `control`
> button and pressing `C` within the terminal currently running json-server. If
> you receive this error, but can't find any other terminals that are running
> json-server, you can try running the series of commands listed at the bottom
> of this lesson. If all else fails, you can always restart your computer!

On this page, you'll see a **Resources** section that lists one resource:
`/articles`. The server has read the `db.json` file and found our `articles`
key, turning it into a resource.

Click `/articles` and you will be navigated to a new page,
`http://localhost:3000/articles`. Instead of a page of info, you'll see the
value associated with `articles` in our data, an array containing two objects:

```json
[
  {
    "id": 1,
    "title": "Example Article",
    "content": "This is an example.",
  },
  {
    "id": 2,
    "title": "Second Article",
    "content": "This is also an example.",
  },
];
```

> **Note**: It's recommended that you use a browser extension such as [JSON
> Viewer][json viewer] to format the JSON string in the browser.

We can go even further — notice the `"id"` key that is listed. Instead of just
going to `/articles`, we can append the value of `"id"` to the end of the URL:

```text
http://localhost:3000/articles/1
```

Now, instead of an array, we get the object inside of it:

```json
{
  "id": 1,
  "title": "Example Article",
  "content": "This is an example."
}
```

Neat! So what is happening? We won't go into too much detail, but JSON server is
following [RESTful conventions][rest].

By providing `/articles` followed by `/1` in our URL, JSON Server knows we're
asking for a resource called `articles`, and within that resource, we're asking
for whatever data has an ID of `1`. JSON Server will look through and match the
request to an ID and return _that_ content. If we change to `2`, we'll get the
data we stored in `articles` that has an ID of `2`.

Leave JSON Server running and we'll move on to the next tool, Postman.

## What is Postman?

Postman is an application that allows us to mock up frontend requests without
writing any JavaScript. With Postman, we can practice sending requests to our
JSON Server.

## Setting up Postman

To get the Postman app, head over to
[https://www.postman.com/downloads/][postman] and click **Download the App**.
There is a web version of Postman, but this will not work with our `localhost`
server, so we'll be using the app version.

Once it's downloaded and installed, open the app. If you see a screen inviting
you to create an account or sign in, click the "Skip and go to the app" link at
the bottom.

On the next screen, you should see a "Get Started" section on the right side;
click the first option: "Create a request".

You should then see an input field starting with **GET** and containing the
placeholder text _Enter request URL_.

![Get request
bar](https://curriculum-content.s3.amazonaws.com/phase-1/communicating-with-the-server/get-request-bar.png)

We're now ready to send requests to our server!

## Retrieving Data from our JSON Server using Postman

Here, we'll write in the URL we previously used to get our JSON server data,
`http://localhost:3000/articles/1`.

![localhost
entered](https://curriculum-content.s3.amazonaws.com/phase-1/communicating-with-the-server/postman-request.png)

Once entered, hit the **Send** button. If everything is working, you should see
the same article data from earlier, an object with three keys: `"id"`,
`"title"`, and `"content"`. You're now performing the full request/response
cycle using our tools! Let's explore what is happening.

When you click **Send** on Postman, you send a request to the URL you provided.
This is a **GET** request — a request for data from a resource.

Our JSON server is actively listening for these requests. If you look at your
terminal where JSON server is running, you will see that the server has
recognized your GET request, displaying something similar to this:

```text
GET /articles/1 200 25.666 ms
```

JSON Server sees that this is a GET request. It also notes that the request is
specifically for `/articles/1`. `200` is a [HTTP status code][status] that
indicates the request was received, accepted and responded to successfully.
`25.666 ms` is the amount of time it took to complete the request, in
milliseconds.

Back in Postman, we can see the response from our server in the lower panel and
confirm we received what we expected.

Depending on what we need, we can change out the details of our request. Imagine
we are building a local news site containing many articles. Instead of
requesting just the article with an ID of `1`, we might just send a request for
`/articles` and get everything available from the server.

In complex webpages, we may send requests to both depending on what page is
being accessed — we might have an index page of all article titles, and when a
title is clicked, we'd send a request for a single article.

## Sending Data to our JSON Server from Postman

We've now seen how a GET HTTP request works, so let's move on to a POST request.
POST requests are used when we want to _send data to_ a server.

> **Note:** There are a couple of other HTTP request options for sending data —
> PUT and PATCH — but for simplicity, we'll just focus on POST requests for now.

Continuing our news site example, let's say we've written a new article and want
to add it to the site.

A POST request allows us to send the title and content of this new article to
the 'database' (our db.json file, in this case). As long as we've structured the
request correctly, JSON Server will receive the request, recognize it as a POST
request and attempt to store the article information in the db.json file.

To send data to our server, first, we need to switch our Postman request from
GET to POST. Click on GET beside the URL bar to display a drop-down menu of HTTP
request options and switch over to POST.

Second, we need to adjust the URL we're using. In this particular case, we're
sending content that should become a _new_ article. Because of this, we don't
want to use a specific ID value in the URL. Instead, we'll send a request to
`/articles`:

```txt
http://localhost:3000/articles
```

When JSON Server receives the request, it'll recognize it as a POST request and
automatically add it to the appropriate resource.

Finally, before we can send our request, we need to provide the data we want to
send. In Postman, just below the URL bar, click the **Body** tab, then choose
the **raw** option, and select **JSON** from the drop-down menu.

![postman
post](https://curriculum-content.s3.amazonaws.com/phase-1/communicating-with-the-server/postman-post.png)

In the code box just below these options, write in the following JSON:

```json
{
  "title": "Third Article",
  "content": "Another example!"
}
```

Note that we don't need to wrap the contents in quotes and that we left out the
ID key/value. Postman will handle the quotes that wrap the contents for us, and
JSON Server will create the ID.

![postman post
example](https://curriculum-content.s3.amazonaws.com/phase-1/communicating-with-the-server/postman-post-body.png)

>**Note** While we're not working with real databases at this point, it's
>important to note that real databases will also take care of creating IDs. IDs
>are meant to be unique within a database — how are we to know what a unique ID
>might be when we're creating a new entry in our database? Imagine our database
>has millions of entries! Databases, on the other hand, can verify that an ID is
>unique before creating one. Problem solved!

When ready, click **Send**. In the terminal, we should see JSON Server
recognizing the request. In Postman, we'll see the server's response in the
lower panel:

```json
{
  "title": "Third Article",
  "content": "Another example!",
  "id": 3
}
```

Typically, after a successful POST request, the server will send back the new
data as a response. In this case, it sent back what we sent _and_ included the
newly assigned ID.

As one final confirmation, navigate to your `db.json` file and open it up. You
should see that the file has changed to include your newly submitted content!
Congratulations, you've persisted data to `db.json`!

## Submit Using CodeGrade

Once you're done, be sure to commit and push your code up to GitHub, then submit
the assignment using CodeGrade. Even though this lab does not have tests, it
must still be submitted through CodeGrade in order to be marked as complete in
Canvas.

## Conclusion

Although we haven't learned how to build our own backends yet, JSON server can
act as a placeholder, enabling us to learn the first half of the
request/response cycle without having to worry about a backend.

Also, because JSON Server follows RESTful conventions that are widely used
throughout the internet, we'll start to become familiar with how server
resources _should_ be structured long before we create our own.

We encourage you to try creating your own resources in `db.json`. A few quick
notes about setting resources up:

- Any top-level key/value pair (like `articles` in our example) will be
  automatically treated as a resource we can retrieve and send data to.
- The value should be either an array or an object. Try both to see how they
  differ!

With JSON Server, you'll now be able to design frontends that persist data!

## Handling EADDRESSINUSE

If you're getting the EADDRESSINUSE error and can't find any processes open in
any of your terminals, you can run the following series of commands.

1. Run the command `lsof -i :PORT-NUMBER` in your terminal.
   - This is a `Bash` command. [Learn more about
     Bash](https://en.wikipedia.org/wiki/Bash_(Unix_shell)).
   - The first part of the command stands for `list open files`.
   - The `-i` portion tells our computer that we want to look for open network
    connections.
   - The `:PORT-NUMBER` portion tells our computer to look for any type of
    network connection on that specific port number.
     - You should input the port number that you're trying to use in place of
      `PORT-NUMBER` in the command.
     - Ex: `lsof -i :3000`
2. You should see an output in your terminal listing information about the
   processes running on this port.
3. Look for the column labelled `PID`. There should be a number listed in this
   column.
4. Run the command `kill PID-NUMBER`, where `PID-NUMBER` is the number listed in
   the `PID` column.
5. If that still didn't work, trying running `kill -9 PID-NUMBER`.
6. If, after running these commands, you're still receiving the EADDRESSINUSE
   error, just restart your computer!

## Resources

- [JSON Server][json server]
- [Postman][postman]
- [HTTP Status Codes][status]
- [Representational State Transfer (REST)][rest]

[rest]: https://en.wikipedia.org/wiki/Representational_state_transfer
[json server]: https://www.npmjs.com/package/json-server
[status]: https://developer.mozilla.org/en-US/docs/Web/HTTP/Status
[postman]: https://www.postman.com/downloads/
[json viewer]:
  https://chrome.google.com/webstore/detail/json-viewer/gbmdgpbipfallnflgajpaliibnhdgobh?hl=en-US
