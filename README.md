Chatium Documentation
Here you can find all the information that will help you develop applications on the Chatium platform.
First steps
If you are new to Chatium development, this is the place for you:
✦
From scratch: What is the Chatium Platform? | Platform Features Overview
✦
General concepts: Chatium account | Routing basics | Markup | Navigation
Let's start
Create the developer account at chatium.com/dev
1.
First screen
2.
Create an action
3.
Entering text
4.
Passing the state
Reference
For quick access to information on the API provided by the platform:
✦
app-ui: Blocks | Actions
✦
Builtin modules: @app/responsive | @app/socket | @app/jobs

What is the Chatium platform
Chatium is a platform that allows you to quickly put together the functionality of a website and a mobile application in one language without installing either a server or a database.
Mobile apps in Chatium are built in a similar way to websites - each screen is a page that is accessible from a URL. In the browser, these pages open as a web site, and inside the application - as native screens.
With this approach, you can develop and update applications on the fly and immediately demonstrate and test functionality.
The main Chatium language is Typescript with JSX support
// Example code for a screen with one
// button that creates and records a user
app.screen('/', function(ctx,req) {
  return (
    <screen title='First screen'>
      <button onClick={ctx.router.apiCall('/register')}>
        Register
      </button>
    </screen>
  )
})

// Authorizes a user via sms and records them
app.apiCall('/register', function(ctx,req) {
  chatium.requireRealAuth();
  ctx.getOrCreateUser();
  return showToast('Thank you. You are registered!') ;
})
By placing this code in the root of your account in about.tsx, you will create a screen with a simple action that will be instantly available at https://<your domain>/about and will update as the code is edited.
You do not need to install a server, configure hosting and routing. The Chatium Platform provides the standard mechanisms required for application development:
✦
Application hosting
✦
Web version of the application on its own domain
✦
Built-in database
✦
Authorization
✦
Messaging
✦
File service with image thumbnailing
✦
Video hosting
✦
Audio hosting
✦
Web and desktop-IDE for development
✦
Building apps for stores

Platform Features Overview
Application hosting
Files placed in your Chatium account are immediately available for execution.
You do not need to separately install a server and configure it - immediately after creating an account, you can start writing business logic and the application's screens.
The application works in the browser right away. You can also open it in the Chatium application or build an application of your own.
Web version of the application on its own domain
Initially, your application is published on the <name>.chatium.com domain.
You can use it as a mobile website, or set a separate view for the desktop version. You do not need to write the business logic of your application twice - all the logic works simultaneously on both the website and the mobile application.
After the application is ready, it can be run on your own domain and have an SSL certificate issued.
Built-in database
To store the data, all you have to do is describe the table and fields, and it immediately becomes available from the code. Chatium's built-in database is called Heap and provides standard NoSQL table capabilities - adding, editing, retrieving and filtering data.
Authorization
With a help of just a couple of lines of code, you can enable sms authorization for certain screens or actions.
Messaging
The functionality to embed chats into your application is at the foundation of Chatium. Chats can be used for any communication with the users - feedback, group chats or for notifications.
File service
Media files uploaded to your account are available for immediate integration into the application: videos, pictures or even an entire playlist of audio files to be added into a screen.
You can also give a simple option for users to upload their media files, take photos or videos directly from the app's camera, or select from the phone gallery.
IDE
Manipulating account files can be done either via a browser or locally using the Microsoft Visual Studio Code extension.
The IDE is integrated with the internal git-repository of the account and allows you to edit the application code on the go, while it is running.
Building apps for stores
After the application is developed and tested in bothe the browser and the mobile preview application, you can build application binaries for iOS and Android.
Next. Chatium Account

Chatium account
One application - one account
Account is an isolated space that stores application code and data.
Each account is assigned an address of the form <name>.chatium.com with its own file system inside. You can edit account files from a browser or using the VS Code extension.
The person who has registered the account is the owner. They can invite others and assign them administrator roles.

My accounts
Files are immediately available to users after being uploaded to the account.
Static files
Static files such as html, css, js, as well as media files are available in a path that matches the filename with the containing folder.
A file named first.html will open at
https://test.chatium.com/first.html
And a file in the collection folder named f2.html will open at
https://test.chatium.com/collection/f2.html
Typescript files
Files with the extension .tsx allow dynamic response to a query.
The addresses inside them are registered using the methods:
✦
app.screen
✦
app.apiCall
✦
app.get
✦
app.post
/**
 * Example: about.tsx file in the account named test
 * The screen will open at:
 * https://test.chatium.com/about
 */
