# Coursera HTML, CSS, and Javascript for WebDevs - Week 5 Notes

## Document Object Model Manipulation
### Lecture 53 - DOM Manipulation

 - See examples folder for the index.html we're referencing.

Document Object Model Manipulation  

The special document called `document`

The `window` object has a `document` object that contains our entire page. We use this to access elements in the page.

For example in the console:

```
document.getElementById("title");
  <h1 id="title">Lecture 53</h1>
```

We can access the `document` variable inside of our scripts, because it is in the `global` scope.

```javascript
console.log(document.getElementById("title"));
```

We get null? Why?  
Because of when the line is executed - where it is mentioned! In the script tag inside the header.  
The item "title" has not yet been rendered. So it's null. How do we solve it? Severla ways.  

  - special method that listens for an event in the lifecycle of hte page loading, and triggers the execution
  - place the javascript at the very _end_ of the document, so it executes after everything in the DOM is in browser memory.

The same code _after_ the JS script tag is moved to the end, produces the element just like the console.

```javascript
console.log(document instanceof HTMLDocument); // true
```

In our `index.html`, we tell the button to do something `onClick`:

```html
<button onclick="sayHello()">Say it!</button>
```

Now it will call the function sayHello in our JS on clicking the button.

The function:

```javascript
function sayHello () {
    var name = 
      document
        .getElementByID("name")
        .value; // gives us the value instead of the element.
    var message = "Hello " + name " "!";
    docuemnt
      .getElementByID("content")
      .textContent = message;     // This puts the message Into the div with id 'content'!
}
```

What if we want to put HTML styling in? If we put it into the `message` variable, it will not render. (Good!)

We need a different way to say to render HTML styling.

```javascript
function sayHello () {
    var name = 
      document
        .getElementByID("name")
        .value; // gives us the value instead of the element.
    var message = "<h2>Hello " + name " "!</h2>";
    docuemnt
      .getElementByID("content")
      .innerHTML = message;     // This puts the message Into the div with id 'content'!
}
```

How can we add intelligence?
Let's make the Title change for specific inputs.

```javascript
function sayHello () {
    var name = 
      document
        .getElementByID("name")
        .value; // gives us the value instead of the element.
    var message = "<h2>Hello " + name " "!</h2>";
    docuemnt
      .getElementByID("content")
      .innerHTML = message;     // This puts the message Into the div with id 'content'!

    if ( name == "student") {
      var title = 
        document
          .querySelector("#title") //specify via CSS selector syntax
          .textContent;
      title += " & Lovin' it!";   //we haven't change the actual value, just a copy.
      document
        .querySelector("h1") //can also query on the h1!
        .textContent = title;
    }
}
```

### Lecture 54 - Handling Events

Continunig from the last lecture, the "onClick" is an example of an event handler.

Event Handlers are functions that you bind with specific methods to certain events that happen in the browser.

The events may be triggered by 

  - the browser life cycle, i.e. page loaded
  - a user action, i.e. clicking or typing a character.

The easiest way to envoke is with hte `onSomething` attribute in html.

Examples:

  - `onblur` - when this element loses focus
  - `onclick`
  - `ondblclick`
  - `onfocus`
  - `onkeydown`
  - `onkeypress`
  - `onkeyup`
  - `onmousedown`

But you don't have to place things straight on the HTML. Dirties your html a bit, this isn't content.

Instead, we can bing the function `sayHello` to the onclick event itself

#### Unobstrusive event binding

Remove the onclick from the html.

In the script.js:

```javascript
document.querySelector("button")
  .addEventListener("click", sayHello);
```

Now when buttons are clicked, the will call `sayHello()`!  
This also means `this` will point to the button element instead of the `window`  


Alternatively, you can do:

```javascript
document.querySelector("button")
  .onclick = sayHello;
```

Exactly the same code-wise with the previous.

This method gives us more flexibility with the `this` pointing at the button itself.

Now a Lifecycle event example:

These scripts can go in the head, instead of the body bottom


```javascript
document.addEventListener("DOMContentLoaded",
  function(event) {
    function sayHello () {
        var name = 
          document
            .getElementByID("name")
            .value; // gives us the value instead of the element.
        var message = "<h2>Hello " + name " "!</h2>";
        docuemnt
          .getElementByID("content")
          .innerHTML = message;     // This puts the message Into the div with id 'content'!

        if ( name == "student") {
          var title = 
            document
              .querySelector("#title") //specify via CSS selector syntax
              .textContent;
          title += " & Lovin' it!";   //we haven't change the actual value, just a copy.
          document
            .querySelector("h1") //can also query on the h1!
            .textContent = title;
        }
    }
  }
}
```