app.screen('/', function() {
    return (
        <screen title="About">
            <text>
                Some text about the company
            </text>
            <button class="primary" onClick={ctx.account.navigate('/)}>
                Main page
            </button>
        </screen>
    )
})
An incoming request launches the code of a relevant .tsx file, which in turn produces a response on the fly.


Routing basics
Routing is based on a few simple principles:
✦
The request is processed by a file corresponding to the path specified in the URL of HTTP request.
✦
One file can handle several different requests. To find a matching route within a handler file, a part of the request URL fillowing the  ~  (tilde) character is considered.
✦
The file extensions  .ts  and  .tsx  can be omitted from the URL (i.e., instead of https://test.chatium.com/example.tsx, you can make a request to https://test.chatium.com/example).
✦
If the path in the request URL matches a directory, the system will try to find an index file in this directory ( index.html ,  index.ts(x) or  index ) and select it as the handler.
Upon processing, the request URL is divided into 3 parts:
https://accname.chatium.com /folder1/about ~part1
✦
domain determines the account. Can be a subdomain on chatium.com or a custom one.
✦
  Path   - accounting for nesting and directories. May or may not contain the .tsx file extension.
✦
part - optional part, points to a method inside the file. Can be appended to a url via tilde. Can be used to hold variables, such as product id etc.
Looking for a request handle withing the account, a matching file is searched for ignoring  .ts  and  .tsx  extensions. In the found file, a suitable callback is chosen, with the  /  template being the default one. The callback is executed and its output is returned back to the user.
/**
 * File /company/about.tsx in the account named test
 * The screen opens at the address:
 * https://test.chatium.com/company/about
 */
app.screen('/', function() {
  return <screen title="About"/>
})
Files with the extensions .ts and .tsx register address patterns in their code using the methods:
✦
app.screen - processes GET requests, returns a Screen
✦
app.get - processes GET requests, returns any type of data
✦
app.post - processes POST requests, returns any type of data
✦
app.apiCall - processes POST requests, returns an Action
Each of these methods has 2 parameters:
✦
pattern - a string pattern that is compared against part in the address. If part is not present in the address,  /  is used as a default.
✦
callback - request handler function.
How the path pattern works
All examples are given for an account named test, with the relevant account address being test.chatium.com.
If the address does not have part, i.e. there is nothing after the tilde in the url, the root template is then used.
/**
 * File /catalog.tsx in the account named test
 * The screen opens at the address:
 * https://test.chatium.com/catalog
 */
app.screen('/', function() {
  return <screen title="Catalogue"/>
})
If template is static, then a direct match with part is searched.
/**
 * File /catalog.tsx in the account named test
 * The screen opens at the address:
 * https://test.chatium.com/catalog~list
 */
app.screen('/list', function() {
  return <screen title="List of goods"/>
})
The path template can have dynamic parameters.
/**
 * File /catalog.tsx in the account named test
 * The screen opens at the addresses matching the template:
 * https://test.chatium.com/catalog~item/:id
 *
 * such as:
 * https://test.chatium.com/catalog~item/15
 * https://test.chatium.com/catalog~item/00001
 */
app.screen('/item/:id', function(ctx,req) {
  return (
    <screen title="Товар">
      <text>{req.params.id}</text>
    </screen>
  )
})
Callback function
The purpose of the callback function is to return a result to the user's request. Has two parameters: ctx and req.
✦
ctx - the context in which the current request is being executed. Contains information about the user, account, environment, router, device and screen size from which the user made the request.
✦
req - query parameters, for example queryString or params.
Such callback must return a data structure matching the request. A screen for app.screen, and any data for app.get, for example a JSON object.


Markup
The screen in Chatium is composed using JSX
// Screen code example
app.screen('/', function(ctx,req) {
  return (
    <screen title='First screen'>
      <text>Some text on the screen</text>
    </screen>
  )
})
The function must return an object with the <screen> as the root tag, and other blocks (tags) contained within.
Blocks
Blocks are laid out using tags similar to those of HTML. Except they are the tags from the set of app-ui. The list of built-in tags is much smaller than in HTML (just 19 of), but all of them can be displayed in a mobile application as native components.
audio, box, button, footer, header, horizontal-scroll, horizontal-scroll-indicator, iap-product, icon, image, list-item, parallax, smart-icon, search-input, sticky, text, text-input, video, video-poster,
Many tags can be expressed via jsx blocks with certain styles.
For example, Chatium doesn't have the <i> tag, but you can express it via the text tag with a style:
<text style={{fontStyle: 'italic'}}>
  italic text
</text>
Just like in HTML, some blocks can contain other blocks, while other are atomic (always empty).
// section is a kind of a block that can nest other blocks
<section>
  // while image can't nest any blocks
  <image src={{url: "https://chatium.com/s/static/img/chatium.png"}}/>
</section>
JSX  blocks can be combined into components with specified styles and functionality. Such tags start with a capital letter and can have their own parameters.
// Using a custom component of direct speech
<Quote author="Winston Churchill">
  This is not the end. Not even the beginning of the end. But it is the end of the beginning.
</Quote>
Actions
Interacting with the screen, the user can trigger events associated with blocks. Actions can be set to be called upon particular events.
The most commonly used event is a click on a block. This event is called onClick.
<box onClick={ACTION}>
  Click me
</box>
The action can be an object or an array of objects. The @app/ui module has methods that prepare such objects. As an example we can list the following actions:apiCall, attachMedia, goBack, navigate, showToast, showContextMenu, showTextDialog, refresh, The full list of actins can be seen here
// Clicking on the button will display the message "Hello World"
<button onClick={showToast('Hello World')}>
  Click me
</button>
Action objects can be prepared by other objects. For example, ctx.account object should be used to prepare a link action to a page within the account.
// Clink will open Google:
<text class="link" onClick={navigate('https://google.com')}>
  Open Google
</text>
// Click will open a page within the account:
<text class="link" onClick={ctx.account.navigate('/about')}>
  To the page "About the company"
</text>
// Click will call an action described in the same file:
<button class="primary" onClick={ctx.router.apiCall('/do')}>
  Register
</button>

Navigation
As was mentioned earlier, files hold the code for request handlers, bound to request address patterns and request types. These handlers produse user output.
/**
 * File /catalog.tsx in the account named test
 * The screen opens at the address:
 * https://test.chatium.com/catalog
 */
app.screen('/', function(ctx,req) {
    return (
        <screen title="Catalog"/>
    )
})
Navigation action
To direct the user to another screen, use the action navigate
This action is applicable for any URL, including those on external services.
Most often, navigation occurs within the application or even within a single file. Considering that the domain of the application can be changed, it is recommended to use relative URLs instead of absolute for the internal navigation. To aid the relative navigation, Chatium offers objects  ctx.account  and  ctx.router . Where  ctx  is the first parameter for every request hanler function.
ctx.account
Defines the account for the currently executed code. Holds id and the name of the account. To form any url inside the account, method  ctx.account.url()  can be used.
ctx.account.url('/page')
To create a navigate action on any URL inside the account, the method  ctx.account.navigate()  should be used.
/**
 * File /catalog.tsx in the account named test
 * The screen opens at the address:
 * https://test.chatium.com/catalog
 */
app.screen('/', function(ctx,req) {
    return (
        <screen title="Catalog"/>
            <button onClick={ctx.account.navigate('/')}>
                Open the main page
            </button>
        </screen>
    )
})
ctx.router
Defines the entry point (file) for the request. The currently executed code can be stored in any file, but  ctx.router  defines the handler's origin.
There can be a multiple of handlers with different address templates per file. To find a suitable handler within the file, a part of the request URL fillowing the  ~  (tilde) character is considered. If no suitable template is found, 404 error is turned.
/**
 * File /catalog.tsx in the account named test
 * The screen opens at the address:
 * https://test.chatium.com/catalog
 */
app.screen('/', function(ctx,req) {
    return (
        <screen title="Catalog"/>
            {/* NOTE:
              * Using ctx.router.navigate to obtain a navigation
              * action relevant to the handler's origin
              */}
            <button onClick={ctx.router.navigate('/list')}>
                To the list of goods
            </button>
        </screen>
    )
})

/**
 * File /catalog.tsx in the account named test
 * The screen opens at the address:
 * https://test.chatium.com/catalog~list
 */
app.screen('/list', function(ctx,req) {
    return (
        <screen title="Catalog. List of the goods."/>
           
        </screen>
    )
})


Creating the first screen
When we talk about a mobile application, the first thing that comes to mind is the interface, because this is what makes a mobile application different from any other.
In reality, a complete mobile application requires a huge number of invisible components for its operation: this is a server that processes requests, a database that stores data, a video hosting, a collection of queues that run code at a certain time, and number of sockets that allow different clients to notify each other about changes.
This is what sets Chatium apart from most app builders - we provide all the infrastructure you need to release a production app.
But we will start by creating a screen. To do this, let's create our first TypeScript file, name it start.tsx and put this code in it:
chatium.screen('/', async function(ctx, req) {
  return <screen title="My screen"></screen>
})
Let's save it and see what happens after clicking the "Preview" button. We should see a blank screen titled My Screen.
Let's add the first block to this screen. To do this, inside the <screen> tag, we add the <text> tag containing some text.
<text>Hello world</text>
We see that our text fits too tightly to the borders of the phone. In order to space it away, add the section class to it.
<text class="section">Hello world</text>
Now we are going to add a button to our screen below the text. To do this, we use the button tag. Let's give it a primary class to indicate that this is the main button on the screen and a section class to add padding.
<button class={["primary","section"]} title="Click me"/>
So far this button does nothing. To change that, let's take the showToast action and add it to the button's onClick property.
<button onClick={showToast('Hi!')} class={["primary","section"]} title="Click me"/>
And import showToast itself from @app/ui at the first line of our source code.
// import showToast action
import {showToast} from '@app/ui'

// Main screen
chatium.screen('/', async function(ctx, req) {
  return <screen title="My screen">
    <text class="section">Hello world</text>
    <button onClick={showToast('Hi!')} class={["primary","section"]} title="Click me"/>
  </screen>
})
Add a second screen and make a link to it
Let's try to create a second screen and link these 2 screens together.
Now add another call to chatium.screen at the bottom of the same file, with the /catalog as the value of the first argument.
// Catalog screen
chatium.screen('/catalog', async function(ctx, req) {
  return <screen title="Catalog">
    <text class="section">Catalog items</text>
  </screen>
})
This screen can be seen if we add ~catalog to the address of our file.
For this, in the first screen, instead of showToast, we add navigate action to the catalog screen.
<button
  onClick={ctx.router.navigate('/catalog')}
  class={["primary","section"]}
  title="Click me"/>
In this action, we used the /catalog argument - also the first argument of the ctx callback function.
ctx - is the execution context of our code. It contains information about the user, his device and the environment in which the code is running:
✦
ctx.account - object that defines the account for the currently executed code. Holds id and the name of the account.
✦
ctx.router - object that defines the entry point (file) for the request. The currently executed code can be stored in any file, but ctx.router defines the handler's origin.
Method  chatium.screen  binds a screen request handler to a URL path template. The tamplate is a part of the URL to access the screen. And the full URL to access the screen is relevant to the code point of entry: the file that has executed  chatium.screen .
Note the difference between the code point of execution and the containing file.
So far our example had one file and one path template: start.tsx and /catalog. That make the example accessible at the URL https://accout-domain/start~catalog.
The handler can be moved to a different file, leaving only the binding to the original place.
import CatalogScreen from './catalog-screen.tsx'
chatium.screen('/catalog', CatalogScreen})
This keeps start.tsx as the code execution point and addresses remain the same.
Thus, the  ctx.router.navigate  method provides navigation to another screen in a particular file.
Let's add a link to the second screen that takes us back to the first.
<text onClick={ctx.router.navigate('/')} class="link">
  Go to main screen
</text>
Now we have a file that contains 2 screens and navigation between them.
Final code
// import showToast action
import {showToast} from '@app/ui'

// Main screen
chatium.screen('/', async function(ctx, req) {
  return <screen title="My screen">
    <text class="section">Hello world</text>
    <button
      onClick={ctx.router.navigate('/catalog')}
      class={["primary","section"]}
      title="Click me"
    />
  </screen>
})

// Catalog screen
chatium.screen('/catalog', async function(ctx, req) {
  return <screen title="Catalog">
    <text class="section">Catalog items</text>

    <text onClick={ctx.router.navigate('/')} class="link">
      Go to main screen
    </text>
  </screen>
})
Try to create this file in your account. After that, scan the QR code in the lower right corner with the Chatium app and see how it works in the native app.



Create an action
So far, we have learned how to make screens and navigate between them. Now we will create code that is not represented in blocks on the screen, but can be called from it.
As a simple example: ask user a question with three possible answers. Depending on the user's action, the correct answer will show him a message that he is right, and the rest that he is wrong.
So, let's create a file question.tsx, enter the initial code and run it.
// Import showToast action
import {showToast} from '@app/ui'

// Screen with 3 buttons and question
app.screen('/', async function(ctx, req) {
  return <screen title="Question">
   
    <text class="section">The Capital of Great Britain</text>
     
    <button onClick={showToast('Click')} class={["secondary","section"]} title="New York"/>
    <button onClick={showToast('Click')} class={["secondary","section"]} title="Liverpool"/>
    <button onClick={showToast('Click')} class={["secondary","section"]} title="London"/">
  </screen>
})
We have 3 buttons on the screen. Each of them has 2 classes - secondary and section. Secondary to make the button gray. Section to add standard indentation. Alternatively a  section  wrapping block can be used to add such indentation to a group of elements.
All three buttons do the same thing - show a message on the screen. Let's create another action and place its code below such of the screen. For this, we use the method  app.apiCall .
// Handler for button
app.apiCall('/check', async function(ctx, req) {
  return showToast('Clicked');
})
This code in the file question.tsx binds the action handler to the path template check. The handler responds to a POST request on the path question~check.
In order for this action to be called upon a click on the button, a corresponding call needs to be initiated in  onClick . Let's do this for the first button.
<button
  onClick={ctx.router.apiCall('/check', { value: 'New York' })}
  class={["secondary","section"]}
>
  New York
</button>
Note how in this code we use  ctx.router.apiCall  to form a request with parameers. A request to call a handler previously bound to /check path using  app.apiCall . Hence  ctx.router.apiCall  should be seen as a companion method of  app.apiCall .
The parameters are passed as the second argument. They set as an object with the names and values of the parameters.
To obtain the passed parameters, we finally get to use the second argument in the handler  req  containing all of the request parameters. There,  req.body  contains all the parameters that we passed in the  ctx.router.apiCall .
Let's replace the static code with the value from the request.
// Handler for button
app.apiCall('/check', async function(ctx, req) {
  return showToast('You are clicked: ' + req.body.value);
})
Now let's slightly modify the code so that a message saying "Correct" was displayed for the right answer and another otherwise.
// Handler for button
app.apiCall('/check', async function(ctx, req) {
  if ( req.body.value === 'London' ) {
    return showToast("Correct")
  }
  else {
    return showToast(req.body.value + ' is not correct');
  }
})
Let's insert these actions in all 3 buttons and see how it works.
app.screen('/', async function(ctx, req) {
  return <screen title="Question">
   
    <text class="section">The Capital of Great Britain</text>
     
    <button onClick={ctx.router.apiCall('/check', { value: 'New York'})} class={["secondary","section"]} title="New York"/>
    <button onClick={ctx.router.apiCall('/check', { value: 'Liverpool'})} class={["secondary","section"]} title="Liverpool"/>
    <button onClick={ctx.router.apiCall('/check', { value: 'London'})} class={["secondary","section"]} title="London"/>
  </screen>
})
As we see there is a lot of repetitive code. To avoid this, we will separate the data from the presentation and modify the code so it has no repetitions. At the same time, we will slightly change the verification code to make it shorter.
Resulting code
// Import showToast action
import {showToast} from '@app/ui'

const question = {
  label: 'The Capital of Great Britain',
  answers: [ 'New York', 'Liverpool', 'London' ],
  correct: 'London',
}

// Screen with 3 buttons and question
app.screen('/', async function(ctx, req) {
  return <screen title="Question">

    <text class="section">{question.label}</text>

    {question.answers.map( answer =>
      <button
        onClick={ctx.router.apiCall('/check', { value: answer })}
        class={["secondary","section"]}
        title={answer}
      />
    )}
  </screen>
})

// Handler for button
app.apiCall('/check', async function(ctx, req) {
  return  ( req.body.value === question.correct )
    ? showToast("Correct")
    : showToast(req.body.value + ' is not correct');
})

Entering text
In the previous lesson we have learned how to describe an action code and call it when the button is clicked.
In this tutorial, we will learn how to use a text field to enter an answer. As a starting point let's take the code we have created in the previous lesson. You can either use the same file or copy the contents to a new one.
// Import showToast action
import {showToast} from '@app/ui'

const question = {
  label: 'The Capital of Great Britain',
  answers: [ 'New York', 'Liverpool', 'London' ],
  correct: 'London',
}

// Screen with 3 buttons and question
app.screen('/', async function(ctx, req) {
  return <screen title="Question">
   
    <text class="section">{question.label}</text>
   
    {question.answers.map( answer =>
      <button
        onClick={ctx.router.apiCall('/check', { value: answer })}
        class={["secondary","section"]}
        title={answer}
      />
    )}
  </screen>
})

// Handler for button
app.apiCall('/check', async function(ctx, req) {
  return  ( req.body.value === question.correct )
    ? showToast("Correct")
    : showToast(req.body.value + ' is not correct');
})
To recall what this code does: it displays a question with a number of answers to it. A click on the right answers displays a message saying "Correct", where a click on a wrong answer says otherwise.
Let's make the task a bit harder: make it possible to enter an answer of your own.
And as well remove the correct answer from the list of suggested:
const question = {
  label: 'The Capital of Great Britain',
  answers: [ 'New York', 'Liverpool', 'Manchester' ],
  correct: 'London',
}
Now add to the screen one more button titled Different answer... under all other buttons.
{question.answers.map( answer =>
  <button
    onClick={ctx.router.apiCall('/check', { value: answer })}
    class={["secondary","section"]}
    title={answer}
  />
)}
<button
  class={["secondary","section"]}
  title="Different answer..."
/>
Now we need to make a click on this button ask the user for an answer of their own and get it submitted for verification.
For this we can use showTextDialog method, after having it imported at the beginning of the file:
import {showTextDialog} from '@app/ui'
This method allows to present an input field and submit the entered text to a specified URL. To have this URL be relevant to the current router, we will use  ctx.router  again. Except this time instead of  ctx.router.apiCall  that returns a complete action, we use  ctx.router.url  that gives a URL only.
<button
  class={["secondary","section"]}
  title="Different answer..."
  onClick={showTextDialog({
    submitUrl: ctx.router.url('/check'),
    title: 'Enter your answer'
  })}
/>
Everything should now work as is. We don't even need to edit the handler code. That is because showTextDialog sends the entered text to the same value parameter available in the handler at  req.body.value .
Resulting code
// Import showToast action
import {showToast} from '@app/ui'

const question = {
  label: 'The Capital of Great Britain',
  answers: [ 'New York', 'Liverpool', 'Manchester' ],
  correct: 'London',
}

// Screen with 3 buttons and question
app.screen('/', async function(ctx, req) {
  return <screen title="Question">
   
    <text class="section">{question.label}</text>
   
    {question.answers.map( answer =>
      <button
        onClick={ctx.router.apiCall('/check', { value: answer })}
        class={["secondary","section"]}
        title={answer}
      />
    )}
    <button
      class={["secondary","section"]}
      title="Different answer..."
      onClick={showTextDialog({
        submitUrl: ctx.router.url('/check'),
        title: 'Enter your answer'
      })}
    />
  </screen>
})

// Handler for button
app.apiCall('/check', async function(ctx, req) {
  return  ( req.body.value === question.correct )
    ? showToast("Correct")
    : showToast(req.body.value + ' is not correct');
})


Passing the state
In the previous lesson we have learned to work with user text input.
Let's improve the UX a little more: color the button green if the person gave a correct answer and red otherwise. For the start, we are going to take the code created in the previous lesson. With intent of making it simplier, remove the button for a user's own answer and bring back a button with the correct answer to the list.
You can continue in question.tsx or create a new file.
// Import showToast action
import {showToast} from '@app/ui'

const question = {
  label: 'The Capital of Great Britain',
  answers: [ 'New York', 'Liverpool', 'London' ],
  correct: 'London',
}

// Screen with 3 buttons and question
app.screen('/', async function(ctx, req) {
  return <screen title="Question">
   
    <text class="section">{question.label}</text>
   
    {question.answers.map( answer =>
      <button
        onClick={ctx.router.apiCall('/check', { value: answer })}
        class={["secondary","section"]}
        title={answer}
      />
    )}
  </screen>
})

// Handler for button
app.apiCall('/check', async function(ctx, req) {
  return  ( req.body.value === question.correct )
    ? showToast("Correct")
    : showToast(req.body.value + ' is not correct');
})
To recall what this code does: it displays a question with a number of answers to it. A click on the right answers displays a message saying "Correct", where a click on a wrong answer says otherwise.
So our intent is to make an answer button change the colour upon a click. In the classic react approach, this would have been a screen state change - react changes the state on the client and applies it right there.
In our case, it is important to understand one thing:
There is no client state in Chatium
We have to produce a new screen on the server side every time we are changing its state. The state can be changed either via change of the data, or even simply via a change in the URL.
For example, you can add something to the URL query segment - the part following the question mark.
In the screen code itself, you can look at the  req.query  object. It receives a query from the HTTP request. Therefore, if we pass  ?q=value1  in the address, then in the screen code we can get this value via  req.query.q .
Let's change the button click handler so that it returns us to the screen with response indication.
// Handler for button
app.apiCall('/check', async function(ctx, req) {
  const messageAction = ( req.body.value === question.correct )
    ? showToast("Correct")
    : showToast(req.body.value + ' is not correct');

  const redirectAction = ctx.router.navigate('/?answered=' + req.body.value, {replace: true});

  return [messageAction, redirectAction];
})
In this code, we declare 2 actions and return them in an array. The application will execute them sequentially: first it will show the message, and then it will navigate the user.
Note the second argument to  navigate . There we pass {replace: true} which does a replacement of the current screen instead of opening a new one.
Now we will display the value of  answered  on the screen to make sure everything is ocrrect.
Output the answer below the question.
<text class="section">{question.label}</text>
{req.query?.answered && <text class="section">Answered: {req.query.answered}</text>}
Note the condition  {req.query?.answered &&  . It makes the tag  <text>...</text>  be rendered only if  req.query  exists and  req.query.answered  contains something.
Let's apply this logic to the colorization of the buttons.
At the moment each option button has 2 classes:  {['secondary','section']} . We are altering this code so that instead of  'secondary'  there is a variable responsible the color.
{question.answers.map( answer => {

  let buttonClass = "secondary"
  if ( req.query?.answered == answer ) {
    buttonClass = answer == question.correct ? "success" : "danger"
  }
  return <button
      onClick={ctx.router.apiCall('/check', { value: answer })}
      class={[ buttonClass, "section"]}
      title={answer}
    />
  }
)}
This code displays the buttons. The one clicked by the user will be colored: assigned the class "success" if this answer is correct, or the class "danger" if not.
Resulting code
// Import showToast action
import {showToast,showTextDialog} from '@app/ui'

const question = {
  label: 'The Capital of Great Britain',
  answers: [ 'New York', 'Liverpool', 'London' ],
  correct: 'London',
}

// Screen with 3 buttons and question
app.screen('/', async function(ctx, req) {

  return <screen title="Question">
   
    <text class="section">{question.label}</text>
   
    {question.answers.map( answer => {

      let buttonClass = "secondary"
      if ( req.query && req.query.answered == answer ) {
        buttonClass = answer == question.correct ? "success" : "danger"
      }
      return <button
          onClick={ctx.router.apiCall('/check', { value: answer })}
          class={[ buttonClass ,"section"]}
          title={answer}
        />
      }
    )}
  </screen>
})

// Handler for button
app.apiCall('/check', async function(ctx, req) {
  const messageAction =  ( req.body.value === question.correct )
    ? showToast("Correct")
    : showToast(req.body.value + ' is not correct');
  const redirectAction = ctx.router.navigate('/?answered=' + req.body.value)
  return [messageAction,redirectAction]
})


Reference
For quick access to information on the API provided by the platform:
✦
app-ui: Blocks | Actions
✦
Builtin modules: @app/responsive | @app/socket | @app/jobs



audio
The block provides an audio playback component.
Properties
src*: { url, durationSeconds }
Audio file information.
url*: string
Audio file URL.
durationSeconds: number
Audio duration in seconds.
title: string
Block title. Has a bold style.
subTitle: string
Block subtitle.
style
Changing appearance, position and geometry of the block.
width,
maxWidth,
minWidth
Block width. Details.
height,
maxHeight,
minHeight
Block height. Details.
margin,
marginHorizontal,
marginVertical,
marginTop,
marginRight,
marginBottom,
marginLeft
External indentation of the block. Details.
position
Defines the block positioning method: relative or absolute. Details.
top,
right,
bottom,
left
Define the offset relative to the initial position. Details.
border
Sets the border for the block on all four sides: width, style and color. Details.
borderTop,
borderRight,
borderBottom,
borderLeft
Setting individual borders for the block edges: width and color. Details.
borderRadius,
borderBottomLeftRadius,
borderBottomRightRadius,
borderTopLeftRadius,
borderTopRightRadius
Rounding of the border corners. Details.
borderWidth,
borderTopWidth,
borderRightWidth,
borderBottomWidth,
borderLeftWidth
Width of the border. Details.
borderColor,
borderTopColor,
borderRightColor,
borderBottomColor,
borderLeftColor
Color of the border. Details.
backgroundColor
Color of the block background. Details.
backgroundGradient
Gradient of the block background. Details.
opacity
Opacity (non-transparency) of the element with its content. Details.
elevation
Block shadow height. Details.
shadowColor
Block shadow color. Details.
flexBasis
Sets the initial block size along the container primary axis. Details.
flexGrow
Block coefficient of growth along the primary axis. Details.
flexShrink
Block coefficient of shrink along the primary axis. Details.
flex
Sets the block behaviour (grow, shrink and basis) along the primary container axis. Details.
alignSelf
Alignment of a specific block across the primary container axis. Details.
Examples
<audio
  src={{
    url: "https://fs.chatium.io/get/audio_ANlDoIs0xm.d12.wav",
    durationSeconds: 11
  }}
  title="Drum solo"
  subTitle="Enjoy the groove"
/>

box
Container for elements grouping. Similar to div in HTML. Supports flex alike in HTML.
Properties
style
Changing appearance, position and geometry of the block.
width,
maxWidth,
minWidth
Block width. Details.
height,
maxHeight,
minHeight
Block height. Details.
overflow
Specifies if the content should be clipped upon taking space beyond the container boundaries. Details.
padding,
paddingHorizontal,
paddingVertical,
paddingTop,
paddingRight,
paddingBottom,
paddingLeft
Internal container indentation. Details.
margin,
marginHorizontal,
marginVertical,
marginTop,
marginRight,
marginBottom,
marginLeft
External indentation of the block. Details.
position
Defines the block positioning method: relative or absolute. Details.
top,
right,
bottom,
left
Define the offset relative to the initial position. Details.
border
Sets the border for the block on all four sides: width, style and color. Details.
borderTop,
borderRight,
borderBottom,
borderLeft
Setting individual borders for the block edges: width and color. Details.
borderRadius,
borderBottomLeftRadius,
borderBottomRightRadius,
borderTopLeftRadius,
borderTopRightRadius
Rounding of the border corners. Details.
borderWidth,
borderTopWidth,
borderRightWidth,
borderBottomWidth,
borderLeftWidth
Width of the border. Details.
borderColor,
borderTopColor,
borderRightColor,
borderBottomColor,
borderLeftColor
Color of the border. Details.
backgroundColor
Color of the block background. Details.
backgroundGradient
Gradient of the block background. Details.
opacity
Opacity (non-transparency) of the element with its content. Details.
elevation
Block shadow height. Details.
shadowColor
Block shadow color. Details.
flexDirection
Defines the container primary axis (blocks direction in the container). Details.
flexBasis
Sets the initial block size along the container primary axis. Details.
flexGrow
Block coefficient of growth along the primary axis. Details.
flexShrink
Block coefficient of shrink along the primary axis. Details.
flex
Sets the block behaviour (grow, shrink and basis) along the primary container axis. Details.
justifyContent
Alignment and distribution of the space along the primary container axis. Details.
alignItems
Alignment of the blocks across the primary container axis. Details.
alignContent
Distribution of space across the container primary axis. Details.
alignSelf
Alignment of a specific block across the primary container axis. Details.
flexWrap
Wrapping of the blocks on the new line along the primary container axis. Details.
transform
Transformation of block and container geometry in 2D and 3D space. Details.
Examples
<box style={{ padding: 10, margin: 10, border: [ 1, 'stroke', '#999'], borderRadius: 15 }}>
  <text><box> is a fundamental markup element. A container similar to div in html.</text>
</box>

<box style={{ flexDirection: 'row' }}>
  <box class='section' style={{ flex: 1 }}>
    <text>Box supports flex</text>
    <text>And can be nested in another box</text>
  </box>
  <box class='section' style={{ flex: 1 }}>
    <button class='primary'>Click me</button>
    <text>За счет этого можно располагать блоки как угодно</text>
  </box>
</box>
<box> is a fundamental markup element. A container similar to div in html.
Box supports flex
And can be nested in another box

Click me
That way the blocks can be arranged as needed.


button
Button
Properties
class: string | Array<string>
Button class
Possible values: section, primary, secondary, success, danger, warning, info, light, dark, link
style
Changing appearance, position and geometry of the block.
width,
maxWidth,
minWidth
Block width. Details.
height,
maxHeight,
minHeight
Block height. Details.
padding,
paddingHorizontal,
paddingVertical,
paddingTop,
paddingRight,
paddingBottom,
paddingLeft
Internal container indentation. Details.
margin,
marginHorizontal,
marginVertical,
marginTop,
marginRight,
marginBottom,
marginLeft
External indentation of the block. Details.
position
Defines the block positioning method: relative or absolute. Details.
top,
right,
bottom,
left
Define the offset relative to the initial position. Details.
color
Color of the text. Details.
fontFamily
Name of the font family. Details.
fontSize
Size of the font. Details.
fontStyle
An option to make the font cursive. Details.
fontWeight
Weight of the font (boldness). Details.
fontVariant
An option to make all the digits same width Details.
letterSpacing
Spacing between the letters. Details.
lineClamp
Limiting the height of the text by the number of lines. Details.
lineHeight
Sets the amount of space allocated for a single line of text. Details.
textAlign
Sets the text alignment. Details.
textDecorationLine
Underline or line-through decoration for the text. Details.
textOverflow
Method for text truncation when breaching the container boundaries. Details.
border
Sets the border for the block on all four sides: width, style and color. Details.
borderTop,
borderRight,
borderBottom,
borderLeft
Setting individual borders for the block edges: width and color. Details.
borderRadius,
borderBottomLeftRadius,
borderBottomRightRadius,
borderTopLeftRadius,
borderTopRightRadius
Rounding of the border corners. Details.
borderWidth,
borderTopWidth,
borderRightWidth,
borderBottomWidth,
borderLeftWidth
Width of the border. Details.
borderColor,
borderTopColor,
borderRightColor,
borderBottomColor,
borderLeftColor
Color of the border. Details.
backgroundColor
Color of the block background. Details.
backgroundGradient
Gradient of the block background. Details.
opacity
Opacity (non-transparency) of the element with its content. Details.
elevation
Block shadow height. Details.
shadowColor
Block shadow color. Details.
flexBasis
Sets the initial block size along the container primary axis. Details.
flexGrow
Block coefficient of growth along the primary axis. Details.
flexShrink
Block coefficient of shrink along the primary axis. Details.
flex
Sets the block behaviour (grow, shrink and basis) along the primary container axis. Details.
alignSelf
Alignment of a specific block across the primary container axis. Details.
transform
Transformation of block and container geometry in 2D and 3D space. Details.
onClick: ChatiumActions
Action on click
onContext: ChatiumActions
Long press action on phone or right click on computer
Examples
<button class="primary">Order</button>

Order
<button class="primary">Contact</button>

Contact
<button class="warning">Move</button>

Move
<button class="danger">Delete</button>

Delete
<button class="info">info</button>

Info
<button class="success">Thank you</button>

Thank you
<button class="light">Turn light on</button>

Turn light on
<button class="dark">Turn light off</button>

Turn light off
<button class="link">Term sheet</button>

Term sheet
<button class={["section","secondary"]}>
  Section
</button>

Section

Ask AI to edit or generate...

footer
A container positioned along the bottom edge of the device screen. <footer> is displayed over any content that does not fit the visible screen area.
Properties
style
Changing appearance, position and geometry of the block.
height,
maxHeight,
minHeight
Block height. Details.
padding,
paddingHorizontal,
paddingVertical,
paddingTop,
paddingRight,
paddingBottom,
paddingLeft
Internal container indentation. Details.
margin,
marginHorizontal,
marginVertical,
marginTop,
marginRight,
marginBottom,
marginLeft
External indentation of the block. Details.
border
Sets the border for the block on all four sides: width, style and color. Details.
borderTop,
borderRight,
borderBottom,
borderLeft
Setting individual borders for the block edges: width and color. Details.
borderRadius,
borderBottomLeftRadius,
borderBottomRightRadius,
borderTopLeftRadius,
borderTopRightRadius
Rounding of the border corners. Details.
borderWidth,
borderTopWidth,
borderRightWidth,
borderBottomWidth,
borderLeftWidth
Width of the border. Details.
borderColor,
borderTopColor,
borderRightColor,
borderBottomColor,
borderLeftColor
Color of the border. Details.
backgroundColor
Color of the block background. Details.
backgroundGradient
Gradient of the block background. Details.
opacity
Opacity (non-transparency) of the element with its content. Details.
flexDirection
Defines the container primary axis (blocks direction in the container). Details.
justifyContent
Alignment and distribution of the space along the primary container axis. Details.
alignItems
Alignment of the blocks across the primary container axis. Details.
alignContent
Distribution of space across the container primary axis. Details.
flexWrap
Wrapping of the blocks on the new line along the primary container axis. Details.
Examples
app.screen('footer', async (ctx, req) => {
  return (
    <screen>
      {/* other screen content */}
      <footer style={{ backgroundColor: 'lightgrey' }}>
        <text>This text is always displayed at the bottom of the device screen</text>
      </footer>
    </screen>
  )
})

Ask AI to edit or generate...



header
Screen header
Properties
mode: opaque | transparent
Default: opaque
 opaque  — Non transparent background. The header is displayed above the screen's visible area. Is the default value.
 transparent  — The background is absent. The header is displayed above the screen content. Doesn't allow display of title, subTitle or custom contents, except for rightButton.
title: string
Header title.
subTitle: string
Header subtitle.
rightButton: <button>
Button displayed on the right hand side of the header.
style
Changing appearance, position and geometry of the block.
width,
maxWidth,
minWidth
Block width. Details.
height,
maxHeight,
minHeight
Block height. Details.
padding,
paddingHorizontal,
paddingVertical,
paddingTop,
paddingRight,
paddingBottom,
paddingLeft
Internal container indentation. Details.
margin,
marginHorizontal,
marginVertical,
marginTop,
marginRight,
marginBottom,
marginLeft
External indentation of the block. Details.
color
Color of the text. Details.
fontFamily
Name of the font family. Details.
fontSize
Size of the font. Details.
fontStyle
An option to make the font cursive. Details.
fontWeight
Weight of the font (boldness). Details.
fontVariant
An option to make all the digits same width Details.
letterSpacing
Spacing between the letters. Details.
lineClamp
Limiting the height of the text by the number of lines. Details.
lineHeight
Sets the amount of space allocated for a single line of text. Details.
textAlign
Sets the text alignment. Details.
textDecorationLine
Underline or line-through decoration for the text. Details.
textOverflow
Method for text truncation when breaching the container boundaries. Details.
border
Sets the border for the block on all four sides: width, style and color. Details.
borderTop,
borderRight,
borderBottom,
borderLeft
Setting individual borders for the block edges: width and color. Details.
borderRadius,
borderBottomLeftRadius,
borderBottomRightRadius,
borderTopLeftRadius,
borderTopRightRadius
Rounding of the border corners. Details.
borderWidth,
borderTopWidth,
borderRightWidth,
borderBottomWidth,
borderLeftWidth
Width of the border. Details.
borderColor,
borderTopColor,
borderRightColor,
borderBottomColor,
borderLeftColor
Color of the border. Details.
backgroundColor
Color of the block background. Details.
backgroundGradient
Gradient of the block background. Details.
opacity
Opacity (non-transparency) of the element with its content. Details.
transform
Transformation of block and container geometry in 2D and 3D space. Details.
Examples
<header> with a title and subtitle. Grey colored background.
app.screen('headerEn', async (ctx, req) => {
  return (
    <screen>
      <header
        title='Header title'
        subTitle='Header subtitle'
        style={{ backgroundColor: 'lightgrey' }}
      />
      {/* The rest of the screen content */}
    </screen>
  )
})

<header> with a menu. Without a title or a background.
import { showContextMenu, navigate, showToast } from '@app/ui'

app.screen('headerMenuEn', async (ctx, req) => {
  const menu = [{
    title: 'Say "Hi!"',
    onClick: showToast('Hi!')
  },{
    title: 'Navigate to Google',
    onClick: navigate("https://google.com", {openInExternalApp: true})
  }]

  return (
    <screen>
      <header
        mode='transparent'
        rightButton={{ onClick: showContextMenu(menu) }}
      />
      {/* The rest of the screen content */}
    </screen>
  )
})

<header> with custom content.
import { showToast } from '@app/ui'

app.screen('headerCustomEn', async (ctx, req) => {
  return (
    <screen>
      <header style={{ backgroundColor: 'lightgrey' }} >
        <button class='primary' onClick={showToast('Hi!')}>A button in the header</button>
      </header>
      {/* The rest of the screen content */}
    </screen>
  )
})

<header> with custom icon button.
import { navigate } from '@app/ui'

app.screen('headerButtonEn', async (ctx, req) => {
  const icon = <icon name={['fas', 'external-link-alt']} size={20} />

  const onClick = navigate('https://google.com/', { openInExternalApp: true })

  const button = <button leftIcon={icon} onClick={onClick} />

  return (
    <screen>
      <header
        title='Open Google'
        style={{ backgroundColor: 'lightgrey' }}
        rightButton={await button}>
      </header>
      {/* The rest of the screen content */}
    </screen>
  )
})


horizontal-scroll
Blocks container with horizontal scrolling.
Properties
snapToBlocks: bool
Scroll sticking to the contained block edges.
showScrollBar: bool
Scrollbar display switch option.
style
Changing appearance, position and geometry of the block.
width,
maxWidth,
minWidth
Block width. Details.
height,
maxHeight,
minHeight
Block height. Details.
padding,
paddingHorizontal,
paddingVertical,
paddingTop,
paddingRight,
paddingBottom,
paddingLeft
Internal container indentation. Details.
margin,
marginHorizontal,
marginVertical,
marginTop,
marginRight,
marginBottom,
marginLeft
External indentation of the block. Details.
position
Defines the block positioning method: relative or absolute. Details.
top,
right,
bottom,
left
Define the offset relative to the initial position. Details.
backgroundColor
Color of the block background. Details.
opacity
Opacity (non-transparency) of the element with its content. Details.
flexBasis
Sets the initial block size along the container primary axis. Details.
flexGrow
Block coefficient of growth along the primary axis. Details.
flexShrink
Block coefficient of shrink along the primary axis. Details.
flex
Sets the block behaviour (grow, shrink and basis) along the primary container axis. Details.
alignSelf
Alignment of a specific block across the primary container axis. Details.
Examples
Horizontal scrolling across three blocks.
<horizontal-scroll showScrollBar={true}>
  <text style={{ padding: 20, backgroundColor: '#eaa' }}>The first element</text>
  <text style={{ padding: 20, backgroundColor: '#aea' }} onClick={showToast('Клик')}>The second element (clickable)</text>
  <box style={{ padding: 20, backgroundColor: '#aae' }}>
    <text>The third element</text>
  </box>
</horizontal-scroll>
The first element
The second element (clickable)
The third element
Horizontal scrolling over the blocks with 95% of the container width. Sticky scroll option enabled.
<horizontal-scroll snapToBlocks={true} class={'section'} style={{ paddingRight: 10 }}>
  <box style={{ width: '95%', paddingLeft: 10 }}>
    <image src={{ url: 'https://fs.chatium.io/get/image_Dbt4FzAV9H.728x454.jpeg', width: 728, height: 454 }} />
  </box>
  <box style={{ width: '95%', paddingLeft: 10 }}>
    <image src={{ url: 'https://fs.chatium.io/get/image_w1yQAIMAD6.728x454.jpeg', width: 728, height: 454 }} />
  </box>
  <box style={{ width: '95%', paddingLeft: 10 }}>
    <image src={{ url: 'https://fs.chatium.io/get/image_iigHuRFu5N.728x454.jpeg', width: 728, height: 454 }} />
  </box>
</horizontal-scroll>


horizontal-scroll-indicator
Progress or current slide indicator for horizontal-scroll.
Properties
indicator*: string
Appearance of the indicator: dots or text. Possible values:
 dots  — Dot indicator.
 text  — Text indicator. Displays sequential number of the current slide.
position: string
Indicator position within horizontal-scroll. Default values: bottom-center for indicator = "dots" and top-right for indicator = "text".
Possible values: top-left, top-center, top-right, bottom-left, bottom-center, bottom-right
Examples
Scroll with indicator dots.
<horizontal-scroll>
  <horizontal-scroll-indicator indicator='dots' />
  <text style={{ padding: 50, backgroundColor: '#eaa' }}>First element</text>
  <text style={{ padding: 50, backgroundColor: '#aea' }}>Second element</text>
  <text style={{ padding: 50, backgroundColor: '#aae' }}>Third element</text>
</horizontal-scroll>
First element
Second element
Third element
Scroll with text indicator. Position in the top left corner.
<horizontal-scroll>
  <horizontal-scroll-indicator indicator='text' position='top-left' />
  <text style={{ padding: 50, backgroundColor: '#eaa' }}>First element</text>
  <text style={{ padding: 50, backgroundColor: '#aea' }}>Second element</text>
  <text style={{ padding: 50, backgroundColor: '#aae' }}>Third element</text>
</horizontal-scroll>
First element
Second element

iap-product
Payment tag for in-app purchases
Properties



con
The icon component provides a large set of small drawings for a more intuitive GUI.
Properties
name*: string | Array<string>
Name of the icon. A large number of icons are available. Set of FontAwesome
Value examples:
 'user'
 ['fas','shopping-cart']
 ['system','android-back']
size: number | Size
Default: md
Icon size
Possible values: sm, md, lg, xl, 2xl, 3xl, number
style
Changing appearance, position and geometry of the block.
padding,
paddingHorizontal,
paddingVertical,
paddingTop,
paddingRight,
paddingBottom,
paddingLeft
Internal container indentation. Details.
margin,
marginHorizontal,
marginVertical,
marginTop,
marginRight,
marginBottom,
marginLeft
External indentation of the block. Details.
position
Defines the block positioning method: relative or absolute. Details.
top,
right,
bottom,
left
Define the offset relative to the initial position. Details.
border
Sets the border for the block on all four sides: width, style and color. Details.
borderTop,
borderRight,
borderBottom,
borderLeft
Setting individual borders for the block edges: width and color. Details.
borderRadius,
borderBottomLeftRadius,
borderBottomRightRadius,
borderTopLeftRadius,
borderTopRightRadius
Rounding of the border corners. Details.
borderWidth,
borderTopWidth,
borderRightWidth,
borderBottomWidth,
borderLeftWidth
Width of the border. Details.
borderColor,
borderTopColor,
borderRightColor,
borderBottomColor,
borderLeftColor
Color of the border. Details.
backgroundColor
Color of the block background. Details.
opacity
Opacity (non-transparency) of the element with its content. Details.
elevation
Block shadow height. Details.
shadowColor
Block shadow color. Details.
alignSelf
Alignment of a specific block across the primary container axis. Details.
transform
Transformation of block and container geometry in 2D and 3D space. Details.
Examples
<icon size='lg' name={['fas','shopping-cart']}/>
<icon size='lg' name={['user']}/>
<icon
  name={['fas', 'heartbeat']}
  size="lg"
  style={{
    color: 'green',
    backgroundColor: 'orange',
    padding: 5,
    borderWidth: 2,
    borderRadius: 500,
    active: { backgroundColor: 'red', opacity: 0.5 },
  }}/>

image
Image.
Size
The image is displayed in its own dimensions, provided there is enough width space in the containing block. It is otherwise limited to the width of the containing block. Image aspect ratio is preserved unless specified otherwise.
The size is specified in logical pixels instead of physical device pixels. When the maximum displayed quality is required on the devices with high pixel density, it is recomended to have the size of the original image three times the resolution of the displayed one.
Properties
src*: string | { url, width?, height?}
Parameters of the original image. String with source URL or object with fields:
url*: string
A string with URL of the source.
width*: number
Source width. Specified along with height. When both width and height are specified, a possible page jump on first load is prevented when the actual size of the image is not known otherwise. The specified size of the source is the size that is taken into account upon rendering and may differ from the real one.
height*: number
Source height. See width above.
resizeMode: string
Default: cover
How to fit the image in case the target area proportions are different from the source.
 cover  — The image is fitted by covering the entire area. Parts of the image that do not fit into the area become invisible. The aspect ratio is preserved.
 contain  — The image is fited entirety, with space added on the sides with insufficient data. The aspect ratio is preserved.
 stretch  — The image is stretched along the area borders. The aspect ratio is ignored.
style
Changing appearance, position and geometry of the block.
width,
maxWidth,
minWidth
Block width. Details.
height,
maxHeight,
minHeight
Block height. Details.
margin,
marginHorizontal,
marginVertical,
marginTop,
marginRight,
marginBottom,
marginLeft
External indentation of the block. Details.
position
Defines the block positioning method: relative or absolute. Details.
top,
right,
bottom,
left
Define the offset relative to the initial position. Details.
border
Sets the border for the block on all four sides: width, style and color. Details.
borderTop,
borderRight,
borderBottom,
borderLeft
Setting individual borders for the block edges: width and color. Details.
borderRadius,
borderBottomLeftRadius,
borderBottomRightRadius,
borderTopLeftRadius,
borderTopRightRadius
Rounding of the border corners. Details.
borderWidth,
borderTopWidth,
borderRightWidth,
borderBottomWidth,
borderLeftWidth
Width of the border. Details.
borderColor,
borderTopColor,
borderRightColor,
borderBottomColor,
borderLeftColor
Color of the border. Details.
backgroundColor
Color of the block background. Details.
backgroundGradient
Gradient of the block background. Details.
opacity
Opacity (non-transparency) of the element with its content. Details.
elevation
Block shadow height. Details.
shadowColor
Block shadow color. Details.
flexBasis
Sets the initial block size along the container primary axis. Details.
flexGrow
Block coefficient of growth along the primary axis. Details.
flexShrink
Block coefficient of shrink along the primary axis. Details.
flex
Sets the block behaviour (grow, shrink and basis) along the primary container axis. Details.
alignSelf
Alignment of a specific block across the primary container axis. Details.
transform
Transformation of block and container geometry in 2D and 3D space. Details.
Examples
300 pixels wide image, smaller than the 350 pixels wide containing block.
<box style={{ width: 350, border: [ 2, 'solid' ] }}>
  <image src='https://fs.chatium.io/thumbnail/4w5NMYcBKL/s/300x200'/>
</box>

450 pixels wide image, larger than the 350 pixels wide containing block. Limited to the width of the containing block. Image aspect ratio is preserved.
<box style={{ width: 350, border: [ 2, 'solid' ] }}>
  <image src='https://fs.chatium.io/thumbnail/4w5NMYcBKL/s/450x300'/>
</box>

Image with specified source dimensions to prevent the page jump on the first load. See src.with in the properties description above.
<box style={{ width: 350, border: [ 2, 'solid' ] }}>
  <image src={{
      url: 'https://fs.chatium.io/thumbnail/4w5NMYcBKL/s/300x200',
      width: 600,
      height: 400
  }} />
</box>

Image with a relative output width. The output height is calculated retaining the aspect ratio of the source specified dimensions. If the source dimensions are not specified, the proportions are calculated based on the actual size of the image. In this case, a page jump is possible on the first load: see src.with in the properties description above.
<box style={{ width: 350, border: [ 2, 'solid' ] }}>
  <image
    src={{
      url: 'https://fs.chatium.io/thumbnail/4w5NMYcBKL/s/300x200',
      width: 600,
      height: 400
    }}
    style={{
      width: '66%'
    }}
  />
</box>

Landscape image in portrait size. Scaling method cover (default). The image is fitted by covering the entire area. Parts of the image that do not fit into the area become invisible. The aspect ratio is preserved.
<image
  src={{
    url: 'https://fs.chatium.io/thumbnail/4w5NMYcBKL/s/300x200',
    width: 300,
    height: 200
  }}
  style={{
    width: '200',
    height: '250',
    border: [ 2, 'solid' ]
  }}
  resizeMode='cover'
/>

Landscape image in portrait size. Scaling method contain. The image is fited entirety, with space added on the sides with insufficient data. The aspect ratio is preserved.
<image
  src={{
    url: 'https://fs.chatium.io/thumbnail/4w5NMYcBKL/s/300x200',
    width: 300,
    height: 200
  }}
  style={{
    width: '200',
    height: '250',
    border: [ 2, 'solid' ]
  }}
  resizeMode='contain'
/>

Landscape image in portrait size. Scaling method stretch. The image is stretched along the area borders. The aspect ratio is ignored.
<image
  src={{
    url: 'https://fs.chatium.io/thumbnail/4w5NMYcBKL/s/300x200',
    width: 300,
    height: 200
  }}
  style={{
    width: '200',
    height: '250',
    border: [ 2, 'solid' ]
  }}
  resizeMode='stretch'
/>

list-item
List item
Properties
icon: <smart-icon>
Component <smart-icon> displayed along the left edge of this <list-item>.
contents: { title, subTitle, badge }
title: string
Title. Displayed in bold.
subTitle: string
Subtitle. Displayed in gray and smaller font.
badge: string | { badge, style }
Text-icon on the right hand side of the subtitle.
badge: string
Text for the icon.
style: object
Icon style. Defaults to white text on red background.
style
Changing appearance, position and geometry of the block.
width,
maxWidth,
minWidth
Block width. Details.
height,
maxHeight,
minHeight
Block height. Details.
padding,
paddingHorizontal,
paddingVertical,
paddingTop,
paddingRight,
paddingBottom,
paddingLeft
Internal container indentation. Details.
margin,
marginHorizontal,
marginVertical,
marginTop,
marginRight,
marginBottom,
marginLeft
External indentation of the block. Details.
position
Defines the block positioning method: relative or absolute. Details.
top,
right,
bottom,
left
Define the offset relative to the initial position. Details.
color
Color of the text. Details.
fontFamily
Name of the font family. Details.
fontSize
Size of the font. Details.
fontStyle
An option to make the font cursive. Details.
fontWeight
Weight of the font (boldness). Details.
fontVariant
An option to make all the digits same width Details.
letterSpacing
Spacing between the letters. Details.
lineClamp
Limiting the height of the text by the number of lines. Details.
lineHeight
Sets the amount of space allocated for a single line of text. Details.
textAlign
Sets the text alignment. Details.
textDecorationLine
Underline or line-through decoration for the text. Details.
textOverflow
Method for text truncation when breaching the container boundaries. Details.
textShadowColor
Color of the text shadow. Details.
textShadowOffset
Shadow offset, horizontally or vertically. Details.
textShadowRadius
Shadow blur radius in pixels. Details.
textTransform
Transformation of text upon display. Details.
border
Sets the border for the block on all four sides: width, style and color. Details.
borderTop,
borderRight,
borderBottom,
borderLeft
Setting individual borders for the block edges: width and color. Details.
borderRadius,
borderBottomLeftRadius,
borderBottomRightRadius,
borderTopLeftRadius,
borderTopRightRadius
Rounding of the border corners. Details.
borderWidth,
borderTopWidth,
borderRightWidth,
borderBottomWidth,
borderLeftWidth
Width of the border. Details.
borderColor,
borderTopColor,
borderRightColor,
borderBottomColor,
borderLeftColor
Color of the border. Details.
backgroundColor
Color of the block background. Details.
backgroundGradient
Gradient of the block background. Details.
opacity
Opacity (non-transparency) of the element with its content. Details.
elevation
Block shadow height. Details.
shadowColor
Block shadow color. Details.
flexDirection
Defines the container primary axis (blocks direction in the container). Details.
flexBasis
Sets the initial block size along the container primary axis. Details.
flexGrow
Block coefficient of growth along the primary axis. Details.
flexShrink
Block coefficient of shrink along the primary axis. Details.
flex
Sets the block behaviour (grow, shrink and basis) along the primary container axis. Details.
alignItems
Alignment of the blocks across the primary container axis. Details.
alignSelf
Alignment of a specific block across the primary container axis. Details.
transform
Transformation of block and container geometry in 2D and 3D space. Details.
Examples
<list-item
  icon={{ name: ['fas', 'laptop'] }}
  content={{
    title: 'Basic example',
    subTitle: 'Subtitle',
  }}
/>
Basic example
Subtitle
<list-item
  icon={{ name: ['fas', 'laptop'] }}
  content={{
    title: <button class="primary" onClick={showToast('Hi!')}>Button</button>,
  }}
/>

Button
<list-item
  icon={{
    name: ['fas', 'laptop'],
    class: 'circle',
    size: 'lg',
    style: { borderWidth: 2 }
  }}
  content={{
    title: <text>A bit of styling for <Link to="/en/reference/app-ui/blocks/smart-icon"><smart-icon></Link></text>
  }}
/>
A bit of styling for <smart-icon>
<list-item
  icon={{ name: ['fas', 'laptop'] }}
  content={{
    title: 'Default badge style',
    badge: '99+'
  }}
/>
Default badge style
99+
<list-item
  icon={{ name: ['fas', 'laptop']}}
  content={{
    title: 'badge with styling',
    subTitle: 'Displays here',
    badge: {
      badge: '!',
      style: {
        backgroundColor: 'transparent',
        border: [1, 'solid', 'green'],
        color: 'green'
      }
    }
  }}
/>
badge

parallax
Container for blocks with a parallax background scrolling effect.
Properties
background: <image>
Background <image> for the container.
contentStyle
Style applied to the container contents.
padding,
paddingHorizontal,
paddingRight,
paddingLeft
Internal container indentation. Details.
opacity
Opacity (non-transparency) of the element with its content. Details.
flexDirection
Defines the container primary axis (blocks direction in the container). Details.
justifyContent
Alignment and distribution of the space along the primary container axis. Details.
alignItems
Alignment of the blocks across the primary container axis. Details.
alignContent
Distribution of space across the container primary axis. Details.
flexWrap
Wrapping of the blocks on the new line along the primary container axis. Details.
style
Style applied to the container itself. Only external margins are adjustable.
margin,
marginHorizontal,
marginVertical,
marginTop,
marginRight,
marginBottom,
marginLeft
External indentation of the block. Details.
Examples
app.screen('', (ctx, req) => {
  const textStyle = {
    textAlign: 'center',
    alignSelf: 'center',
    maxWidth: 400,
    fontWeight: 'bold'
  }

  const background = <image
    src={{
      url: 'https://fs.chatium.io/thumbnail/4w5NMYcBKL/s/600x400',
      width: 600,
      height: 400
    }}
    style={{
      width: '100%'
    }}
  />

  return <screen layout="column">
    <box style={{ height: 300, justifyContent: 'center', backgroundColor: '#eef' }}>
      <text style={textStyle}>
        Scroll the page until the end to see the parallax effect
      </text>
    </box>

    <parallax
      contentStyle={{ justifyContent: 'center' }}
      background={background}
    >
      <text style={textStyle}>
        Note how the background scrolls with a parallax effect while text scrolls normally
      </text>
    </parallax>

    <box style={{ height: 900, justifyContent: 'center', backgroundColor: '#eef' }} />
  </screen>
})

smart-icon
A Smart Icon can be set as a text icon, an icon from the Font Awesome set, a control icon, a spinner, or any image.
Properties
text: string
Icon content text.
name: string | Array<string>
Icon name from the Font Awesome set. A large number of icons are available.
Takes precedence over the text setting — its value is ignored if set.
Value examples:
 'user'
 ['fas','shopping-cart']
 ['system','android-back']
url: string
Address of the icon image. Takes precedence over the text and name parameters — their values are ignored if set.
size: number | Size
Default: "md"
Icon size. Can be an arbitrary number or one from the given set.
Possible values: "sm", "md", "lg", "xl", "2xl", "3xl", number
class: string
Default: "square"
Icon shape: square or circle. Default is square.
Possible values: "square", "circle"
badges: Array<T>
Additional status icons. Where T can be Badge or <smart-icon> as the status.
Badge: { badge, position, style }
badge: string
Status text.
position: Position
Location of the status relative to the main icon. Four preset locations are available or a custom position specified through the style properties left, right, top and bottom.
The same property can be used to position a <smart-icon> status icon.
Possible values: top-left, top-right, botton-left, bottom-right, custom
style: Style
Status style.
style
Changing appearance, position and geometry of the block.
padding,
paddingHorizontal,
paddingVertical,
paddingTop,
paddingRight,
paddingBottom,
paddingLeft
Internal container indentation. Details.
margin,
marginHorizontal,
marginVertical,
marginTop,
marginRight,
marginBottom,
marginLeft
External indentation of the block. Details.
position
Defines the block positioning method: relative or absolute. Details.
top,
right,
bottom,
left
Define the offset relative to the initial position. Details.
border
Sets the border for the block on all four sides: width, style and color. Details.
borderTop,
borderRight,
borderBottom,
borderLeft
Setting individual borders for the block edges: width and color. Details.
borderRadius,
borderBottomLeftRadius,
borderBottomRightRadius,
borderTopLeftRadius,
borderTopRightRadius
Rounding of the border corners. Details.
borderWidth,
borderTopWidth,
borderRightWidth,
borderBottomWidth,
borderLeftWidth
Width of the border. Details.
borderColor,
borderTopColor,
borderRightColor,
borderBottomColor,
borderLeftColor
Color of the border. Details.
backgroundColor
Color of the block background. Details.
opacity
Opacity (non-transparency) of the element with its content. Details.
elevation
Block shadow height. Details.
shadowColor
Block shadow color. Details.
alignSelf
Alignment of a specific block across the primary container axis. Details.
transform
Transformation of block and container geometry in 2D and 3D space. Details.
Examples
Example of icons from Font Awesome set. Many more icons available.
<box style={{ flexDirection: 'row', alignSelf: 'center' }} >
  <smart-icon name="user" />
  <smart-icon name={['fas','shopping-cart']} />
  <smart-icon name={['fas','globe-americas']} />
  <smart-icon name={['fas','map-marker-alt']} />
  <smart-icon name={['fas','at']} />
  <smart-icon name={['fas','phone']} />
</box>
Available spinners
<box style={{ flexDirection: 'row', alignSelf: 'center' }} >
  <smart-icon name={['spinner', 'plane']} />
  <smart-icon name={['spinner', 'chase']} />
  <smart-icon name={['spinner', 'bounce']} />
  <smart-icon name={['spinner', 'wave']} />
  <smart-icon name={['spinner', 'pulse']} />
  <smart-icon name={['spinner', 'flow']} />
</box>
<box style={{ flexDirection: 'row', alignSelf: 'center' }} >
  <smart-icon name={['spinner', 'swing']} />
  <smart-icon name={['spinner', 'circle']} />
  <smart-icon name={['spinner', 'circle-fade']} />
  <smart-icon name={['spinner', 'grid']} />
  <smart-icon name={['spinner', 'fold']} />
  <smart-icon name={['spinner', 'wander']} />
</box>
Control buttons
<box style={{ flexDirection: 'row', alignSelf: 'center' }} >
  <smart-icon name={['system', 'android-back']} />
  <smart-icon name={['system', 'dots']} />
  <smart-icon name={['system', 'ios-back']} />
  <smart-icon name={['system', 'pause']} />
</box>
<box style={{ flexDirection: 'row', alignSelf: 'center' }} >
  <smart-icon name={['system', 'play-arrow']} />
  <smart-icon name={['system', 'replay']} />
  <smart-icon name={['system', 'fast-forward-10']} />
  <smart-icon name={['system', 'rewind-10']} />
</box>
Icon sizes. Arbitrary and predefined.
<horizontal-scroll style={{ alignSelf: 'center' }}>
  <box style={{ flexDirection: 'row' }} >
    <smart-icon name="user" size={10} />
    <smart-icon name="user" size={15} />
    <smart-icon name="user" size="sm" />
    <smart-icon name="user" size="md" />
    <smart-icon name="user" size="lg" />
    <smart-icon name="user" size="xl" />
    <smart-icon name="user" size="2xl" />
  </box>
</horizontal-scroll>
Round and square icons.
<box style={{ flexDirection: 'row', alignSelf: 'center' }} >
  <smart-icon
    name="user"
    class="circle"
    size="lg"
    style={{
      border: [2, 'solid'],
      marginHorizontal: 5
    }}
  />
  <smart-icon
    name="user"
    class="square"
    size="lg"
    style={{
      border: [2, 'solid'],
      marginHorizontal: 5
    }}
  />
</box>
Examples as text, icon and image.
<box style={{ flexDirection: 'row', alignSelf: 'center' }} >
  <smart-icon
    text='123'
    class="circle"
    size="lg"
    style={{
      border: [1, 'solid'],
      marginHorizontal: 5
    }}
  />
  <smart-icon
    name="user"
    class="circle"
    size="lg"
    style={{
      border: [1, 'solid'],
      marginHorizontal: 5
    }}
  />
  <smart-icon
    url='https://fs.chatium.io/thumbnail/4w5NMYcBKL/s/100x100'
    class="circle"
    size="lg"
    style={{
      border: [1, 'solid'],
      marginHorizontal: 5
    }}
  />
</box>
123
Options for the color, background and frame of the icon. The color of the icon is used for the border, unless a custom one is specified.
<box style={{ flexDirection: 'row', alignSelf: 'center' }} >
  <smart-icon
    name="user"
    class="circle"
    style={{
      border: [1, 'solid'],
      marginHorizontal: 5
    }}
  />
  <smart-icon
    name="user"
    class="circle"
    style={{
      color: 'red',
      border: [1, 'solid'],
      marginHorizontal: 5
    }}
  />
  <smart-icon
    name="user"
    class="circle"
    style={{
      backgroundColor: 'red',
      marginHorizontal: 5
      }}
    />
  <smart-icon
    name="user"
    class="circle"
    style={{
      color: 'white',
      border: [3, 'solid', 'blue'],
      backgroundColor: 'red',
      marginHorizontal: 5
      }}
    />
  <smart-icon
    name="user"
    class="circle"
    style={{
      backgroundGradient: [-45, 'white', 'rgb(255,100,30)'],
      marginHorizontal: 5
    }} />
  <smart-icon
    name="user"
    class="circle"
    style={{
      border: [1, 'solid'],
      backgroundGradient: [-45, 'white', 'rgb(255,100,30)'],
      marginHorizontal: 5
    }} />
</box>
Text status icons. The status can breach the boundaries of the icon. In such case it is important for the element containing the icon to have enough space. Otherwise the status gets cropped.
<box style={{ flexDirection: 'row', alignSelf: 'center' }} >
  <box style={{ margin: 5 }}>
    <smart-icon
      name="user"
      class="circle"
      size="xl"
      badges={[{ badge: "123" }]}
      style={{border: [1, 'solid']}}
    />
  </box>
  <box style={{ margin: 5 }}>
    <smart-icon
      name="user"
      class="square"
      size="xl"
      badges={[{ badge: "123" }]}
      style={{border: [1, 'solid']}}
    />
  </box>
  <box style={{ padding: 5 }}>
    <smart-icon
      name="user"
      class="square"
      size="xl"
      badges={[{ badge: "123" }]}
      style={{border: [1, 'solid']}}
    />
  </box>
</box>
123
123
123
Size of the status is adjusted according to the icon size.
<box style={{ flexDirection: 'row', alignSelf: 'center' }} >
  <smart-icon
    name="user"
    class="circle"
    size="sm"
    badges={[{ badge: "123" }]}
    style={{
      border: [1, 'solid'],
      marginHorizontal: 5
    }}
  />
  <smart-icon
    name="user"
    class="circle"
    size="md"
    badges={[{ badge: "123" }]}
    style={{
      border: [1, 'solid'],
      marginHorizontal: 5
    }}
  />
  <smart-icon
    name="user"
    class="circle"
    size="xl"
    badges={[{ badge: "123" }]}
    style={{
      border: [1, 'solid'],
      marginHorizontal: 5
    }}
  />
</box>
123
123
123
Position of the status.
<box style={{ flexDirection: 'row', alignSelf: 'center' }} >
  <smart-icon
    name="user"
    class="circle"
    size="xl"
    badges={[{
      badge: "123",
      position: 'top-right'
    }]}
    style={{
      border: [1, 'solid'],
      marginHorizontal: 5
    }}
  />
  <smart-icon
    name="user"
    class="circle"
    size="xl"
    badges={[{ badge: "123" }]}
    style={{
      border: [1, 'solid'],
      marginHorizontal: 5
    }}
  />
  <smart-icon
    name="user"
    class="circle"
    size="xl"
    badges={[{
      badge: "123",
      position: 'custom',
      style: {
        left: -5
      }
    }]}
    style={{
      border: [1, 'solid'],
      marginHorizontal: 5
    }}
  />
</box>
123
123
123
Text status styling. Note the difference in the transparency of the two statuses. For one status, only the background is transparent, while for the other, everything is transparent, including the text.
<box style={{ flexDirection: 'row', alignSelf: 'center' }} >
  <smart-icon
    name="user"
    class="circle"
    size="xl"
    badges={[{
      badge: "123",
      style: {
        backgroundColor: 'rgba(255, 0, 0, 0.5)'
      }
    }]}
    style={{
      border: [1, 'solid'],
      marginHorizontal: 5
    }}
  />
  <smart-icon
    name="user"
    class="circle"
    size="xl"
    badges={[{
      badge: "123",
      style: {
        backgroundColor: 'green',
        opacity: '0.5'
      }
    }]}
    style={{
      border: [1, 'solid'],
      marginHorizontal: 5
    }}
  />
  <smart-icon
    name="user"
    class="circle"
    size="xl"
    badges={[{
      badge: "123",
      style: {
        color: 'red',
        border: [1, 'solid', 'black'],
        backgroundColor: 'white'
      }
    }]}
    style={{
      border: [1, 'solid'],
      marginHorizontal: 5
    }}
  />
</box>
123
123
123
<smart-icon> as a status.
<box style={{ flexDirection: 'row', alignSelf: 'center' }} >
  <box style={{ padding: 5 }}>
    <smart-icon
      name="user"
      class="circle"
      size="xl"
      badges={[
        <smart-icon
          text="123"
          class="circle"
          style={{
            border: [1, 'solid'],
            backgroundColor: '#def'
          }}
        />
      ]}
      style={{border: [1, 'solid']}}
    />
  </box>
  <box style={{ padding: 5 }}>
    <smart-icon
      name="user"
      class="circle"
      size="xl"
      badges={[
        <smart-icon
          name={['fas','phone']}
          class='circle'
          style={{
            color: '#fff',
            backgroundColor: '#999902',
          }}
        />
      ]}
      style={{border: [1, 'solid']}}
    />
  </box>
  <box style={{ padding: 5 }}>
    <smart-icon
      name="user"
      class="circle"
      size="xl"
      badges={[
        <smart-icon
          url='https://fs.chatium.io/thumbnail/4w5NMYcBKL/s/100x100'
          class="circle"
        />
      ]}
      style={{border: [1, 'solid']}}
    />
  </box>
</box>
123
All four types of statuses on one icon.
<box style={{ flexDirection: 'row', alignSelf: 'center' }} >
  <box style={{ padding: 5 }}>
    <smart-icon
      name="user"
      class="circle"
      size="xl"
      badges={[
        <smart-icon
          position='top-right'
          name={['fas','phone']}
          class='circle'
          style={{
            color: '#fff',
            backgroundColor: '#999902',
          }}
        />,
        <smart-icon
          url='https://fs.chatium.io/thumbnail/4w5NMYcBKL/s/100x100'
          class="circle"
        />,
        <smart-icon
          text="123"
          class="circle"
          position='custom'
          style={{
            left: -5,
            border: [1, 'solid'],
            backgroundColor: '#def'
          }}
        />,
        {
          badge: 'text',
          position: 'bottom-left'
        }
      ]}
      style={{border: [1, 'solid']}}
    />
  </box>
</box>


search-input
Search query input field.
Properties
The properties are identical to <text-input>. The exception is the returnKeyType parameter fixed to the search value.
Examples
Search organization example. The results are shown on input.

Form code:
<search-input
  name="fruitSearchInput"
  formId="formSearch"
  placeholder="Fruit search"
  onChange={{
    url: ctx.router.url('fruitSearchApi')
  }}
  initialValue={req.query.fruitSearchQuery}
/>

<FruitList searchQuery={req.query.fruitSearchQuery} style={{ paddingTop: 10 }} />
Search submit API code:
app.apiCall('fruitSearchApi', (ctx, req) => {
  return ctx.router.navigate(
    `/?fruitSearchQuery=${encodeURIComponent(req.body.fruitSearchInput || '')}`,
    { replace: true }
  )
})
FruitList code. Performs the actual search and displays the result:
const fruitList = [
  "Apricot", "Avocado", "Pineapple", "Annona", "Guanabana", "Orange",
  "Banana", "Bergamot", "Pomegranate", "Grapefruit", "Pear", "Guava", "Jackfruit",
  "Pitahaya", "Durian", "Carambola", "Kiwi", "Clementine", "Kumquat", "Lime",
  "Lemon", "Lychee", "Longan", "Lam Yai", "Mango", "Mangosteen", "Mandarin",
  "Passionfruit", "Medlar", "Nectarine", "Papaya", "Peach", "Pomelo", "Orange",
  "Rambutan", "Salak", "Sapodilla", "Oroblanco", "Plum", "Tangelo", "Henomeles",
  "Persimmon", "Citron", "Cherimoya", "Chompu", "Apple"
]

function FruitList({searchQuery, style}) {
  const query = (searchQuery || '').toLowerCase().trim()

  if (0 === query.length) {
    return <text style={style}>Enter a fruit search query.</text>
  }

  const match = fruitList.filter(f => f.toLowerCase().includes(query))

  if (0 === match.length) {
    return <text style={style}>No fruits matching "{(searchQuery || '').trim()}" query found.</text>
  }

  return <text style={style}>{match.join(', ')}</text>
}

sticky
Blocks container "sticking" to the top screen edge. The element behaves normally as long as the scroll positions it below the top edge of the screen. When a regular container gets even partially hidden behind the top screen edge, sticky "breaks away" from its position on the page and is positions itself along the top edge of the screen.
Properties
style
Changing appearance, position and geometry of the block.
width,
maxWidth,
minWidth
Block width. Details.
height,
maxHeight,
minHeight
Block height. Details.
padding,
paddingHorizontal,
paddingVertical,
paddingTop,
paddingRight,
paddingBottom,
paddingLeft
Internal container indentation. Details.
margin,
marginHorizontal,
marginVertical,
marginTop,
marginRight,
marginBottom,
marginLeft
External indentation of the block. Details.
border
Sets the border for the block on all four sides: width, style and color. Details.
borderTop,
borderRight,
borderBottom,
borderLeft
Setting individual borders for the block edges: width and color. Details.
borderRadius,
borderBottomLeftRadius,
borderBottomRightRadius,
borderTopLeftRadius,
borderTopRightRadius
Rounding of the border corners. Details.
borderWidth,
borderTopWidth,
borderRightWidth,
borderBottomWidth,
borderLeftWidth
Width of the border. Details.
borderColor,
borderTopColor,
borderRightColor,
borderBottomColor,
borderLeftColor
Color of the border. Details.
backgroundColor
Color of the block background. Details.
backgroundGradient
Gradient of the block background. Details.
opacity
Opacity (non-transparency) of the element with its content. Details.
elevation
Block shadow height. Details.
shadowColor
Block shadow color. Details.
flexDirection
Defines the container primary axis (blocks direction in the container). Details.
flexBasis
Sets the initial block size along the container primary axis. Details.
justifyContent
Alignment and distribution of the space along the primary container axis. Details.
alignItems
Alignment of the blocks across the primary container axis. Details.
alignContent
Distribution of space across the container primary axis. Details.
alignSelf
Alignment of a specific block across the primary container axis. Details.
flexWrap
Wrapping of the blocks on the new line along the primary container axis. Details.
Examples
app.screen('sticky', (ctx, req) => {
  return <screen>
    <box style={{ height: 300 }} >
      <text style={{ alignSelf: 'center', paddingTop: 100 }}>
        Scroll the screen untill the end
      </text>
    </box>

    <sticky>
      <text style={{ alignSelf: 'center' }}>
        Sticky block!
      </text>
    </sticky>

    <box style={{ height: 300 }} />

    <sticky>
      <text style={{ alignSelf: 'center' }}>
        Another sticky block, displacing the previous
      </text>
    </sticky>

    <box style={{ height: 300 }} />

    <sticky />

    <box style={{ height: 1000 }} >
      <text style={{ alignSelf: 'center' }}>
        An empty sticky block, to get rid of sticky at the top of the screen
      </text>
    </box>

    <box>
      <text style={{ alignSelf: 'center', paddingBottom: 100 }}>
        The end
      </text>
    </box>
  </screen>
})

Open the example

text
Text block.
Properties
text: string | number
An option to pass the data as a parameter instead of children elements.
style
Changing appearance, position and geometry of the block.
width,
maxWidth,
minWidth
Block width. Details.
height,
maxHeight,
minHeight
Block height. Details.
padding,
paddingHorizontal,
paddingVertical,
paddingTop,
paddingRight,
paddingBottom,
paddingLeft
Internal container indentation. Details.
margin,
marginHorizontal,
marginVertical,
marginTop,
marginRight,
marginBottom,
marginLeft
External indentation of the block. Details.
position
Defines the block positioning method: relative or absolute. Details.
top,
right,
bottom,
left
Define the offset relative to the initial position. Details.
color
Color of the text. Details.
fontFamily
Name of the font family. Details.
fontSize
Size of the font. Details.
fontStyle
An option to make the font cursive. Details.
fontWeight
Weight of the font (boldness). Details.
fontVariant
An option to make all the digits same width Details.
letterSpacing
Spacing between the letters. Details.
lineClamp
Limiting the height of the text by the number of lines. Details.
lineHeight
Sets the amount of space allocated for a single line of text. Details.
textAlign
Sets the text alignment. Details.
textAlignVertical
Vertical alignment of text in a container. Details.
textDecorationLine
Underline or line-through decoration for the text. Details.
textOverflow
Method for text truncation when breaching the container boundaries. Details.
textShadowColor
Color of the text shadow. Details.
textShadowOffset
Shadow offset, horizontally or vertically. Details.
textShadowRadius
Shadow blur radius in pixels. Details.
textTransform
Transformation of text upon display. Details.
backgroundColor
Color of the block background. Details.
opacity
Opacity (non-transparency) of the element with its content. Details.
elevation
Block shadow height. Details.
shadowColor
Block shadow color. Details.
flexBasis
Sets the initial block size along the container primary axis. Details.
flexGrow
Block coefficient of growth along the primary axis. Details.
flexShrink
Block coefficient of shrink along the primary axis. Details.
flex
Sets the block behaviour (grow, shrink and basis) along the primary container axis. Details.
alignSelf
Alignment of a specific block across the primary container axis. Details.
transform
Transformation of block and container geometry in 2D and 3D space. Details.
Examples
<text>Minimal example</text>
Minimal example
<text text="Minimal example as a parameter"></text>
Minimal example as a parameter
<text> blocks following one another don't concatenate into a line.
<text>First</text><text>Second</text>
First
Second
A <text> block can act as a container for other <text> blocks. Then all child blocks following one another will concatenate into a line. Blocks concatenate without spaces. Spaces have to be specified explicitly.
<text>
  <text>First</text>
  <text>Second</text>
  {'   '}
  <text>Third</text>
</text>
FirstSecond   Third
Text styling.
<text>
  Plain
</text>
<text style={{ fontWeight: 'bold' }}>
  Bold
</text>
<text style={{ fontStyle: 'italic' }}>
  Cursive
</text>
<text style={{ color: 'red' }}>
  Red
</text>
<text style={{ textDecorationLine: 'underline' }}>
  Underline
</text>
Plain
Bold
Cursive
Red
Underline
Nested <text> block inherits text styling from the parent.
<text>
  Plain
  <text style={{ fontWeight: 'bold' }}>
    Bold
    <text style={{ fontStyle: 'italic' }}>
      Cursive
      <text style={{ color: 'red' }}>
        Red
        <text style={{ textDecorationLine: 'underline' }}>
          Underline
        </text>
      </text>
    </text>
  </text>
</text>
PlainBoldCursiveRedUnderline
Text size. Arbitrary and predefined.
<text style={{ fontSize: 50 }}>50</text>
<text style={{ fontSize: 40 }}>40</text>
<text style={{ fontSize: '3xl' }}>3xl</text>
<text style={{ fontSize: '2xl' }}>2xl</text>
<text style={{ fontSize: 'xl' }}>xl</text>
<text style={{ fontSize: 'lg' }}>lg</text>
<text style={{ fontSize: 'md' }}>md</text>
<text style={{ fontSize: 'sm' }}>sm</text>
<text style={{ fontSize: 'xs' }}>xs</text>
50
40
3xl
2xl
xl
lg
md
sm
xs
Text color
<text style={{ color: 'red' }}>red</text>
<text style={{ color: '#0d0' }}>#0d0</text>
<text style={{ color: 'rgb(12,137,221)' }}>rgb(12,137,221)</text>
<text style={{ color: 'rgba(12,137,221, 0.6)' }}>rgba(12,137,221, 0.6)</text>
red
#0d0
rgb(12,137,221)
rgba(12,137,221, 0.6)
Text background color
<text style={{ backgroundColor: 'red' }}>red</text>
<text style={{ backgroundColor: '#0d0' }}>#0d0</text>
<text style={{ backgroundColor: 'rgb(12,137,221)' }}>rgb(12,137,221)</text>
<text style={{ backgroundColor: 'rgba(12,137,221, 0.6)' }}>rgba(12,137,221, 0.6)</text>
red
#0d0
rgb(12,137,221)
rgba(12,137,221, 0.6)
Class link for link styling convenience.
<text class={'link'} onClick={ctx.account.url('/en')}>
  Link example
</text>
Link example
Links can be embedded in text.
<text>
  Example of{' '}
  <text class={'link'} onClick={ctx.account.url('/en')}>
    a link
  </text>
  {' '}in text.
</text>
Example of a link in text.
A class section can be used to add standard padding on the sides.
<text class='section' style={{ backgroundColor: '#ddd' }}>
  Text with class "section" adding standard indents.
</text>
Text with class "section" adding standard indents.
<text style={{ backgroundColor: '#ddd', padding: 30 }}>
  Text with an arbitrary padding on all four sides.
</text>
Text with an arbitrary padding on all four sides.
<text class={['link', 'section']} style={{ backgroundColor: '#ddd'}} onClick={ctx.account.url('/en')}>
  An example combination of two classes, "link" and "section".
</text>


text-input
Text input field. Used to create forms.
Properties
name*: string
Field name. Passed to the API upon the form submit.
formId*: string
Form identifier. Fields with the same form ID are submitted to the API together.
placeholder: string
A text to display when the field is empty. Serves as a hint to the user.
initialValue: string
Initial value in the field once the screen is displayed.
inputmode: InputMode
Default: text
Type of the input value. Changes of the virtual keyboard appearance on mobile devices and affects the associations of browser input hints. The tel value also changes the field appearance for the phone number entering convenience.
Possible values: text, decimal, tel, email, url, password
returnKeyType: KeyType
Default: done
Enter button view on the virtual keyboard. Only in the mobile app.
Possible values: done, go, next, search, send
disabled: bool
Default: false
Makes the fied inactive and the value immutable.
multiline: bool
Default: false
Multiline field.
autoFocus: bool
Default: false
Puts the field in focus when the screen is displayed.
autoCorrect: bool
Default: true
Text autocorrect on input. Only in the mobile app.
autocapitalize: Capitalize
Default: sentences
Capitalization options of the virtual keyboard in mobile browsers: disabled; per sentenses; per word; per character.
Possible values: none, sentences, words, characters
maxLength: number
Restriction on the maximum length of the entered text in characters.
onChange: Action
Action called upon text entry.
onReturnKeyPress: Action
Default: onChange
Action called upon the Enter press. By default, the action set in onChange is called.
style
Changing appearance, position and geometry of the block.
width,
maxWidth,
minWidth
Block width. Details.
height,
maxHeight,
minHeight
Block height. Details.
padding,
paddingHorizontal,
paddingVertical,
paddingTop,
paddingRight,
paddingBottom,
paddingLeft
Internal container indentation. Details.
margin,
marginHorizontal,
marginVertical,
marginTop,
marginRight,
marginBottom,
marginLeft
External indentation of the block. Details.
position
Defines the block positioning method: relative or absolute. Details.
top,
right,
bottom,
left
Define the offset relative to the initial position. Details.
color
Color of the text. Details.
fontFamily
Name of the font family. Details.
fontSize
Size of the font. Details.
fontStyle
An option to make the font cursive. Details.
fontWeight
Weight of the font (boldness). Details.
fontVariant
An option to make all the digits same width Details.
letterSpacing
Spacing between the letters. Details.
lineHeight
Sets the amount of space allocated for a single line of text. Details.
textAlign
Sets the text alignment. Details.
textDecorationLine
Underline or line-through decoration for the text. Details.
textShadowColor
Color of the text shadow. Details.
textShadowOffset
Shadow offset, horizontally or vertically. Details.
textShadowRadius
Shadow blur radius in pixels. Details.
textTransform
Transformation of text upon display. Details.
border
Sets the border for the block on all four sides: width, style and color. Details.
borderTop,
borderRight,
borderBottom,
borderLeft
Setting individual borders for the block edges: width and color. Details.
borderRadius,
borderBottomLeftRadius,
borderBottomRightRadius,
borderTopLeftRadius,
borderTopRightRadius
Rounding of the border corners. Details.
borderWidth,
borderTopWidth,
borderRightWidth,
borderBottomWidth,
borderLeftWidth
Width of the border. Details.
borderColor,
borderTopColor,
borderRightColor,
borderBottomColor,
borderLeftColor
Color of the border. Details.
backgroundColor
Color of the block background. Details.
backgroundGradient
Gradient of the block background. Details.
opacity
Opacity (non-transparency) of the element with its content. Details.
elevation
Block shadow height. Details.
shadowColor
Block shadow color. Details.
flexBasis
Sets the initial block size along the container primary axis. Details.
flexGrow
Block coefficient of growth along the primary axis. Details.
flexShrink
Block coefficient of shrink along the primary axis. Details.
flex
Sets the block behaviour (grow, shrink and basis) along the primary container axis. Details.
alignSelf
Alignment of a specific block across the primary container axis. Details.
Examples
Input styling
<text>Basic field:</text>
<text-input
  name="plainInput"
  formId="formStyling"
/>

<text style={{ marginTop: 20 }}>A field with a placeholder:</text>
<text-input
  name="withPlaceholderInput"
  formId="formStyling"
  placeholder="Placeholder text"
/>

<text style={{ marginTop: 20 }}>A field with an initial value:</text>
<text-input
  name="withInitialValueInput"
  formId="formStyling"
  initialValue='Initial value'
/>

<text style={{ marginTop: 20 }}>Telephone number field:</text>
<text-input
  name="phoneInput"
  formId="formStyling"
  inputmode={'tel'}
  initialValue='12345678900'
/>

<text style={{ marginTop: 20 }}>Password field:</text>
<text-input
  name="phoneInput"
  formId="formStyling"
  inputmode={'password'}
  initialValue='1234567'
/>

<text style={{ marginTop: 20, color: 'silver' }}>Inactive field:</text>
<text-input
  name="phoneInput"
  formId="formStyling"
  initialValue="This text can't be changed"
  disabled
/>

<text style={{ marginTop: 20 }}>Multiline field:</text>
<text-input
  name="multilineInput"
  formId="formStyling"
  multiline
/>

<text style={{ marginTop: 20 }}>A field with custom style:</text>
<text-input
  class='section'
  name='first'
  formId="formStyling"
  initialValue='A different style when focused'
  style={{
    backgroundColor: '#dbdbdb',
    color: 'black',
    padding: [15, 30],
    borderRadius: 50,
    borderWidth: 0,
    focus: {
      backgroundColor: '#aeaeae',
      color: 'white'
    },
  }}
/>
Basic field:
A field with a placeholder:
Placeholder text
A field with an initial value:
Initial value
Telephone number field:
1 234-567-8900
+

Password field:
•••••••
Inactive field:
This text can't be changed
Multiline field:
A field with custom style:
A different style when focused
Basic form
Two input fields. A button press submits the data.

Form code:
<text-input
  name="firstInput"
  formId="formSubmitButton"
  placeholder="Enter firstInput text"
/>

<text-input
  name="secondInput"
  formId="formSubmitButton"
  placeholder="Enter secondInput text"
  style={{ marginTop: 10 }}
/>

<button
  class='primary'
  style={{ marginTop: 20 }}
  title='Submit the data'
  onClick={{
    type: 'submitForm',
    formId: 'formSubmitButton',
    url: ctx.router.url('formSubmitApi')
  }}
/>
Form submit API code:
app.apiCall('formSubmitApi', (ctx, req) => {
  // req.body with the form data
})
Enter firstInput text
Enter secondInput text

Submit the data
Data received by formSubmitApi in req.body (see API how to):
Submit the form to see the data



A form with autosubmit on entry
The data is submitted automatically upon entry.

Form code:
<text-input
  name="firstInput"
  formId="formOnChange"
  placeholder="Enter firstInput text"
  onChange={{
    type: 'submitForm',
    formId: 'formOnChange',
    url: ctx.router.url('formSubmitApi')
  }}
/>

<text-input
  name="secondInput"
  formId="formOnChange"
  placeholder="Enter secondInput text"
  style={{ marginTop: 10 }}
  onChange={{
    type: 'submitForm',
    formId: 'formOnChange',
    url: ctx.router.url('formSubmitApi')
  }}
/>
Form submit API code:
app.apiCall('formSubmitApi', (ctx, req) => {
  // req.body with the form data
})
Enter firstInput text
Enter secondInput text
Data received by formSubmitApi in req.body (see API how to):
Submit the form to see the data



A form with additional parameters
The data is sent to the API either automatically upon a change or a button press. In both cases, an additional submitType parameter is passed indicating the submission origin.

Form code:
<text-input
  name="firstInput"
  formId="formCustomParams"
  placeholder="Enter firstInput text"
  onChange={{
    type: 'submitForm',
    formId: 'formCustomParams',
    url: ctx.router.url('formSubmitApi'),
    params: {
      submitType: 'Changed firstInput'
    }
  }}
/>

<text-input
  name="secondInput"
  formId="formCustomParams"
  placeholder="Enter secondInput text"
  style={{ marginTop: 10 }}
  onChange={{
    type: 'submitForm',
    formId: 'formCustomParams',
    url: ctx.router.url('formSubmitApi'),
    params: {
      submitType: 'Changed secondInput'
    }
  }}
/>

<button
  class='primary'
  style={{ marginTop: 20 }}
  title='Submit the data'
  onClick={{
    type: 'submitForm',
    formId: 'formCustomParams',
    url: ctx.router.url('formSubmitApi'),
    params: {
      submitType: 'Button press'
    }
  }}
/>
Form submit API code:
app.apiCall('formSubmitApi', (ctx, req) => {
  // req.body with the form data
})
Enter firstInput text
Enter secondInput text

Submit the data
Data received by formSubmitApi in req.body (see API how to):
Submit the form to see the data




A form with data validation upon entry
The data is sent to the validation API upon entry, and the submition API upon the button press.

Form code:
<text-input
  name="dateInput"
  formId="formWithValidation"
  placeholder="Enter a date in format DD/MM/YYYY"
  onChange={{
    type: 'submitForm',
    formId: 'formWithValidation',
    url: ctx.router.url('formValidateApi')
  }}
/>

<text-input
  name="emailInput"
  formId="formWithValidation"
  placeholder="Enter email"
  style={{ marginTop: 10 }}
  onChange={{
    type: 'submitForm',
    formId: 'formWithValidation',
    url: ctx.router.url('formValidateApi')
  }}
/>

<button
  class='primary'
  style={{ marginTop: 20 }}
  title='Submit the data'
  onClick={{
    type: 'submitForm',
    formId: 'formWithValidation',
    url: ctx.router.url('formSubmitApi')
  }}
/>
Validation API code:
app.apiCall('formValidateApi', (ctx, req) => {
  const fields = {}

  if (req.body.dateInput && !/d{2}/d{2}/d{4}$/.test(req.body.dateInput)){
    fields.dateInput = 'Invalid date format'
  }

  if (req.body.emailInput && !/^w+@[a-zA-Z_]+?.[a-zA-Z]{2,3}$/.test(req.body.emailInput)){
    fields.emailInput = 'Invalid email format'
  }

  if (Object.keys(fields).length > 0) {
    return {
      success: false,
      statusCode: 422,
      reason: null,
      fields
    }
  }

  return {
    success: true
  }
})
Enter a date in format DD/MM/YYYY
Enter email

Submit the data
Data received by formSubmitApi in req.body (see API how to):
Submit the form to see the data


Create an action
So far, we have learned how to make screens and navigate between them. Now we will create code that is not represented in blocks on the screen, but can be called from it.
As a simple example: ask user a question with three possible answers. Depending on the user's action, the correct answer will show him a message that he is right, and the rest that he is wrong.
So, let's create a file question.tsx, enter the initial code and run it.
// Import showToast action
import {showToast} from '@app/ui'

// Screen with 3 buttons and question
app.screen('/', async function(ctx, req) {
  return <screen title="Question">
   
    <text class="section">The Capital of Great Britain</text>
     
    <button onClick={showToast('Click')} class={["secondary","section"]} title="New York"/>
    <button onClick={showToast('Click')} class={["secondary","section"]} title="Liverpool"/>
    <button onClick={showToast('Click')} class={["secondary","section"]} title="London"/">
  </screen>
})
We have 3 buttons on the screen. Each of them has 2 classes - secondary and section. Secondary to make the button gray. Section to add standard indentation. Alternatively a  section  wrapping block can be used to add such indentation to a group of elements.
All three buttons do the same thing - show a message on the screen. Let's create another action and place its code below such of the screen. For this, we use the method  app.apiCall .
// Handler for button
app.apiCall('/check', async function(ctx, req) {
  return showToast('Clicked');
})
This code in the file question.tsx binds the action handler to the path template check. The handler responds to a POST request on the path question~check.
In order for this action to be called upon a click on the button, a corresponding call needs to be initiated in  onClick . Let's do this for the first button.
<button
  onClick={ctx.router.apiCall('/check', { value: 'New York' })}
  class={["secondary","section"]}
>
  New York
</button>
Note how in this code we use  ctx.router.apiCall  to form a request with parameers. A request to call a handler previously bound to /check path using  app.apiCall . Hence  ctx.router.apiCall  should be seen as a companion method of  app.apiCall .
The parameters are passed as the second argument. They set as an object with the names and values of the parameters.
To obtain the passed parameters, we finally get to use the second argument in the handler  req  containing all of the request parameters. There,  req.body  contains all the parameters that we passed in the  ctx.router.apiCall .
Let's replace the static code with the value from the request.
// Handler for button
app.apiCall('/check', async function(ctx, req) {
  return showToast('You are clicked: ' + req.body.value);
})
Now let's slightly modify the code so that a message saying "Correct" was displayed for the right answer and another otherwise.
// Handler for button
app.apiCall('/check', async function(ctx, req) {
  if ( req.body.value === 'London' ) {
    return showToast("Correct")
  }
  else {
    return showToast(req.body.value + ' is not correct');
  }
})
Let's insert these actions in all 3 buttons and see how it works.
app.screen('/', async function(ctx, req) {
  return <screen title="Question">
   
    <text class="section">The Capital of Great Britain</text>
     
    <button onClick={ctx.router.apiCall('/check', { value: 'New York'})} class={["secondary","section"]} title="New York"/>
    <button onClick={ctx.router.apiCall('/check', { value: 'Liverpool'})} class={["secondary","section"]} title="Liverpool"/>
    <button onClick={ctx.router.apiCall('/check', { value: 'London'})} class={["secondary","section"]} title="London"/>
  </screen>
})
As we see there is a lot of repetitive code. To avoid this, we will separate the data from the presentation and modify the code so it has no repetitions. At the same time, we will slightly change the verification code to make it shorter.
Resulting code
// Import showToast action
import {showToast} from '@app/ui'

const question = {
  label: 'The Capital of Great Britain',
  answers: [ 'New York', 'Liverpool', 'London' ],
  correct: 'London',
}

// Screen with 3 buttons and question
app.screen('/', async function(ctx, req) {
  return <screen title="Question">

    <text class="section">{question.label}</text>

    {question.answers.map( answer =>
      <button
        onClick={ctx.router.apiCall('/check', { value: answer })}
        class={["secondary","section"]}
        title={answer}
      />
    )}
  </screen>
})

// Handler for button
app.apiCall('/check', async function(ctx, req) {
  return  ( req.body.value === question.correct )
    ? showToast("Correct")
    : showToast(req.body.value + ' is not correct');
})

video-poster
Video poster block. Opens a full screen video playback when clicked. Style and aspect ratio behaviour is equivalent to that of <image>.
Properties
src*: string | { url, width?, height?}
Parameters of the original image. String with source URL or object with fields:
url*: string
A string with URL of the source.
width*: number
Source width. Specified along with height. When both width and height are specified, a possible page jump on first load is prevented when the actual size of the image is not known otherwise. The specified size of the source is the size that is taken into account upon rendering and may differ from the real one.
height*: number
Source height. See width above.
video*: <video>
<video> block to be opened for a full screen playback.
resizeMode: string
Default: cover
How to fit the image in case the target area proportions are different from the source.
 cover  — The image is fitted by covering the entire area. Parts of the image that do not fit into the area become invisible. The aspect ratio is preserved.
 contain  — The image is fited entirety, with space added on the sides with insufficient data. The aspect ratio is preserved.
 stretch  — The image is stretched along the area borders. The aspect ratio is ignored.
style
Changing appearance, position and geometry of the block.
width,
maxWidth,
minWidth
Block width. Details.
height,
maxHeight,
minHeight
Block height. Details.
padding,
paddingHorizontal,
paddingVertical,
paddingTop,
paddingRight,
paddingBottom,
paddingLeft
Internal container indentation. Details.
margin,
marginHorizontal,
marginVertical,
marginTop,
marginRight,
marginBottom,
marginLeft
External indentation of the block. Details.
position
Defines the block positioning method: relative or absolute. Details.
top,
right,
bottom,
left
Define the offset relative to the initial position. Details.
border
Sets the border for the block on all four sides: width, style and color. Details.
borderTop,
borderRight,
borderBottom,
borderLeft
Setting individual borders for the block edges: width and color. Details.
borderRadius,
borderBottomLeftRadius,
borderBottomRightRadius,
borderTopLeftRadius,
borderTopRightRadius
Rounding of the border corners. Details.
borderWidth,
borderTopWidth,
borderRightWidth,
borderBottomWidth,
borderLeftWidth
Width of the border. Details.
borderColor,
borderTopColor,
borderRightColor,
borderBottomColor,
borderLeftColor
Color of the border. Details.
backgroundColor
Color of the block background. Details.
backgroundGradient
Gradient of the block background. Details.
opacity
Opacity (non-transparency) of the element with its content. Details.
elevation
Block shadow height. Details.
shadowColor
Block shadow color. Details.
flexBasis
Sets the initial block size along the container primary axis. Details.
flexGrow
Block coefficient of growth along the primary axis. Details.
flexShrink
Block coefficient of shrink along the primary axis. Details.
flex
Sets the block behaviour (grow, shrink and basis) along the primary container axis. Details.
alignSelf
Alignment of a specific block across the primary container axis. Details.
transform
Transformation of block and container geometry in 2D and 3D space. Details.
Examples
Minimal example. Without specifying of src.width and src.height. Possible block jump can occur on the first page load.
<video-poster
  src={{
    url: 'https://fs.chatium.io/thumbnail/video_6TbT3QgKcP.d5.1920x1080.mp4/s/1080x608'
  }}
  video={{
    src: {
      mp4: 'https://kinescopecdn.net/7363a955-0847-4dd1-b6f9-52c943640dc9/videos/5180e2e7-2cd4-46f4-8754-226e7c2e6b16/assets/0f6e80af-4ad8-49ae-bef1-efb2ba93765f/1080p.mp4'
    }
  }}
/>

An example with src.width and src.height.
<video-poster
  src={{
    url: 'https://fs.chatium.io/thumbnail/video_6TbT3QgKcP.d5.1920x1080.mp4/s/1080x608',
    width: 1080,
    height: 608
  }}
  video={{
    src: {
      mp4: 'https://kinescopecdn.net/7363a955-0847-4dd1-b6f9-52c943640dc9/videos/5180e2e7-2cd4-46f4-8754-226e7c2e6b16/assets/0f6e80af-4ad8-49ae-bef1-efb2ba93765f/1080p.mp4'
    }
  }}
/>

An example with source dimentions being specified different to that of the actual. Property resizeMode='contain' set to fit the image within the dimentions aspect ratio.
<video-poster
  style={{
    backgroundColor: 'blue'
  }}
  src={{
    url: 'https://fs.chatium.io/thumbnail/video_6TbT3QgKcP.d5.1920x1080.mp4/s/1080x608',
    width: 1080,
    height: 1080
  }}
  video={{
    src: {
      mp4: 'https://kinescopecdn.net/7363a955-0847-4dd1-b6f9-52c943640dc9/videos/5180e2e7-2cd4-46f4-8754-226e7c2e6b16/assets/0f6e80af-4ad8-49ae-bef1-efb2ba93765f/1080p.mp4'
    }
  }}
  resizeMode='contain'
/>

video
Video block.
Properties
src*: { mp4, width?, height?} | { hls, width?, height?}
Parameters of the source video. Video source can be specified in either MP4 of HLS formats.
mp4: string
String with a URL to a source in MP4 format.
hls: string
String with a URL to a source in HLS format.
width: number
Width of the source video. Has to be specified along with height. Specifying both width and height is necessary to determine the <video> block aspect ratio. The specified dimentions of the source are the dimentions that taken into account upon rendering and may differ from these of the source. Whenever the specified dimentions are different to the actual, the video is fitted in the area according to resizeMode property.
If the dimentions are not specified, the aspect ratio is calculated based on the loaded poster. If aspect ratio of the poster is defferent from 16:9, a <video> block jump may occur when loading the poster. If a poster hasn't been specified either or loaded yet, the aspect ratio is taken as 16:9.
height: number
Height of the source video. See. width above.
poster: string
URL of the video preview. See src.width above about possible influence of poster on the aspect ratio of the entire <video> block.
autoplay: bool
Default: false
Playback automatic start upon page load. In a browser the video will start muted if the page has been upened via an external link and the domain isn't bookmarked. Sound can be enable by the user if the unmute button is pressed in the block.
muted: bool
Default: false
Mute video playback. The user can't enable the audio.
loop: bool
Default: false
Playback in a loop.
controls: VideoControls
Default: compact
Video controls setting.
 full  — controls at the bottom, with a video progress bar.
 compact  — controls in the middle, with fast forward and rewind buttons.
 mini  — play and pause buttons only.
 none  — initial play button only.
preferredQuality: number
Preferred playback quality. Actual playback quality isn't guarantied to match the preference.
Value examples:
 360
 480
 720
 1080
resizeMode: Resize
Default: contain
How to fit the video in case the target area proportions are different from the source.
 cover  — The video is fitted by covering the entire area. Parts of the video that do not fit into the area become invisible. The aspect ratio is preserved.
 contain  — The video is fited entirety, with space added on the sides with insufficient data. The aspect ratio is preserved.
 stretch  — The video is stretched along the area borders. The aspect ratio is ignored.
onVideoEnd: Action
Action called when playback reaches the end.
style
Changing appearance, position and geometry of the block.
width: number | percentage
Width of the block in either pixels or percent. By default either src.width or width of the poster are used if known, otherwise set to 100% if the parent container has it's flexDirection set to column.
height: number | percentage
Height of the block in either pixels or percent. By default either src.height or height of the poster are used if known, otherwise set to 200 pixels if the parent block has it's flexDirection set to row.
maxWidth,
minWidth
Block width. Details.
maxHeight,
minHeight
Block height. Details.
margin,
marginHorizontal,
marginVertical,
marginTop,
marginRight,
marginBottom,
marginLeft
External indentation of the block. Details.
position
Defines the block positioning method: relative or absolute. Details.
top,
right,
bottom,
left
Define the offset relative to the initial position. Details.
border
Sets the border for the block on all four sides: width, style and color. Details.
borderTop,
borderRight,
borderBottom,
borderLeft
Setting individual borders for the block edges: width and color. Details.
borderRadius,
borderBottomLeftRadius,
borderBottomRightRadius,
borderTopLeftRadius,
borderTopRightRadius
Rounding of the border corners. Details.
borderWidth,
borderTopWidth,
borderRightWidth,
borderBottomWidth,
borderLeftWidth
Width of the border. Details.
borderColor,
borderTopColor,
borderRightColor,
borderBottomColor,
borderLeftColor
Color of the border. Details.
backgroundColor
Color of the block background. Details.
backgroundGradient
Gradient of the block background. Details.
opacity
Opacity (non-transparency) of the element with its content. Details.
elevation
Block shadow height. Details.
shadowColor
Block shadow color. Details.
flexBasis
Sets the initial block size along the container primary axis. Details.
flexGrow
Block coefficient of growth along the primary axis. Details.
flexShrink
Block coefficient of shrink along the primary axis. Details.
flex
Sets the block behaviour (grow, shrink and basis) along the primary container axis. Details.
alignSelf
Alignment of a specific block across the primary container axis. Details.
Examples
Minimal example, without a poster. Just a black rectangle is displayed before start of the playback.
<video
  src={{
    mp4: 'https://kinescopecdn.net/7363a955-0847-4dd1-b6f9-52c943640dc9/videos/5180e2e7-2cd4-46f4-8754-226e7c2e6b16/assets/0f6e80af-4ad8-49ae-bef1-efb2ba93765f/1080p.mp4'
  }}
/>
An example similar to the previous, except the video is vertical. With neither source dimentions nor poster being specified, video is fitted into horizontal 16:9 aspect ratio.
<video
  src={{
    mp4: 'https://kinescopecdn.net/7363a955-0847-4dd1-b6f9-52c943640dc9/videos/bc1e9cce-8382-4e48-a829-fae49fe34763/assets/ba8ff8f5-b9b2-465e-87cf-790e68fb1183/1080p.mp4'
  }}
/>
Vertical video with a poster. Block size adapts the poster aspect ratio. Block jump may occur when loading the poster.
<video
  src={{
    mp4: 'https://kinescopecdn.net/7363a955-0847-4dd1-b6f9-52c943640dc9/videos/bc1e9cce-8382-4e48-a829-fae49fe34763/assets/ba8ff8f5-b9b2-465e-87cf-790e68fb1183/1080p.mp4'
  }}
  poster='https://fs.chatium.io/fileservice/file/thumbnail/h/video_Z9Q1YX5GEK.d6.1080x1920.mp4/s/1080x1920'
/>

Vertical video with a poster and the source dimentions specified. Block size adjusts to the video aspect ratio. The block displays in the suitable size from the very start, without a jump upon loading.
<video
  src={{
    mp4: 'https://kinescopecdn.net/7363a955-0847-4dd1-b6f9-52c943640dc9/videos/bc1e9cce-8382-4e48-a829-fae49fe34763/assets/ba8ff8f5-b9b2-465e-87cf-790e68fb1183/1080p.mp4',
    width: 1080,
    height: 1920
  }}
  poster='https://fs.chatium.io/fileservice/file/thumbnail/h/video_Z9Q1YX5GEK.d6.1080x1920.mp4/s/1080x1920'
/>

Using style, the block size is set to an aspact ratio different to the source video. Property resizeMode='cover' scales the video to cover the entire area. Parts of the video that do not fit into the area become invisible. The aspect ratio is preserved.
<video
  src={{
    mp4: 'https://kinescopecdn.net/7363a955-0847-4dd1-b6f9-52c943640dc9/videos/5180e2e7-2cd4-46f4-8754-226e7c2e6b16/assets/0f6e80af-4ad8-49ae-bef1-efb2ba93765f/1080p.mp4'
  }}
  poster='https://fs.chatium.io/fileservice/file/thumbnail/h/video_6TbT3QgKcP.d5.1920x1080.mp4/s/1080x608'
  style={{ width: 200, height: 300 }}
  resizeMode='cover'
/>

Using style, the block size is set to an aspact ratio different to the source video. Property resizeMode='stretch' stretches the video along the area borders. The aspect ratio is ignored.
<video
  src={{
    mp4: 'https://kinescopecdn.net/7363a955-0847-4dd1-b6f9-52c943640dc9/videos/5180e2e7-2cd4-46f4-8754-226e7c2e6b16/assets/0f6e80af-4ad8-49ae-bef1-efb2ba93765f/1080p.mp4'
  }}
  poster='https://fs.chatium.io/fileservice/file/thumbnail/h/video_6TbT3QgKcP.d5.1920x1080.mp4/s/1080x608'
  style={{ width: 200, height: 300 }}
  resizeMode='stretch'
/>

Block with controls='full'.
<video
  src={{
    mp4: 'https://kinescopecdn.net/7363a955-0847-4dd1-b6f9-52c943640dc9/videos/5180e2e7-2cd4-46f4-8754-226e7c2e6b16/assets/0f6e80af-4ad8-49ae-bef1-efb2ba93765f/1080p.mp4'
  }}
  poster='https://fs.chatium.io/fileservice/file/thumbnail/h/video_6TbT3QgKcP.d5.1920x1080.mp4/s/1080x608'
  controls='full'
/>

Block with controls='compact'.
<video
  src={{
    mp4: 'https://kinescopecdn.net/7363a955-0847-4dd1-b6f9-52c943640dc9/videos/5180e2e7-2cd4-46f4-8754-226e7c2e6b16/assets/0f6e80af-4ad8-49ae-bef1-efb2ba93765f/1080p.mp4'
  }}
  poster='https://fs.chatium.io/fileservice/file/thumbnail/h/video_6TbT3QgKcP.d5.1920x1080.mp4/s/1080x608'
  controls='compact'
/>

Block with controls='mini'.
<video
  src={{
    mp4: 'https://kinescopecdn.net/7363a955-0847-4dd1-b6f9-52c943640dc9/videos/5180e2e7-2cd4-46f4-8754-226e7c2e6b16/assets/0f6e80af-4ad8-49ae-bef1-efb2ba93765f/1080p.mp4'
  }}
  poster='https://fs.chatium.io/fileservice/file/thumbnail/h/video_6TbT3QgKcP.d5.1920x1080.mp4/s/1080x608'
  controls='mini'
/>

Block with controls='none'.
<video
  src={{
    mp4: 'https://kinescopecdn.net/7363a955-0847-4dd1-b6f9-52c943640dc9/videos/5180e2e7-2cd4-46f4-8754-226e7c2e6b16/assets/0f6e80af-4ad8-49ae-bef1-efb2ba93765f/1080p.mp4'
  }}
  poster='https://fs.chatium.io/fileservice/file/thumbnail/h/video_6TbT3QgKcP.d5.1920x1080.mp4/s/1080x608'
  controls='none'
/>

Block with onVideoEnd. Displays a pop up message when playback reaches the end.
<video
  src={{
    mp4: 'https://kinescopecdn.net/7363a955-0847-4dd1-b6f9-52c943640dc9/videos/5180e2e7-2cd4-46f4-8754-226e7c2e6b16/assets/0f6e80af-4ad8-49ae-bef1-efb2ba93765f/1080p.mp4'
  }}
  poster='https://fs.chatium.io/fileservice/file/thumbnail/h/video_6TbT3QgKcP.d5.1920x1080.mp4/s/1080x608'
  onVideoEnd={showToast('The playback has finished')}
/>

image
Image.
Size
The image is displayed in its own dimensions, provided there is enough width space in the containing block. It is otherwise limited to the width of the containing block. Image aspect ratio is preserved unless specified otherwise.
The size is specified in logical pixels instead of physical device pixels. When the maximum displayed quality is required on the devices with high pixel density, it is recomended to have the size of the original image three times the resolution of the displayed one.
Properties
src*: string | { url, width?, height?}
Parameters of the original image. String with source URL or object with fields:
url*: string
A string with URL of the source.
width*: number
Source width. Specified along with height. When both width and height are specified, a possible page jump on first load is prevented when the actual size of the image is not known otherwise. The specified size of the source is the size that is taken into account upon rendering and may differ from the real one.
height*: number
Source height. See width above.
resizeMode: string
Default: cover
How to fit the image in case the target area proportions are different from the source.
 cover  — The image is fitted by covering the entire area. Parts of the image that do not fit into the area become invisible. The aspect ratio is preserved.
 contain  — The image is fited entirety, with space added on the sides with insufficient data. The aspect ratio is preserved.
 stretch  — The image is stretched along the area borders. The aspect ratio is ignored.
style
Changing appearance, position and geometry of the block.
width,
maxWidth,
minWidth
Block width. Details.
height,
maxHeight,
minHeight
Block height. Details.
margin,
marginHorizontal,
marginVertical,
marginTop,
marginRight,
marginBottom,
marginLeft
External indentation of the block. Details.
position
Defines the block positioning method: relative or absolute. Details.
top,
right,
bottom,
left
Define the offset relative to the initial position. Details.
border
Sets the border for the block on all four sides: width, style and color. Details.
borderTop,
borderRight,
borderBottom,
borderLeft
Setting individual borders for the block edges: width and color. Details.
borderRadius,
borderBottomLeftRadius,
borderBottomRightRadius,
borderTopLeftRadius,
borderTopRightRadius
Rounding of the border corners. Details.
borderWidth,
borderTopWidth,
borderRightWidth,
borderBottomWidth,
borderLeftWidth
Width of the border. Details.
borderColor,
borderTopColor,
borderRightColor,
borderBottomColor,
borderLeftColor
Color of the border. Details.
backgroundColor
Color of the block background. Details.
backgroundGradient
Gradient of the block background. Details.
opacity
Opacity (non-transparency) of the element with its content. Details.
elevation
Block shadow height. Details.
shadowColor
Block shadow color. Details.
flexBasis
Sets the initial block size along the container primary axis. Details.
flexGrow
Block coefficient of growth along the primary axis. Details.
flexShrink
Block coefficient of shrink along the primary axis. Details.
flex
Sets the block behaviour (grow, shrink and basis) along the primary container axis. Details.
alignSelf
Alignment of a specific block across the primary container axis. Details.
transform
Transformation of block and container geometry in 2D and 3D space. Details.
Examples
300 pixels wide image, smaller than the 350 pixels wide containing block.
<box style={{ width: 350, border: [ 2, 'solid' ] }}>
  <image src='https://fs.chatium.io/thumbnail/4w5NMYcBKL/s/300x200'/>
</box>

450 pixels wide image, larger than the 350 pixels wide containing block. Limited to the width of the containing block. Image aspect ratio is preserved.
<box style={{ width: 350, border: [ 2, 'solid' ] }}>
  <image src='https://fs.chatium.io/thumbnail/4w5NMYcBKL/s/450x300'/>
</box>

Image with specified source dimensions to prevent the page jump on the first load. See src.with in the properties description above.
<box style={{ width: 350, border: [ 2, 'solid' ] }}>
  <image src={{
      url: 'https://fs.chatium.io/thumbnail/4w5NMYcBKL/s/300x200',
      width: 600,
      height: 400
  }} />
</box>

Image with a relative output width. The output height is calculated retaining the aspect ratio of the source specified dimensions. If the source dimensions are not specified, the proportions are calculated based on the actual size of the image. In this case, a page jump is possible on the first load: see src.with in the properties description above.
<box style={{ width: 350, border: [ 2, 'solid' ] }}>
  <image
    src={{
      url: 'https://fs.chatium.io/thumbnail/4w5NMYcBKL/s/300x200',
      width: 600,
      height: 400
    }}
    style={{
      width: '66%'
    }}
  />
</box>

Landscape image in portrait size. Scaling method cover (default). The image is fitted by covering the entire area. Parts of the image that do not fit into the area become invisible. The aspect ratio is preserved.
<image
  src={{
    url: 'https://fs.chatium.io/thumbnail/4w5NMYcBKL/s/300x200',
    width: 300,
    height: 200
  }}
  style={{
    width: '200',
    height: '250',
    border: [ 2, 'solid' ]
  }}
  resizeMode='cover'
/>

Landscape image in portrait size. Scaling method contain. The image is fited entirety, with space added on the sides with insufficient data. The aspect ratio is preserved.
<image
  src={{
    url: 'https://fs.chatium.io/thumbnail/4w5NMYcBKL/s/300x200',
    width: 300,
    height: 200
  }}
  style={{
    width: '200',
    height: '250',
    border: [ 2, 'solid' ]
  }}
  resizeMode='contain'
/>

Landscape image in portrait size. Scaling method stretch. The image is stretched along the area borders. The aspect ratio is ignored.
<image
  src={{
    url: 'https://fs.chatium.io/thumbnail/4w5NMYcBKL/s/300x200',
    width: 300,
    height: 200
  }}
  style={{
    width: '200',
    height: '250',
    border: [ 2, 'solid' ]
  }}
  resizeMode='stretch'
/>

Actions
apiCall
attachMedia
goBack
navigate
showToast
showContextMenu
showTextDialog
refresh

apiCall
Method for registering an API handler in the application. Binds a specific handler to a path pattern.
The method does not require connecting modules and is available globally in the application through the  chatium  object.
app.apiCall(pattern, handler)
Parameters
pattern*: string
The path pattern that the handler will respond at. The path is relative to the current router.
handler*: async function(ctx, req)
API call handler function.
Examples
Shows the time in a pop-up message upon a click on the button.
import {showToast} from '@app/ui'

app.apiCall('showTime', async (ctx, req) => {
  return showToast(new Date().toDateString())
})

app.screen('/', async (ctx, req) => {
  return (
    <screen>
      <button title="Show time" onClick={ctx.router.apiCall('/showTime')}/>
    </screen>
  )
})

attachMedia
Method for file uploading to the application: video, image, audio or any other file.
import { attachMedia } from "@app/ui"
attachMedia({ submitUrl, title, mediaType })
Parameters
params*: { title, mediaType, submitUrl }
Object with upload parameters.
submitUrl*: string
Address of an API call to receive the uploaded file information. It is available to the handler in  req.body.file . In particular:
req.body.file.hash: string
File ID in the application's file storage.
req.body.file.name: string
The original filename before uploading to the application.
title: string
File selection dialog title. Can be used to suggest the user what kind of file to select.
mediaType: photo | video | undefined
Default: undefined
Allows to restrict the uploading file types.
 photo  — limits the file type to images. On mobile devices, offers to select an image from the gallery.
 video  — limits file type to video. On mobile devices, offers to select a video from the gallery.
 undefined  — does not limit the file type. On mobile devices, images and videos located in the gallery may not be accessible with this option.
Examples
Button click prompts to upload an image. Once uploaded, the image is displayed on the screen.
import { attachMedia } from '@app/ui'
import { getThumbnailUrl } from '@app/storage'

app.apiCall('onImageUpload', async (ctx, req) => {
  return ctx.router.navigate('imageUpload?hash=' + req.body.file.hash)
})

app.screen('imageUpload', async (ctx, req) => {
  const onButtonClick = attachMedia({
    title: "Choose an image",
    mediaType: "photo",
    submitUrl: ctx.router.url('onImageUpload')
  })

  return (
    <screen>
      <button title="Upload an image" onClick={onButtonClick} />
      {req.query?.hash && <img src={getThumbnailUrl(req.query.hash)} />}
    </screen>
  )
})

goBack
History navigation method. Returns the user to the previous screen.
import { goBack } from "@app/ui"
attachMedia()
Examples
On the click of a button, jumps back in history
import { goBack } from '@app/ui'

app.screen('goBack', async (ctx, req) => {
  return (
    <screen>
      <button title="Go back" onClick={goBack()} />
    </screen>
  )
})

navigate
Navigates the user to the specified address. If specified, can open a modal window on top of the current.
import { navigate } from "@app/ui"
navigate(url, params)
Parameters
url*: string
The address to navigate to. This function only supports absolute URLs. To pass relative URLs, use  ctx.account.navigate()  and  ctx.router.navigate() .
params: { replace, openInModalScreen, fullScreenModal, openInExternalApp, resetStack }
An object with additional optional parameters specifying how exactly to open the passed URL.
replace: boolean
Default: false
If true - the new screen will replace the previous one in the current navigation/history stack, rather than being added to it. Returning "back" from such a URL ignores the URL on which the user was before the transition.
This also voids the standard animation of the transition between screens in the mobile application.
openInModalScreen: true | false | undefined
Default: undefined
Opens the link in a modal window.
 true  — the new screen will open in a new modal "layer" on top of the current one with the appropriate animation.
 false  — the link is guaranteed to open in the base "layer" and not in the modal. If there are any existing modal layers, this option makes them all close immediately and executes navigate in the base stack. Note that false isn't a default value like it often is elsewhere.
 undefined  — (not specified) navigate will happen in the current stack (the base or a modal).
fullScreenModal: boolean
Default: false
Only used together with openInModalScreen. If true, opens a modal window full screen, presenting a cross to close it.
openInExtrenalApp: boolean
Default: false
In the mobile application allows to open the link in an external application relying on the mobile OS to do so. Typically a link opens in the default mobile browser.
In the web version the link opens via  window.open()  and most likely in a new tab.
resetStack: boolean
Default: false
Applies for the mobile application only. If true, opens the link with a full reset of the stack (voids the back navigation).
Examples
Navigates the user to google, opening a new window
<button
  class="secondary"
  onClick={navigate("https://google.com", {openInExternalApp: true})}
>
  Open Google
</button>

Open Google
Objects ctx.account and ctx.router have their own navigate methods, composing absolute paths in relation to the current account and the router.
<button
  class="secondary"
  onClick={ctx.account.navigate("/")}
>
  Open main page
</button>

Open main page
The following code will navigate the user to the  /test  route declared within the current page.
<button
  class="secondary"
  onClick={ctx.router.navigate("/test")}
>
  Go to test
</button>

Go to test


showToast
Shows a popup message
import {showToast} from '@app/ui'
Properties
message: text
Message text
Examples
Display Hello world message on button click
<button
  onClick={showToast("Hello world")}>
  Click
</button>

showContextMenu
Show context menu.
import { showContextMenu } from "@app/ui"
showContextMenu(menu: Array<MenuItem>)
Parameters
menu: Array<MenuItem>
Array of menu items.
MenuItem: { title, onClick }
Menu item.
title: string
Menu item title.
onClick: ChatiumActions
Click action on menu item.
Examples
Shows a context menu with two items upon the button click. Clicking on the first element displays a pop-up message "Hi!". And clicking on the second element opens Google page.
import { showContextMenu, navigate, showToast } from '@app/ui'

app.screen('showMenu', async (ctx, req) => {
  const menu = [{
    title: 'Say "Hi!"',
    onClick: showToast('Hi!')
  },{
    title: 'Navigate to Google',
    onClick: navigate("https://google.com", {openInExternalApp: true})
  }]

  return (
    <screen>
      <button title="Menu" onClick={showContextMenu(menu)} />
    </screen>
  )
})

showTextDialog
Show text entering dialog
Gives a popup message
import {showTextDialog} from '@app/ui'
Properties
options: object
Object with input field parameters
submitUrl*: string
The URL to submit the user entered text to. The text will be accessible via req.body.value
title: string
Text describing what input we expect from the user
description: string
Description specifying what is expected from the user
placeholder: string
The text in place of the value before the start of input
multiline: boolean
Default: false
Multiline Input
inputType: string
Input type
Possible values: string, code
value: string
Default value
submitData: object
The object that req.body will be extended with.
submitButtonTitle: string
Send button caption
cancelButtonTitle: string
Cancel button caption
Examples
Ask the user for their name and greet them
import {showTextDialog, showToast} from '@app/ui'

// Screen
app.screen('/', function(ctx,req) {
  return <screen>
    <button
      onClick={showTextDialog({title:"What is your name?", submitUrl: ctx.router.url('/say')})}
      class="secondary"
      title="Click"
    />
  </screen>
});

// Handler
app.apiCall('/say', async function(ctx,req) {
  return showToast("Hello " + req.body.value)
});

refresh
Updates the screen with server content.
import { refresh } from "@app/ui"
refresh()
Examples
Displays the server time at the moment of the screen production. Button press refreshes the screen and time.
import { refresh } from '@app/ui'

app.screen('refresh', async (ctx, req) => {
  return (
    <screen>
      <text style={{alignSelf: 'center'}}>Time at the screen update: {new Date().toLocaleTimeString()}</text>
      <button title="Refresh" onClick={refresh()} />
    </screen>
  )
})

App-UI
screen
Style
Dimentions
Indentation
Positioning
Text
Border
Background
Opacity and Shadow
Flex
Transform

screen
Represents the screen and must be the only root element. Contains blocks and meta-information on the screen.
Properties
title: string
Screen title. Is used as the title of a browser window or screen window in the mobile application.
socketIds: string[]
List of sockets triggering reload of the screen contents upon their update. Can be used for screens waiting for a state change from the server: for example, a screen with a purchase status: as soon as it is accepted, the screen can be updated without a user action.
desktopLayout: enum
Default: column
The template in which the screen is displayed on a wide screen. Column by default - narrow screen with vertical positioning.
Possible values: column, empty, mobile

Style, dimentions
width,
maxWidth,
minWidth: number | percent
Sets the block width. As an absolute value in pixels or a relative value in percent of the parent container width. maxWidth has priority over width. minWidth has priority over maxWidth and width.
width: 300
width: 250
width: 350
width: '50%'
width: '100%'
width: '120%'
maxWidth has priority over width:
width: 999,
maxWidth: '50%'
minWidth has priority over width:
width: 10,
minWidth: '50%'
minWidth has priority over maxWidth:
maxWidth: '10%',
minWidth: '75%'
height,
maxHeight,
minHeight: number | percent
Sets the block height. As an absolute value in pixels or a relative value in percent of the parent container height. maxHeight has priority over height. minHeight has priority over maxHeight and height.
height: 200
height: 300
height: '50%'
height: '100%'
height: '120%'
height: 200,
maxHeight: '50%'
overflow: visible | hidden
Default: hidden
Container property.
Specifies if the content should be clipped upon taking space beyond the container boundaries.
width: 250,
overflow: 'visible'
width: 350
width: 250,
overflow: 'hidden'
width: 350

Style, indentation
padding: number
Sets the internal indentation on all sides of the container. Properties width and height include the indentation set by padding.
 number  — same indentation on all four sides.
 [ number, number ]  — different indents along the vertical and horizontal axis of the container. The first element of the array sets the vertical indent and the second sets horizontal.
 [ number, number, number ]  — different indents at the top, bottom and the horizontal axis of the container. The first element of the array sets the top indent, the second sets the horizontal and the last is the bottom.
 [ number, number, number, number ]  — different indents on all four sides. The first element of the array sets the top indent, and the following elements set the indents in clockwise order.
In the following examples width = 150 and height = 100 are set for every container. The area available for the content is different in every container because both width and height include the indents set by padding.
padding: 10
padding: [10,25]
padding:
[10,25,40]
padding:
[10,25,40,55]
paddingVertical,
paddingHorizontal: number
Sets the internal indentation along the vertical or horizontal axis of the container. Has priority over padding.
paddingVertical: 10
paddingHorizontal: 10
padding: 25,
paddingVertical: 10
padding: 25,
paddingHorizontal: 10
paddingTop,
paddingRight,
paddingBottom,
paddingLeft: number
Sets the internal indentation. One property per indent. Has priority over padding, paddingVertical, paddingHorizontal.
paddingTop: 10
paddingRight: 10
paddingBottom: 10
paddingLeft: 10
padding: 25,
paddingBottom: 10,
paddingRight: 0

paddingHorizontal: 25,
paddingLeft: 10
margin: number
Sets the external indentation on all sides of the element. Can have a negative value. Properties width and height don't include the indentation set by margin.
 number  — same indentation on all four sides.
 [ number, number ]  — different indents along the vertical and horizontal axis of the container. The first element of the array sets the vertical indent and the second sets horizontal.
 [ number, number, number ]  — different indents at the top, bottom and the horizontal axis of the container. The first element of the array sets the top indent, the second sets the horizontal and the last is the bottom.
 [ number, number, number, number ]  — different indents on all four sides. The first element of the array sets the top indent, and the following elements set the indents in clockwise order.
In the following examples width = 100 and height = 60 are set for every container. The area available for the content is the same for every container because both width and height don't include the indents set by margin.
margin: 10
margin: -10
margin: [10,25]
margin: [10,-25]
margin:
[10,25,40]
margin:
[10,25,-10,-25]
Negative margin allows to overlay the elements by shifting the indents inwards.
width: 250,
height: 100
width: 150,
height: 100,
margin: -20
marginVertical,
marginHorizontal: number
Sets the external indentation along the vertical or horizontal axis of the block. Has priority over margin. Can have a negative value. See margin for examples of negative indentation. Properties width and height don't include the indentation set by margin....
marginVertical: 10
marginHorizontal: 10
marginTop,
marginRight,
marginBottom,
marginLeft: number
Sets the external indentation. One property per indent. Has priority over margin, marginVertical, marginHorizontal. Can have a negative value. See margin for examples of negative indentation. Properties width and height don't include the indentation set by margin....
marginTop: 10
marginRight: 10
marginBottom: 10
marginLeft: 10

Style, positioning
position
Default: relative
Defines the block positioning method.
 relative  — block's natural position is taken as the initial position. The top, right, bottom and left define the offset relative to the initial position.
 absolute  — the position relative to the boundaries of the container is taken as the initial. The top, right, bottom and left define the offset relative to the initial position and potentialy the block size as well.
top,
right,
bottom,
left: number | percent
Define the offset relative to the initial position. As an absolute value in pixels or a relative value in percent to the container size.
With position = 'relative' block's natural position is taken as the initial position. Then the offset is set using one or two coordinates: left or right horizontally and top or bottom vertically. The property left has priority over right if both are set, and top over bottom.
In case of position = 'absolute' the position relative to the boundaries of the container is taken as the initial. Properties top, right, bottom and left define the offset relative to the initial position. If left is set in pair with right, these properties define the block horizontal size, and property top in pair with bottom the vertical size.
In the example with position = 'relative', the natural position of the block is immediately after the text block it is following. Then properties top, right, bottom and left define a shift relative to that natural position.
position: 'relative',
left: 10,
top: -10
Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua.
position: 'relative',
right:  '5%',
bottom: '-5%'
Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua.
In its turn position = 'absolute' places the block in relation to the borders of the container, ignoring the preceding text block. In such case the block size can optionally be set via offsets from the opposite edges of the container.
position: 'absolute',
left: 10,
top:  20,


Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua.
position: 'absolute',
left:   10,
top:    20,
right:  30,
bottom: 40
Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua.

Style, text
color: Color
Color of the text
<text>Normal</text>
<text style={{ color: 'red' }}>Red</text>
<text style={{ color: '#38e' }}>hex code in short notation</text>
<text style={{ color: '#3388ee' }}>hex code in long notation</text>
<text style={{ color: 'rgb(128, 100, 88)' }}>rgb</text>
<text style={{ color: 'rgb(128, 100, 88, 0.5)' }}>rgb with transparency</text>
Normal
Red
hex code in short notation
hex code in long notation
rgb
rgb with transparency
fontFamily: string
Name of the font family. Can be a title of a custom font set prior via chatiumrc.
Value examples:
 "serif"
 "sans"
 "mono"
 ...font title
<text>default</text>
<text style={{ fontFamily: 'sans' }}>sans</text>
<text style={{ fontFamily: 'serif' }}>serif</text>
<text style={{ fontFamily: 'mono' }}>mono</text>
default
sans
serif
mono
fontSize: number | StandardFontSize
Font size. Arbitrary or predefined.
Possible values: number, "xs", "sm", "md", "lg", "xl", "2xl", "3xl"
<text style={{ fontSize: 50 }}>50</text>
<text style={{ fontSize: 40 }}>40</text>
<text style={{ fontSize: '3xl' }}>3xl</text>
<text style={{ fontSize: '2xl' }}>2xl</text>
<text style={{ fontSize: 'xl' }}>xl</text>
<text style={{ fontSize: 'lg' }}>lg</text>
<text style={{ fontSize: 'md' }}>md</text>
<text style={{ fontSize: 'sm' }}>sm</text>
<text style={{ fontSize: 'xs' }}>xs</text>
50
40
3xl
2xl
xl
lg
md
sm
xs
fontStyle
An option to make the font cursive.
Possible values: normal, italic
<text>default</text>
<text style={{ fontStyle: 'normal' }}>normal</text>
<text style={{ fontStyle: 'italic' }}>italic</text>
default
normal
italic
fontWeight
Weight of the font (boldness).
Possible values: normal, bold, 100, 200, 300, 400, 500, 600, 700, 800, 900
<text style={{ fontWeight: 'normal' }}>normal</text>
<text style={{ fontWeight: 'bold' }}>bold</text>
<text style={{ fontWeight: '100' }}>100</text>
<text style={{ fontWeight: '200' }}>200</text>
<text style={{ fontWeight: '300' }}>300</text>
<text style={{ fontWeight: '400' }}>400</text>
<text style={{ fontWeight: '500' }}>500</text>
<text style={{ fontWeight: '600' }}>600</text>
<text style={{ fontWeight: '700' }}>700</text>
<text style={{ fontWeight: '800' }}>800</text>
<text style={{ fontWeight: '900' }}>900</text>
normal
bold
100
200
300
400
500
600
700
800
900
fontVariant: ('normal' | 'tabular-nums')[]
 tabular-nums  — font property making all the digits same width.
<text style={{ fontVariant: [ 'tabular-nums' ] }}>1234567890, tabular-nums</text>
1234567890, tabular-nums
letterSpacing: "normal" | number
Spacing between the letters.
<text style={{ letterSpacing: 'normal' }}>Example of letter spacing: normal</text>
<text style={{ letterSpacing: 1.5 }}>Example of letter spacing: 1.5</text>
<text style={{ letterSpacing: 2 }}>Example of letter spacing: 2</text>
Example of  letter spacing: normal
Example of  letter spacing: 1.5
Example of  letter spacing: 2
lineClamp: number
Limiting the height of the text by the number of lines. Excess text is trimmed according to textOverflow property.
<text style={{ lineClamp: 3 }}>
  Example of{'\n'}
  line{'\n'}
  clamp{'\n'}
  this text is the fourth line and will not be displayed
</text>
Example of
line
clamp
this text is the fourth line and will not be displayed
lineHeight: "normal" | number
Sets the amount of space allocated for a single line of text.
<text style={{ fontSize: 16 }}>
  Example of lineHeight{'\n'}
  default
</text>

<text style={{ fontSize: 16, lineHeight: 32 }}>
  Example of lineHeight{'\n'}
  twice the size of the font
</text>

<text style={{ fontSize: 16, lineHeight: 100, lineClamp: 1 }}>
  Example of lineHeight used to set the block height and centering of a single line text.
</text>
Example of lineHeight
default
Example of lineHeight
twice the size of the font
Example of lineHeight used to set the block height and centering of a single line text.
textAlign
Default: auto
Sets the text alignment. The value of auto sets the alignment depending on the individual properties of the align and alignItems blocks, where applicable.
Possible values: auto, left, center, right, justify
Text example with textAlign = left.
Text example with  textAlign = center.
Text example with  textAlign = right.
Text example with textAlign = justify. Setting of this property make the text stretch to fith the container width.
textAlignVertical
Default: top
Vertical alignment of text in a container.
Possible values: top, center, bottom
top
center
bottom
textDecorationLine
Underline or line-through decoration for the text.
Possible values: none, underline, line-through, underline line-through
none
underline
line-through
underline line-through
textOverflow
Method for text truncation when breaching the container boundaries.
 ellipsis  — text is truncated with ellipsis.
 clip  — text is truncated at the container edge. Works only when used together with lineClamp: 1. In other cases the text gets truncated with ellipsis.
Possible values: clip, ellipsis
Text example with textOverflow = clip: a b c d e f g h i j k l m n o p q r s t u v w x x y z
Text example with textOverflow = ellipsis: a b c d e f g h i j k l m n o p q r s t u v w x x y z
Text example with textOverflow = clip: a b c d e f g h i j k l m n o p q r s t u v w x x y z
Text example with textOverflow = ellipsis: a b c d e f g h i j k l m n o p q r s t u v w x x y z
textShadowColor: Color
Color of the text shadow. To make the shadow appear, either textShadowOffset or textShadowRadius must also be specified.
<text style={{ textShadowColor: 'red', textShadowOffset: { width: 2, height: 2 } }} >
  Text with red shadow
</text>

<text style={{ textShadowColor: '#38e', textShadowOffset: { width: 2, height: 2 } }} >
  Text with shadow color in short notation
</text>

<text style={{ textShadowColor: '#3388ee', textShadowOffset: { width: 2, height: 2 } }} >
  Text with shadow color in long notation
</text>

<text style={{ textShadowColor: 'rgb(128, 100, 88)', textShadowOffset: { width: 2, height: 2 } }} >
  Text with shadow color in rgb
</text>

<text style={{ textShadowColor: 'rgb(128, 100, 88, 0.5)', textShadowOffset: { width: 2, height: 2 } }} >
  Text with shadow color in rgb with transparency
</text>
Text with red shadow
Text with shadow color in short notation
Text with shadow color in long notation
Text with shadow color in rgb
Text with shadow color in rgb with transparency
textShadowOffset: { width, height }
Shadow offset, horizontally or vertically.
width: number
height: number
<text style={{ textShadowColor: 'lightgrey', textShadowOffset: { width: 3, height: 3 } }} >
  Shadow offset width = 3 and height = 3.
</text>

<text style={{ textShadowColor: 'lightgrey', textShadowOffset: { width: -5, height: -2 }, marginTop: 10 }} >
  Shadow offset with a negative value width = -5 and height = -2.
</text>
Shadow offset width = 3 and height = 3.
Shadow offset with a negative value width = -5 and height = -2.
textShadowRadius: number
Shadow blur radius in pixels.
<text style={{ textShadowRadius: 3 }} >
  Example of textShadowRadius = 3 without shadow offset.
</text>

<text style={{ textShadowOffset: { width: 3, height: 3 }, textShadowRadius: 3, marginTop: 10 }} >
  Example of textShadowRadius = 3 and shadow offset width = 3 and height = 3.
</text>

<text style={{ textShadowColor: 'grey', textShadowOffset: { width: 3, height: 3 }, textShadowRadius: 3, marginTop: 10 }} >
  Example of textShadowRadius = 3 with shadow offset and color.
</text>
Example of textShadowRadius = 3 и без смещения тени.
Example of textShadowRadius = 3 и смещением тени width = 3 и height = 3.
Example of textShadowRadius = 3, смещением и заданием цвета тени.
textTransform
Transformation of text upon display.
 none  — no transformation.
 uppercase  — convert everything to upper case.
 lowercase  — convert everything to lover case.
 capitalize  — capitalize the first letters of every word.
<text>oRiGiNaL tExT cOmB</text>

<text style={{ marginTop: 10 }}>textTransform = 'capitalize':</text>
<text style={{ textTransform: 'capitalize' }}>oRiGiNaL tExT cOmB</text>

<text style={{ marginTop: 10 }}>textTransform = 'uppercase':</text>
<text style={{ textTransform: 'uppercase' }}>oRiGiNaL tExT cOmB</text>

<text style={{ marginTop: 10 }}>textTransform = 'lowercase':</text>
<text style={{ textTransform: 'lowercase' }}>oRiGiNaL tExT cOmB</text>
oRiGiNaL tExT cOmB
textTransform = 'capitalize':
ORiGiNaL TExT COmB
textTransform = 'uppercase':
ORIGINAL TEXT COMB
textTransform = 'lowercase':
original text comb

Style, border
border: [ border-width, border-style, border-color ]
Sets the border for the block on all four sides.
border-width: number
Width of the border in pixes.
border-style
Style of the border:
 solid  — a solid line.
 dotted  — a dotted line.
 dashed  — a dashed line.
border-color: Color
Color of the border.
border: [
  1, 'solid', 'black'
]
border: [
  3, 'solid', 'black'
]
border: [
  1, 'solid', 'red'
]
border: [
  3, 'solid', 'red'
]
border: [
  1, 'dotted', 'black'
]
border: [
  3, 'dotted', 'black'
]
border: [
  1, 'dashed', 'black'
]
border: [
  3, 'dashed', 'black'
]
borderTop,
borderRight,
borderBottom,
borderLeft: [ border-width, border-color ]
Setting individual borders for the block edges. Only the solid border style is available. Has priority over border and can be combined with.
border-width: number
Width of the border in pixels.
border-color: Color
Color of the border.
borderTop: [
  1, 'black'
]
borderBottom: [
  3, 'grey'
]
borderLeft:  [ 15, 'rgb(0,99,0)' ],
borderRight: [ 15, 'rgb(0,0,99)' ]

Use of border as a base while setting individual styles for two other edges:
border: [ 3, 'solid', '#c50' ],
borderLeft:  [ 15, 'rgb(0,99,0)' ],
borderRight: [ 15, 'rgb(0,0,99)' ]
Current limitation doesn't allow any style other than solid for varied borders withing a block:
border: [ 3, 'dotted', '#c50' ],
borderLeft:  [ 15, 'rgb(0,99,0)' ],
borderRight: [ 15, 'rgb(0,0,99)' ]
borderRadius: number
 number  — same radius on all four corners.
 [ number, number, number, number ]  — individual border radius, starting from the top left corner and continuing clockwise.
border: [ 1, 'solid', 'black' ],
borderRadius: 10
border: [ 1, 'solid', 'black' ],
borderRadius: [ 0, 10, 20, 30 ]
border: [ 1, 'solid', 'black' ],
borderBottom: [ 15, 'rgb(0,99,0)' ],
borderRight: [ 7, 'rgb(0,0,99)' ],
borderRadius: [ 0, 10, 20, 30 ]
borderBottomLeftRadius,
borderBottomRightRadius,
borderTopLeftRadius,
borderTopRightRadius: number
Radius of individual border corners. Has proirity over borderRadius and can be combined with.
border: [ 1, 'solid', 'black' ],
borderBottomLeftRadius: 20
border: [ 1, 'solid', 'black' ],
borderRadius: 20,
borderBottomLeftRadius: 0
borderWidth: number
Width of the border. Can either set a same width for the entire border or individual sides. Has priority over border and can be combined with.
 number  — same width on all four sides.
 [ number, number ]  — individual width of horizontal and vertical block edges. The first element sets the width for horizontal edges and the second for the vertical.
 [ number, number, number ]  — individual width for the top, bottom and vertical edges. The first element sets the width for the top edge, the second sets the width for the vertical edges and the last is the bottom edge.
 [ number, number, number, number ]  — individual width for all four edges. The first element sets the width for top the edge, with following parameters setting the edges in clockwise order.
borderWidth: 1

borderWidth: [ 1, 10 ]

borderWidth: [ 1, 10, 20 ]

borderWidth: [ 1, 10, 20, 30 ]

Current limitation doesn't allow any style other than solid for varied borders withing a block:
border: [ 1, 'dotted', 'black'],
borderWidth: [ 1, 10, 20, 30 ]

borderTopWidth,
borderRightWidth,
borderBottomWidth,
borderLeftWidth: border-width
Setting individual width of the edges. Has priority over border and borderWidth. Can be combined with them.
borderBottomWidth: 10

borderWidth: 1,
borderBottomWidth: 10,
borderRightWidth: 20
Current limitation doesn't allow any style other than solid for varied borders withing a block:
border: [ 1, 'dotted', 'black'],
borderBottomWidth: 10
borderColor: Color
Color of the border. Has priority over border and can be combined with.
 Color  — same color for all four edges
 [ Color, Color ]  — individual color of horizontal and vertical block edges. The first element sets the color for horizontal edges and the second for the vertical.
 [ Color, Color, Color ]  — individual color for the top, bottom and vertical edges. The first element sets the color for the top edge, the second sets the color for the vertical edges and the last is the bottom edge.
 [ Color, Color, Color, Color ]  — individual color for all four edges. The first element sets the color for top the edge, with following parameters setting the edges in clockwise order.
Use of border as a base, redefining color via borderColor:
border: [ 3, 'solid', 'black' ],
borderColor: 'rgb(200,0,0)'
borderWidth: 3,
borderColor: '#c00'
borderWidth: 3,
borderColor: [ '#c00', '#0c0' ]
borderWidth: 3,
borderColor: [ '#c00', '#0c0', '#00c' ]
borderWidth: 3,
borderColor: [
  '#c00', '#0c0', '#00c', '#f90'
]
Current limitation doesn't allow any style other than solid for varied borders withing a block. Varied colors in this case:
border: [ 3, 'dotted', 'black' ],
borderColor: [ '#c00', '#0c0' ]
borderTopColor,
borderRightColor,
borderBottomColor,
borderLeftColor: Color
Setting individual color of the edges. Has priority over borderColor and border. Can be combined with them.
borderWidth: 3,
borderBottomColor: '#c00'
Use of border as a base and setting individual colors of two edges:
border: [ 3, 'solid', '#0c0' ],
borderBottomColor: '#c00',
borderRightColor:  '#00c'
Use of  borderColor as a base and setting individual colors of two edges:
borderWidth: 3,
borderColor: '#0c0',
borderBottomColor: '#c00',
borderRightColor:  '#00c'
Current limitation doesn't allow any style other than solid for varied borders withing a block. Varied colors in this case:
border: [ 3, 'dotted', 'black' ],
borderBottomColor: '#c00'

Style, background
backgroundColor: Color
Color of the block background.
Red:
backgroundColor: 'red'
hex code in a short notation:
backgroundColor: '#38e'
hex code in a long notation:
backgroundColor: '#3388ee'
rgb:
backgroundColor: 'rgb(128, 100, 88)'
rgb with transparency:
backgroundColor: 'rgb(128, 100, 88, 0.5)'
backgroundGradient: [ gradient-direction, ...gradient-color[] ]
gradient-direction
Default: to bottom
Gradient direction. A string constant for one of the four directions or a number. A number means an angle, where 0 is equivalent of "to top", 90 equivalent of "to right", and so on clockwise. The value can be negative.
Possible values: "to right", "to left", "to bottom", "to top", number
gradient-color: Color | [ Color, number ]
Color and its position in the gradient. Position is defined as a fraction value between 0 and 1. Value of 0 means a position at the start of the gradient and 1 at the end. If a position is not defined, it is determined automatically.
backgroundGradient: [
  'black',
  'yellow'
]
backgroundGradient: [
  'to right',
  'black',
  'yellow'
]
backgroundGradient: [
  'to right',
  'black',
  'red',
  'yellow'
]
backgroundGradient: [
  'to right',
  [ 'black', 0.2 ],
  [ 'red',   0.3 ],
  'yellow'
]
backgroundGradient: [
  45,
  [ 'black', 0.2 ],
  [ 'red',   0.3 ],
  'yellow'
]
backgroundImage: string
A property to set an image as a block background. At the moment is available for the <screen> block only.

Style, opacity and shadow
opacity: number
Sets the opacity (non-transparency) of the element with its content. A number between 0 and 1. Where 0 means full transparency and 1 full visibility.

opacity: 0.75,
backgroundColor: '#ccd'
opacity: 0.50,
backgroundColor: '#ccd'
opacity: 0.25,
backgroundColor: '#ccd'
elevation: number (0 - 24)
Block shadow height. A number between 0 and 24. Shadow is displayed for the blocks with solid (non-transparent) background color.
elevation: 24
elevation: 15
elevation: 5
shadowColor: Color
Block shadow color. Requres the elevation to be set for the shadow to appear. Shadow is displayed for the blocks with solid (non-transparent) background color.
elevation: 10,
shadowColor: 'black'
elevation: 10,
shadowColor: 'red'
elevation: 10,
shadowColor: 'rgb(0, 255, 0)'
elevation: 10,
shadowColor: 'black',
backgroundColor: 'white'

Style, flex
flexDirection
Container property.
Defines the container primary axis (blocks direction in the container).
Possible values: row, column, row-reverse, column-reverse
flexDirection: 'row'
A
B
C
flexDirection: 'row-reverse'
A
B
C
flexDirection: 'column'
A
B
C
flexDirection: 'column-reverse'
A
B
C
flexBasis: "auto" | number | percent
Default: auto
Block property.
Sets the initial block size along the container primary axis. Container primary axis is set via flexDirection.
The initial size has a nature of recommended only and can vary depending on other properties of the block.
Properties flexGrow and flexShrink can affect the block size depending on the container size.
Such properties as minWidth, minHeight, maxWidth and maxHeight have priority over flexBasis.
With flexBasis = auto initial block size depends on the content. Container size doesn't affect block size. Upon insufficient space in the container, blocks take space beyound the container boundaries.
flexDirection: 'row'
fb: auto
AAAAAA
fb: auto
BBBBBBBBBB
fb: auto
CCCCCCCCCCCCCC
A percent value of flexBasis sets the initial block size depending on the container size. Blocks are not restricted by the container size and upon insufficient space will take space beyound the container boundaries.
flexDirection: 'row'
fb: 25%
fb: 50%
An absolute value of flexBasis sets the initial block size in pixels independent of the container size. Blocks are not restricted by the container size and upon insufficient space will take space beyound the container boundaries.
flexDirection: 'row'
fb: 100
fb: 150
An expample of using blocks with different types of flexBasis size within one container. Illustrates how blocks take space beyound the container boundaries
flexDirection: 'row'
fb: auto
fb: 50%
fb: 80
flexGrow: number
Default: 0
Block property.
Block coefficient of growth along the primary axis. Container primary axis is set via flexDirection.
Defines the share of container vacant space the block will get upon growth. The share is calculated as a part set via flexGrow of the total summ of flexGrow of all blocks within the container.
Important to note this is a share from the container vacant space only, and not from all available. Vacant space is what is left after the blocks take the available space according to their initial size. Block initial size is defined by the content, via flexBasis or size properties such as width and height.
Such properties as minWidth, minHeight, maxWidth and maxHeight have priority over flexGrow.
Example of blocks with zero flexGrow. Such blocks don't grow and take the space according to their initial size only. All vacant space remains unoccupied.
flexDirection: 'row'
fg: 0
fg: 0
fg: 0
Vacant
space
All blocks have set equal flexGrow. Blocks grow proportionally distributing the vacant space between each other.
flexDirection: 'row'
fg: 1
fg: 1
fg: 1
Blocks with zero flexGrow don't grow and take the space according to their initial size only, while the sole block with flexGrow greater than zero takes the entire vacant space. Particular greater than zero value of flexGrow in such case is not important.
flexDirection: 'row'
fg: 0
fg: 0
fg: 1
Blocks have different flexGrow values. Blocks grow distributing vacant space according to the shares set via flexGrow.
flexDirection: 'row'
fg: 1
fg: 3
fg: 1
Blocks have zero initial size set via flexBasis. Blocks with zero initial size don't take any space and the entire area of the container becomes vacant space. Then the entire block size, and not just its growth, is calculated based on the share set via flexGrow.
flexDirection: 'row'
fg: 1,
fb: 0%
fg: 3,
fb: 0%
fg: 1,
fb: 0%
Upon insufficient space in the container, blocks take space beyound the container boundaries. To change this behaviour, property flexWrap = wrap makes blocks wrap onto a new line instead. Then vacant space the blocks distribute between each other is such within the line.
flexDirection: 'row'
flexWrap: 'wrap'
fg: 1
fg: 1
fg: 1
fg: 1
fg: 1
fg: 1
fg: 2
flexShrink: number
Default: 0
Block property.
Block coefficient of shrink along the primary axis. Container primary axis is set via flexDirection.
Takes effect if the size of all blocks along the same line start to take space beyound the container boundaries. The coefficient defines the share of the space beyound the boundaries that will be subtracted from the size of the current block.
Property flexShrink works in a similar way to flexGrow, except that instead of vacant space in the container, it is the space beyound the boundaries what is taken into account, and instead of blocks growing, their sizes are reduced by shares of this space.
Such properties as minWidth, minHeight, maxWidth and maxHeight have priority over flexShrink.
Blocks with zero flexShrink do not shrink when there is not enough space in the container. The size of the blocks in this example is determined by the content and takes up extra space beyound the container boundaries.
flexDirection: 'row'
fs: 0
AAAAAAAA
fs: 0
BBBBBBBBBBBBBB
fs: 0
CCCCCCCCCCCCCCCCCCCC
Blocks with the same flexShrink do shrink by the same absolute amount.
flexDirection: 'row'
fs: 1
AAAAAAAA
fs: 1
BBBBBBBBBBBBBB
fs: 1
CCCCCCCCCCCCCCCCCCCC
Blocks with zero flexShrink do not shrink, while the only block with a non-zero flexShrink does shrink until either there is no more extra space taken beyound the container boundaries or it reaches own minimal size. Particular greater than zero value of flexShrink in such case is not important.
flexDirection: 'row'
fs: 0
AAAAAAAA
fs: 1
BBBBBBBBBBBBBB
fs: 0
CCCCCCCCCCCCCCCCCCCC
The only block with zero flexShrink doesn't shrink. While two other blocks with greater than zero flexShrink do shrink by the shares of extra space beyound the boundaries. Shares calculated based on flexShrink.
flexDirection: 'row'
fs: 0
AAAAAAAA
fs: 3
BBBBBBBBBBBBBB
fs: 1
CCCCCCCCCCCCCCCCCCCC
flex: number
Block property.
Sets the block behaviour (grow, shrink and basis) along the primary container axis. Container primary axis is set via flexDirection
The property should be seen as short a syntax for common applications.
Such properties as minWidth, minHeight, maxWidth and maxHeight have priority over flex.
 number = 0  — same as flexGrow = 0, flexShrink = 0 и flexBasis = auto.
 number > 0  — same as flexGrow = number, flexShrink = 1 и flexBasis = 0%.
 number < 0  — same as flexGrow = 0, flexShrink = 1 и flexBasis = auto.
flex = 0 blocks take the space according to their initial size only. Neither grow nor shrink. All vacant space in the container remains unoccupied.
flexDirection: 'row'
f: 0
f: 0
f: 0
Vacant
space
flex > 0 is the simpliest way to use flex. Block initial size is ignored. The final size of the block is defined by the share set in flexGrow of vacant space in the container.
flexDirection: 'row'
f: 1
f: 3
f: 1
flex < 0 is a condition for the block size to be calculated according to vacant space only. Block minimal size is set with either minWidth or minHeight. Particular less than zero value of flex doesn't matter.
flexDirection: 'row'
f: -1
AAAAAAA
f: -1
BBBBBBBBBBBB
f: -1
CCCCCCCCCCCCCCCCC
justifyContent
Container property.
Alignment and distribution of the space along the primary container axis. Container primary axis is set via flexDirection.
The property defines how the space gets distributd around and between the blocks along the primary container axis. Alignment takes place after sizing and margins are applied.
Possible values: flex-start, flex-end, center, space-between, space-around, space-evenly
justifyContent: 'flex-start',
flexDirection: 'row'
A
B
C
justifyContent: 'flex-end',
flexDirection: 'row'
A
B
C
justifyContent: 'center',
flexDirection: 'row'
A
B
C
justifyContent: 'space-between',
flexDirection: 'row'
A
B
C
justifyContent: 'space-around',
flexDirection: 'row'
A
B
C
justifyContent: 'space-evenly',
flexDirection: 'row'
A
B
C
alignItems
Container property.
Alignment of the blocks across the primary container axis. Container primary axis is set via flexDirection.
Possible values: flex-start, flex-end, center, baseline, stretch
alignItems: 'flex-start',
flexDirection: 'row'
A
B
C
alignItems: 'flex-end',
flexDirection: 'row'
A
B
C
alignItems: 'center',
flexDirection: 'row'
A
B
C
alignItems: 'baseline',
flexDirection: 'row'
A
B
C
alignItems: 'stretch',
flexDirection: 'row'
A
B
C
alignContent
Container property.
Distribution of space across the container primary axis. Container primary axis is set via flexDirection.
Applies within containers with flexWrap = wrap or flexWrap = wrap-reverse. For the containers with flexWrap property in its default value no-wrap, the property alignContent has no effect.
Possible values: flex-start, flex-end, center, stretch, space-between, space-around
alignContent: 'flex-start',
flexWrap: 'wrap',
flexDirection: 'row'
A
B
C
D
E
F
G
H
alignContent: 'flex-end',
flexWrap: 'wrap',
flexDirection: 'row'
A
B
C
D
E
F
G
H
alignContent: 'center',
flexWrap: 'wrap',
flexDirection: 'row'
A
B
C
D
E
F
G
H
alignContent: 'stretch',
flexWrap: 'wrap',
flexDirection: 'row'
A
B
C
D
E
F
G
H
alignContent: 'space-between',
flexWrap: 'wrap',
flexDirection: 'row'
A
B
C
D
E
F
G
H
I
J
K
L
M
alignContent: 'space-around',
flexWrap: 'wrap',
flexDirection: 'row'
A
B
C
D
E
F
G
H
I
J
K
L
M
alignSelf
Block property.
Alignment of a specific block across the primary container axis. Container primary axis is set via flexDirection.
Allows to set a particular alignment for a block regardless of alignItems container propery.
Possible values: auto, flex-start, flex-end, center, baseline, stretch
flexDirection: 'row'
alignSelf:
flex-start
alignSelf:
flex-end
alignSelf:
center
alignSelf:
stretch
flexWrap
Default: nowrap
Container property.
Wrapping of the blocks on the new line along the primary container axis. Container primary axis is set via flexDirection.
Blocks intent to fit into one line and by default upon insufficiency of vacant space start to take extra space beyound the container boundaries. Property values flexWrap = wrap or flexWrap = wrap-reverse change this behavior and blocks start to wrap onto the new line instead of taking space beyound the container boundaries.
Possible values: nowrap, wrap, wrap-reverse
flexWrap: 'wrap',
flexDirection: 'row'
A
B
C
D
E
F
G
H
flexWrap: 'wrap-reverse',
flexDirection: 'row'
A
B
C
D
E
F
G
H
flexWrap: 'nowrap',
flexDirection: 'row'
A
B
C
D
E
F

Style, transform
transform: { transform-action: value }[]
Transformation of block and container geometry in 2D and 3D space.
The transformation is done via transform-actions. Several actions can be applied per block at the same time.
Important to note that all transformation is applied only to the display geometry of blocks and containers. It is applied after the final arrangement of blocks and containers on the screen, and therefore does not affect the relationship between them.
The following transform-action are available:
rotate,
rotateX,
rotateY,
rotateZ: angle
Rotation around one of the three axes. The angle of rotation is specified in degrees with the "deg" suffix or radians with the "rad" suffix. Can take negative value.
The rotate and rotateZ actions are synonyms.
It is not possible to use several actions of the rotate group in one block at the same time.
The rotate action is equivalent to rotateZ:
source
transform: [{
  rotate:
    "-45deg"
}]
transform: [{
  rotate:
    "3.14rad"
}]
Action rotateX:
source
transform: [{
  rotateX:
    "-45deg"
}]
transform: [{
  rotateX:
    "3.14rad"
}]
Action rotateY:
source
transform: [{
  rotateY:
    "-45deg"
}]
transform: [{
  rotateY:
    "3.14rad"
}]
perspective: number
Adding a perspective to the block. Perspective is defined as a distance from the user to the center of the box along the Z axis. Specified in pixels. The higher is the value, the less perspective is pronounced.
source
transform: [{
  perspective: 500
},{
  rotateY: "-45deg"
}]
source
transform: [{
  perspective: 130
},{
  rotateY: "-45deg"
}]
scale,
scaleX,
scaleY: number
Scaling along the X and Y axes. Specified as a scaling factor. Can take negative value, which mirrors the object along the axis.
The scale acts along both axes at the same time, while scaleX and scaleY each along their own axis.
Scaling along both axes at the same time:
source
transform: [{
  scale: 0.5
}]
transform: [{
  scale: 1.5
}]
Scaling along axis X:
source
transform: [{
  scaleX: 0.5
}]
transform: [{
  scaleX: 1.5
}]
Scaling along axis Y:
source
transform: [{
  scaleY: 0.5
}]
transform: [{
  scaleY: 1.5
}]
Independent scaling along axes X and Y:
source
transform: [{
  scaleX: 0.8
},{
  scaleY: 1.3
}]
Mirroring of the block:
source
transform: [{
  scaleY: -1
}]
transform: [{
  scaleX: -0.8
}]
translateX,
translateY: number
Block position shift along axes X and Y. Specified in pixels. Can take negative value.
source
transform: [{
  translateY:
    -10
}]
transform: [{
  translateX:
    15
}]
skewX,
skewY: angle
Distorts the block at a certain angle along either X or Y axis.
Искажение блока по осям X и Y. Specified in degrees with "deg" suffix or radians with "rad" suffix. Can take negative value.
It is not possible to use skewX and skewY in the same block at the same time. If necessary, the matrix transformation should be used.
Along axis X:
source
transform: [{
  skewX:
    "-10deg"
}]
transform: [{
  skewX:
    "0.1rad"
}]
Along axis Y:
source
transform: [{
  skewY:
    "-10deg"
}]
transform: [{
  skewY:
    "0.1rad"
}]
matrix: [number, number, number, number, number, number]
Matrix transformation of the block. Essentially a combination of three different transformations: scale, skew amd transform.
Parameters are in the following order: [ scaleX, skewY, skewX, scaleY, translateX, translateY ]
Value for the parameters skewX and skewY is an angle given in radians as a number.
Block scale:
source
transform: [{
  matrix:
    [0.8,0,0,1.3,0,0]
}]
Block skew:
source
transform: [{
  matrix:
    [1,0.1,0.3,1,0,0]
}]
Block translate:
source
transform: [{
  matrix:
    [ 1,0,0,1,10,-15 ]
}]
All three transformations together:
source
transform: [{
  matrix: [
    0.8,0.1,0.3,
    1.3,10,-15
  ]
}]

Фоновые задачи
Механизм, позволяющий объявлять и выполнять фоновые задачи:
✦
Через промежуток времени (см. scheduleJobAfter)
✦
Как можно скорее (см. scheduleJobAsap)
✦
В определённое время (см. scheduleJobAt)
app.job('jobPath', callback)
Аргументы
jobPath*: string
Путь задачи.
callback: async function
Callback функция, которыя вызывается при вызове задачи.
Примеры
Пример объявления задачи.
app.job('jobPath', async (ctx, req) => {
  ctx.log('Job called', req)
})

cancelJob
Функция, отменяющая задачу.
Применение/сигнатура
import { cancelJob } from '@app/jobs'
cancelJob(ctx, jobId)
Аргументы
ctx*: app.Ctx
Контекст запроса. Используется для внутренней реализации, сохранения информации о пользователе, создавшем запись, а также позволяет выполнить запрос в нужной транзакции.
jobId*: string
Идентификатор задачи.

scheduleJobAfter
Позволяет асинхронно выполнить поставленную задачу через установленный промежуток времени.
Применение/сигнатура
import { scheduleJobAfter } from '@app/jobs'
await scheduleJobAfter(ctx, scheduleDuration, scheduleMeasure, jobUrl, dataObject)
Аргументы
ctx*: app.Ctx
Контекст запроса. Используется для внутренней реализации, сохранения информации о пользователе, создавшем запись, а также позволяет выполнить запрос в нужной транзакции.
scheduleDuration*: number
Период ожидания перед выполнением задачи.
scheduleMeasure*: string
Единица измерения периода ожидания.
Возможные значения: milliseconds, seconds, minutes, hours, days, weeks, months, quarters, years
jobUrl*: string
Ссылка на задачу, созданную с помощью app.job.
dataObject: object
Объект для передачи данных внутрь задачи.
Примеры
Пример использования функции:
import { scheduleJobAfter } from '@app/jobs'

app.screen('/', function (ctx, req) {
  return (
    <screen>
      <button onClick={ctx.router.apiCall('scheduleJob', {})} class={['primary', 'section']}>
        Schedule job
      </button>
    </screen>
  )
})

app.apiCall('scheduleJob', async (ctx, req) => {
  await scheduleJobAfter(ctx, 5, 'seconds', ctx.router.url('jobPath'), {
    key: '123',
  })
})

app.job('jobPath', async (ctx, params) => {
  ctx.log('Job called', params)
})

scheduleJobAsap
Позволяет асинхронно выполнить поставленную задачу как можно скорее.
Применение/сигнатура
import { scheduleJobAsap } from '@app/jobs'
scheduleJobAsap(ctx, jobUrl, dataObject)
Аргументы
ctx*: app.Ctx
Контекст запроса. Используется для внутренней реализации, сохранения информации о пользователе, создавшем запись, а также позволяет выполнить запрос в нужной транзакции.
jobUrl*: string
Ссылка на задачу, созданную с помощью app.job.
dataObject: object
Объект для передачи данных внутрь задачи.
Примеры
Пример использования функции:
import { scheduleJobAsap } from '@app/jobs'

app.screen('/', function (ctx, req) {
  return (
    <screen>
      <button onClick={ctx.router.apiCall('scheduleJob', {})} class={['primary', 'section']}>
        Schedule job
      </button>
    </screen>
  )
})

app.apiCall('scheduleJob', async (ctx, req) => {
  await scheduleJobAsap(ctx, ctx.router.url('jobPath'), {
    key: '123',
  })
})

app.job('jobPath', async (ctx, params) => {
  ctx.log('Job called asap', params)
})

scheduleJobAt
Позволяет асинхронно выполнить поставленную задачу в определённое время.
Применение/сигнатура
import { scheduleJobAt } from '@app/jobs'
scheduleJobAt(ctx, startAt, jobUrl, dataObject)
Аргументы
ctx*: app.Ctx
Контекст запроса. Используется для внутренней реализации, сохранения информации о пользователе, создавшем запись, а также позволяет выполнить запрос в нужной транзакции.
startAt*: Date
Date объект, указывающий на время выполнения задачи.
jobUrl*: string
Ссылка на задачу, созданную с помощью app.job.
dataObject: object
Объект для передачи данных внутрь задачи.
Примеры
Пример использования функции:
import { scheduleJobAt } from '@app/jobs'

app.screen('/', function (ctx, req) {
  return (
    <screen>
      <button onClick={ctx.router.apiCall('scheduleJob', {})} class={['primary', 'section']}>
        Schedule job
      </button>
    </screen>
  )
})

app.apiCall('scheduleJob', async (ctx, req) => {
  const date = new Date("2025-01-26");
  await scheduleJobAt(ctx, date, ctx.router.url('jobPath'), {
    key: '123',
  })
})

app.job('jobPath', async (ctx, params) => {
  ctx.log('Job called, today is 26.01.2025', params)
})

Адаптивная вёрстка
Функционал, позволяющий изменять контент страницы в зависимости от ширины экрана пользователя.
Клиент автоматически запрашивает новый экран от сервера, когда ширина экрана пользователя доходит до определённых значений. Сервер получает текущие значения экрана пользователя и, в зависимости от них, может изменить текущую вёрстку экрана.
Применение/сигнатура
import { responsiveState } from '@app/responsive'
responsiveState(ctx, { customBreakpoints })
Аргументы
ctx*: app.Ctx
Контекст запроса. Используется для внутренней реализации, сохранения информации о пользователе, создавшем запись, а также позволяет выполнить запрос в нужной транзакции.
customBreakpoints: object
Существует 15 возможных точек останова. Все они перечислены ниже, от менее к более специфичным и точным.
 xs 
 sm 
 md 
 lg 
 xl 
 xlDown 
 lgDown 
 mdDown 
 smDown 
 xsDown 
 xsOnly 
 smOnly 
 mdOnly 
 lgOnly 
 xlOnly 
Все брейкпоинты можно разделить на три типа:
✦
 xs ,  sm  и т.д. - точки, определяющие поведения для всех размеров экрана, что больше или равны значениям брейкпоинтов.
✦
 xsOnly ,  smOnly  и т.д. - точки, определяющие поведения для всех размеров экрана, что входят в диапазон от брейкпоинта до следующего не включительно [bp, next_bp).
✦
 xsDown ,  smDown  и т.д. - точки, определяющие поведения для всех размеров экрана, что меньше значений брейкпоинтов.
Использование адаптивной вёрстки
Главным критерием реализации является передача агрумента responsive в экран клиента.
import { responsiveState } from "@app/responsive"

app.screen("/", async (ctx, req) => {
  const responsive = responsiveState(ctx)
  return <screen responsive={ responsive }></screen>
})
Отрисовку блоков вы можете определять так.
import { responsiveState } from "@app/responsive"

app.screen("/", async (ctx, req) => {
  const responsive = responsiveState(ctx)

  return (
    <screen responsive={ responsive }>
      { responsive.md
        ? <text>Появится только для точек md или больше</text>
        : <text>Появится только для точек меньше md</text>
      }
    </screen>
  )
})
Стили блоков контролируются так.
import { responsiveState } from "@app/responsive"

app.screen("/", async (ctx, req) => {
  const responsive = responsiveState(ctx)

  return (
    <screen responsive={ responsive }>
      <text style={[
        { color: "black" },
        responsive.lg && { color: "red" }
      ]}>Текст становится красным для точек lg или больше</text>

      Эквивалентно

      <text style={responsive.boxStyle({
        color: "black",
        lg: { color: "red" }
      })}>Текст становится красным для точек lg или больше</text>
    </screen>
  )
})
У объекта  responsive  есть функции-конструкторы стилей для каждого типа блоков:  responsive.textStyle() ,  responsive.buttonStyle()  и т.д.
Эти конструкторы используются для верных подсказок в редакторе кода, т.к. в зависимости от блока, вы можете применять к нему определённые стили.
Модуль @app/ui/styles
Таким образом вы можете использовать функции-конструкторы вне блока screen. Однако вы всё же обязаны использовать методы объекта responsive.
import { responsiveState } from "@app/responsive"
import * as s from '@app/ui/styles'

const commonStyle = s.textStyle({
  color: "black",
  lg: { color: "red" },
})

app.screen("/", async (ctx, req) => {
  const responsive = responsiveState(ctx)

  return (
    <screen responsive={ responsive }>
      <text style={responsive.textStyle(commonStyle, {
        fontSize: 16
      })}>Текст становится красным для точек lg или больше</text>
    </screen>
  )
})

Механизм обновления экранов через сокеты
Позволяет обновлять содержимое экрана, используя сокеты.
Применение/сигнатура
import { genSocketId, updateSocket } from '@app/socket'
genSocketId(socketName), updateSocket(socketName)
Аргументы
socketName*: string
Имя сокета. Используется для генерации идентификатора сокета и обновления содержимого экрана.
Критерием для использования обновления экранов через сокеты является передача в экран параметра  socketIds=[] 
app.screen("/", async (ctx, req) => {
  return <screen socketIds=socketIdList></screen>
})
Функция  updateSocket()  отличается от  refresh()  тем, что механизм обновления экранов через сокеты не является действием и не требует никакого вмешательства пользователя, работая автономно.
Примеры
Самый простой пример:
import { genSocketId, updateSocket } from '@app/socket'

app.screen('/', async (ctx, req) => {
  const socketId = await genSocketId('my-socket')

  return (
    <screen socketIds={[socketId]}>
      <section>
        <text style={{ fontSize: 30, fontWeight: 'bold', textAlign: 'center' }}>
          {Math.round(Math.random() * 1000)}
        </text>
        <button
          class={['section', 'primary']}
          onClick={updateSocketRoute.apiCall()}
        >
          Refresh via socketUpdate
        </button>
      </section>
    </screen>
  )
})

const updateSocketRoute = app.apiCall('/', async (ctx) => {
  return await updateSocket('my-socket')
})
Вы можете обновлять экран, в зависимости от полученных через WebHook данных:
import { genSocketId, updateSocket } from '@app/socket'
import { Heap } from "@app/heap"

const socketName = "webhookSocket"
const BgColor = Heap.Table("BgColor", {
  color: Heap.String()
})

const getColor = async () => {
  return (await BgColor.findAll(ctx, {
    order: { createdAt: "desc" },
    limit: 1
  }))[0]
}

app.post("/getNewColor", async (ctx, req) => {
  const newColor = req.body.bgColor
  const color = await getColor()

  if (color) {
    await BgColor.update(ctx, {
      id: color.id,
      color: newColor
    })
  } else {
    await BgColor.create(ctx, {
      color: newColor
    })
  }

  await updateSocket(socketName)
})

app.screen('/', async (ctx, req) => {
  const socketId = await genSocketId(socketName)
  const color = await getColor()

  return (
    <screen socketIds={[socketId]}>
      <box style={{ backgroundColor: color }}>
        <text>Фоновый цвет этого блока обновляется по вебхуку в реальном времени</text>
      </box>
    </screen>
  )
})