Now since the JS loads only after the DOM loads, we can place the JS at the top. This assures the other elements exist

### Lecture 55 - The 'event' Argument

The JS engine passes the `event` argument into every single event handler.  

When you log the `event` out you get, for example, a `mouseEvent`.  
Includes a bunch of helpful info about how the mouse event happened.

Can get more info on the Mozzila Developer Network about events.

Can be related to keyboard, mouse movement, mouse actions, zoom, input. Many options.

All of these have properties, which are defined there as well.

Let's define another event function based on mouse movement and shift.

In our script.js, in the `document.addEventListener` section.

```javascript
document.querySelector("body")
  .addEventListener("mousemove", //on mouse movement
    function (event) {           //but only if Shift is pressed
      if (event.shiftKey) {
        console.log("x: " + event.clientX); //print x
        console.log("y: " + event.clientY); //print y
      }
    }
  );
```

Now in the console we see out X and Y coordinates as we move the mouse.

## Introduction to Ajax

### Lecture 56 - HTTP Basics

Before diving into Ajax, let's talk about the HTTP.

#### What is HTTP?

HyperText Transfer Protocol.

  -based on request/response stateless protocol
    - stateless - no state is remembered by the protocol. All requests are distinct
  - Normal connection process:
    - client opens the connection to the server
    - client sends HTTP req for a resource
    - server sends HTTP response to the client (w/ resource if no error)
    - client closes connection to server

#### Identifying Resources on the Web

**URN: Uniform Resource Name**

  - Uniquely identifier resource or name of resource
  - Does not tell us hot to get the resource

Example: 

"HTML/CSS/Javascipt/Web Developer/Yaakov/Chaikin"  
  Could be a unique name for the course that we are taking.
  But not unique _worldwide_, just on coursera.

**URI: Uniform Resource Identifier**

  - Uniquely identifies resource of location of resource
  - Does not necessarily tell us how to get the resource
    - the context is usually missing. More like a directory

Example:  
`/official_web_site/index.html` - only works in context of knowing the main site.

Personal note: This is an incorrect definition of URI. See [Wikipedia](https://en.wikipedia.org/wiki/Uniform_Resource_Identifier). Or the RFC. Or W3...


**URL: Uniform Resource Locator**

  - Form of URI that provides info on how to get the resource

Example:
  `http://www.mysite.com/path/index.html`

#### HTTP Request Structure (GET)

`GET /index.html?firstName=Yaakov HTTP/1.1`

  - `GET               ` - the method
  - `/index.html       ` - the URI string
  - `?firstName=Yaakov ` - optional QueryString (?name=value&name=value)
  - `HTTP/1.1          ` - HTTP Version

Where is the hostname? Not here. This is only issued after we have connected to a server. So every request is related to the server we are connected to.


#### HTTP Methods

  - GET
    - retrieves the resource
    - data is passed to server as part of the URI
      - i.e. query string
  - POST
    - sends data to server in order to be processed
    - data is sent in the message body
  - More methods not covered here
    - well, sort of.

#### HTTP Request Structure (POST)

```
POST /index.html HTTP/1.1  //
Host: coursera.org         // Request Headers
Accept-Charset: urf-8      //
firstName=Yaakov...    //
...                    //  Message Body
```


#### HTTP Response Structure

`HTTP/1.1 200 OK`

  - `HTTP/1.1 ` - the HTTP version
  - `200      ` - response status code
  - `OK       ` - English phrase describing status code
  - all are separated by a single space

#### Typical HTTP Response

```
HTTP/1.1 200 OK
Date: Tue, 11 Aug 2004 19:00:01 GMT
Content-Type: text/html
<html>
<body>
  <h1>WEEEE</h1>
  <p> I am the BEST EXAMPLE HTML RESPONSE </p>
</body>
</html>
```

#### Some Response Status Codes

  - 200 OK
    - Okay, here is the content you requested
  - 404 Not Found
    - Server can't find the resource requested
  - 403 Forbidden
    - Unauthenticated client tried to access a secure resource
  - 500 Internal Server Error
    - Some unhandled error was raised on the server

### Lecture 57 - Ajax Basics

### Lecture 58 - Processing JSON


## Using Ajax to Connect Restaurant Site with Real Data

### Lecture 59 - Fixing Mobile Nav Menu Automatic Collapse

### Lecture 60 - Dynamically Loading Home View Content

### Lecture 61 - Dynamic Loading Menu Categories View

### Lecture 62 - Dynamically Loading Single Category View

### Lecture 63 - Changing 'active' Button Style Through Javascript
