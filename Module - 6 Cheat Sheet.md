Module - 6 Cheat Sheet 


        6.1.5

            However, making the request only gets us halfway. We still need to read the data that was returned in the response. You could try console logging whatever the fetch() function returns.

            In homepage.js, update the getUserRepos() function to include the following code:

            var response = fetch("https://api.github.com/users/octocat/repos");
            console.log(response);
            If you check the console, you'll see that the fetch() function returned some weird Promise object, as the following image shows:

            The word "Promise" appears in the DevTools console

            Promises are newer additions to JavaScript that act like more advanced callback functions. We'll dissect Promises in greater detail down the road. For now, note that Promises have a method called then(). This method is called when the Promise has been fulfilled.

            Update the code inside getUserRepos() to look like this:

            fetch("https://api.github.com/users/octocat/repos").then(function(response) {
            console.log("inside", response);
            });

            console.log("outside");
            Refresh the browser and watch the console. You'll notice that the second console.log() statement prints first: this is an example of asynchronous behavior.

            What's happening here? Imagine if the GitHub API was running particularly slow and a response didn't come back for several seconds. You wouldn't want the rest of your code to be blocked until then. To avoid that, JavaScript will essentially set aside the fetch request and continue implementing the rest of your code, then come back and run the fetch callback when the response is ready—thus working asynchronously.

            REWIND
            You've come across asynchronous methods in JavaScript before. Event callbacks and timers are both asynchronous in that they don't block the code underneath from running. For example:

            document.querySelector("#submit-btn").addEventListener("click", function(event) {
            // only called when user clicks the button
            console.log("hello 1");
            });

            setTimeout(function() {
            // called after 5 seconds, regardless if button was clicked
            console.log("hello 2");
            }, 5000);

            // called immediately on page load
            console.log("hello 3");
            In this case, the third console.log() will print first because it's not dependent on any asynchronous actions.

            This kind of asynchronous communication with a server is often referred to as AJAX, which stands for Asynchronous JavaScript and XML. The XML in this term refers to an old-fashioned way of formatting data. XML has been largely replaced by JSON, but the name has persisted.

            Now that we've received a response, let's see if we can access the JSON data returned from this HTTP/AJAX request. Take a closer look at the response object that we logged. In the console, expand the object to display the following image:

            The expanded response object and all of its properties are displayed in the DevTools console

            Studying this object should tell us a couple of things. First, the url property confirms where the response came from. Second, because the status property has a value of 200 (which means success), we know that the request went through successfully. But where's the data? Where's the array of repos?

            Before you can use the data in your code, you need to format the response. Update your fetch() logic to look like this:

            fetch("https://api.github.com/users/octocat/repos").then(function(response) {
            response.json().then(function(data) {
                console.log(data);
            });
            });
            Refresh the browser, and you'll now see the array logged in the console.

            Notice how the response object in your fetch() logic has a method called json(). This method formats the response as JSON. But sometimes a resource may return non-JSON data, and in those cases, a different method, like text(), would be used.

            The json() method returns another Promise, hence the extra then() method, whose callback function captures the actual data. This nested then() syntax might confuse you at first, but the more API calls you make, the more comfortable you'll start to feel with it.

            LEGACY LORE
            Developers can talk to server-side APIs by means other than the Fetch API. For example, the XMLHttpRequest object has been a part of JavaScript for a long time. However, its methods for handling responses aren't very intuitive.

            Libraries like jQuery have sought to make using XMLHttpRequest easier by wrapping up its complexities in a single method: $.ajax(). jQuery even used a Promise-like syntax by chaining a done() method to the end. Consider the following code example:

            $.ajax("https://api.github.com/users/octocat/repos").done(function(data) {
            console.log(data);
            });
            Of course, the JavaScript engine has since caught up to jQuery, and many developers prefer to use the built-in Fetch API now instead of loading up a third-party library just for its $.ajax() shorthand.

            End of text box.
            In any case, we now have the data we want, but remember that we've hardcoded the request to Octocat's account. Up next, we'll make the getUserRepos() function dynamic.

    6.1.6

            Testing a server-side API with hardcoded values (like the Octocat username) can help us verify that the API will work at all before we get carried away with too much other logic. Now that we know we can get the data from GitHub, we can update the getUserRepos() function to request any user's repositories.

            CONNECT THE DOTS
            You can liken an API endpoint to a function in the sense that the same base URL can return different data depending on the argument(s) given. Consider the following examples:

            getUserRepos("octocat"); // https://api.github.com/users/octocat/repos
            getUserRepos("microsoft"); // https://api.github.com/users/microsoft/repos
            Or more generically:

            getUserRepos(user); // https://api.github.com/users/<user>/repos
            End of text box.
            In homepage.js, restructure the getUserRepos() function as follows:

            var getUserRepos = function(user) {
            // format the github api url
            var apiUrl = "https://api.github.com/users/" + user + "/repos";

            // make a request to the url
            fetch(apiUrl).then(function(response) {
                response.json().then(function(data) {
                console.log(data);
                });
            });
            };
            Note how we've added a parameter to the getUserRepos() function and inserted the parameter into the GitHub API URL. We then use the newly formatted URL in the subsequent fetch() request.

            Test out the new logic by calling getUserRepos() with a few different usernames, like getUserRepos("microsoft") or getUserRepos("facebook"). You can even try your own username! Check the console each time to see if the returned array changes.

            If everything looks good, go ahead and save your progress with Git. There's still more work to be done before you can mark this user repos feature complete, and in the next lesson you'll continue where you left off.

    6.1.7 : REFLECTION 

        Great job working through the ups and downs of your first server-side API! This lesson covered some pretty dense topics. In particular, you accomplished the following:

        Learned that a server-side API is an interface for accessing another website's data.

        Familiarized yourself with how to read server-side API documentation and why they often give examples using curl.

        Made requests to a server-side API with the browser's Fetch API.

        Used the DevTools Network tab to glean additional information about requests being made.

        Now that you have access to the repository data, the next lesson will walk you through combining it with DOM methods to display this information on the UI.

        Lesson Code Snapshot
        Download the code snapshot (Links to an external site.) for this lesson to compare with your own code. It’s okay if your code isn’t exactly the same—use this code snapshot as a starting point for the next lesson.


6.2.1 : intro 
    Nice work! You’ve successfully used the Fetch API to make an HTTP request to the GitHub API. And not only did you receive a response, but you also learned how to format the response to JSON, as shown in the following image:

Response data from GitHub API request logged to DevTools Console

While Amiko is pleased by the progress so far, she’s still looking forward to two important updates. First, we need to allow users to search for any GitHub account they want. Currently, we have the request hardcoded to a specific URL, meaning that the HTTP request will always search for the same GitHub account. So we’ll update this code to something dynamic.

Second, we need to display the data to the page. At the moment, we can only see the response from a request in the Chrome DevTools console, meaning that no real user will ever see that data! We’ll get this updated as well, so that users can actually see repository names and how many issues they have.

So how will we make those updates? Using HTML and browser events, we’ll capture dynamic user input to search for different GitHub accounts. Then we’ll iterate the response data through each repository’s data, and we’ll display the important parts of it to the page.

For this lesson, we'll set the following goals:

Collect user input to form HTTP requests.

Use an HTTP request's response to display data to the user.

Handle errors that may occur when working with server-side APIs.

You just might find yourself using these skills over and over throughout your web development career. Capturing a user interaction to create an HTTP request and display the response data is a common practice in the field. Luckily, you've already created the HTTP request; now you just need to capture the user input and display the response on the page!

6.2.2: preview

    By the end of this lesson, the application will look something like this image:

    Git It Done app shown with working search functionality

    To replicate this image, we'll have to add some HTML and create the form. Once we've captured the user input and used it to search for a GitHub account, we'll dynamically create more HTML and inject the list of repository data to create the righthand column.

    Take a minute and try to organize the steps we'll take to get there:

        6.2.3
            Add the search form.

            To begin, let's review the GitHub issue associated with this feature one more time:

            GitHub issue with a description of the work that needs to be completed

            It may seem like we haven’t accomplished much yet, because we haven’t actually touched any of the bullet points listed for this feature. But in reality, we’ve made a lot of progress. The bullet points represent actionable items for the user to experience. However, they don’t reflect all of the work that goes into the end goal. For example, we've already studied the GitHub API documentation and taken care of the HTTP request using fetch(). Now we just need to tie that functionality to the page!

            PRO TIP
            Now that we know how to use the Fetch API to create HTTP requests and handle their responses, let's tackle the first bullet point in this GitHub issue: "Users can enter a GitHub username and see a list of all repos from that account."

            Build the Layout
            To allow users to enter any GitHub username, we’ll need to add a form to to index.html. Amiko wants the form to look something like this image:

            User search form on lefthand side of Git it Done app

            The form doesn't need to take up much space on the page, so we'll create a two-column layout: we can place the form on the lefthand side, and we'll display the repositories to the right. Before we think about creating the form itself, let's create the two columns.

            Update the <main> element so that it looks like this:

            Start of code snippet<main class="flex-row justify-space-between">
            <div class="col-12 col-md-4"></div>

            <div class="col-12 col-md-8"></div>
            </main>End of code snippet
            We won’t see a difference yet if we save index.html and refresh the page, but that’s okay. At this point, we’re just creating a responsive layout for the content to be placed into.

            Did you notice that these classes look a bit like Bootstrap? Actually, Amiko created a custom style sheet that follows a lot of the same naming conventions. Take a moment to look over the CSS rules defined in style.css. You'll see that a lot of the classes leverage different uses of the flex property.

            PAUSE
            In a Bootstrap layout, what do the different col-* classes do for us?

            Answer: They allow us to adjust the flexbox columns at different screen widths.




            Now that we've created the main layout, let's create the search form. We'll leverage that style sheet Amiko created.

            Add the following code into the left column (the <div class="col-12 col-md-4"> element):

            Start of code snippet<div class="card">
            <h3 class="card-header text-uppercase">Search By User</h3>
            <form id="user-form" class="card-body">
                <label class="form-label" for="username">Username</label>
                <input name="username" id="username" type="text" autofocus="true" class="form-input" />
                <button type="submit" class="btn">Get User</button>
            </form>
            </div>End of code snippet
            Now if we save index.html and refresh the browser, we'll see something like this image:

            Search By User form element added to application

            Great! Now we have a nice card component with a form inside it. Before moving on, study this HTML a bit more. We'll need some of this information if we want to capture the submitted form input.

    6.2.4  : Handle Form Submisson

           Now that we have a form for users to use, let's make it functional. Think back to when we've done this before—it usually involved the following actions in JavaScript:

            Adding an event listener to the <form> element to execute a function on submission

            Capturing the form's input data to use elsewhere in the app

            Luckily, we'll follow the same basic process for this form. The only difference is what we're doing with the input data. Let's get started!

            To begin, let's navigate to homepage.js. Create two new variables to store a reference to the <form> element with an id of user-form and to the <input> element with an id of username:

            var userFormEl = document.querySelector("#user-form");
            var nameInputEl = document.querySelector("#username");
            Now that we have variables for the two form elements, let's create a function called formSubmitHandler to be executed upon a form submission browser event.

            Add the following code to homepage.js:

            var formSubmitHandler = function(event) {
            event.preventDefault();
            console.log(event);
            };
            Lastly, let's add the submit event listener to the userFormEl. Because we don't need to execute the getUserRepos() function at the bottom of the homepage.js file, remove that line of code and replace it with this:

            userFormEl.addEventListener("submit", formSubmitHandler);
            Before we add any more functionality to formSubmitHandler(), let's make sure the event listener is working correctly. We should always test functionality like this before we write too much code, because if there's an issue, we'll be able to spot it faster and fix it before moving on.

            So to test the event listener, save homepage.js, refresh the page in the browser, and try to search for a user. This won't do much, but we can see if it works by opening the Chrome DevTools console. We should see something like this image after we submit the form:

            Form submit browser event in the DevTools Console

            Now that we know the form submit functionality is working, let's make it actually do something!



            PAUSE
            What does event.preventDefault() do?

            Answer It stops the browser from performing the default action the event wants it to do. In the case of submitting a form, it prevents the browser from sending the form's input data to a URL, as we'll handle what happens with the form input data ourselves in JavaScript.



            Let's update the formSubmitHandler() function to get the value of the form <input> element and send it over to getUserRepos(). Put this code in the function underneath the event.preventDefault(); line:

            // get value from input element
            var username = nameInputEl.value.trim();

            if (username) {
            getUserRepos(username);
            nameInputEl.value = "";
            } else {
            alert("Please enter a GitHub username");
            }
            IMPORTANT
            Make sure the console.log(data) statement is still in the getUserRepos() function from the previous lesson!

            Okay, you should be all set up. Save homepage.js, refresh the page, and try searching for a GitHub username, like your own username or "facebook". The result in the console should look like the following image:

            Response JSON data from search form submission.

            Great! Now we can use fetch() to search for any GitHub user whenever we fill out the form. Let's quickly review the code we just placed into formSubmitHandler(), and then we'll get into displaying this data in the page.

            When we submit the form, we get the value from the <input> element via the nameInputEl DOM variable and store the value in its own variable called username. Note the .trim() at the end: this piece is useful if we accidentally leave a leading or trailing space in the <input> element, such as " octocat" or "octocat ".

            Then we check that there's a value in that username variable. We wouldn't want to make an HTTP request without a username, if we accidentally left the <input> field blank! If there is in fact a value to username, we pass that data to getUserRepos() as an argument. Then, to clear the form, we clear out the <input> element's value.

            At this point, we can use a form to search for a GitHub user's repositories, and we're getting a list of those repositories in the response. Next we need to display them on the page.

    6.2.5 : Display Response Data on Page.

            Remember, web developers often have to capture a user interaction, form an HTTP request from that interaction, and display the response data on the page. We’ve done two of those three tasks so far: we’ve captured a user interaction and used it to form an HTTP request. Now we need to display the important parts of the response data on the page.

            But first, you should know that you may not need all of the response data you receive. A lot of HTTP requests to server-side APIs receive responses with way more JSON data than necessary. The amount of data you need from an HTTP response will vary from project to project, based on the API you make a request to and the type of app you’re building, but in this case you just need to know the username, the repository name, and how many issues the repository has.

            Study the Response Data
            Numerous tools can help us find the data we need, but the easiest way to study the response of a simple API request like this one is to use the browser! Let's study the response data so that we can identify the properties we want to use. Enter the following into the browser's URL bar:

            https://api.github.com/users/octocat/repos
            When you navigate to that URL, you'll see the same response as the fetch() response, like this image shows:

            GitHub API request's response JSON data viewed in Chrome

            Keep in mind, the response itself is an array of objects, with each object holding one repository's data. The nice thing about working with data structured as JSON is that the property names are the same for each repository, so we only need to study this first one.

            PRO TIP
            Let's identify how the details we need are labeled in the response data. We're looking for the name property (for the repository name), the open_issues_count property (for the number of issues), and the login property in the nested owner object (for the repository owner).

            We've identified the data now, so we can work on getting it to the page! This'll involve a for loop and a fair amount of DOM functionality, so let's create a new function for it.

            Create a Function to Display Repos
            Let's start by creating a new function called displayRepos(). This function will accept both the array of repository data and the term we searched for as parameters. Add the following code to homepage.js:

            var displayRepos = function(repos, searchTerm) {
            console.log(repos);
            console.log(searchTerm);
            };
            Now that we've created the function, let's set it up so that when the response data is converted to JSON, it will be sent from getUserRepos() to displayRepos(). Edit the fetch() callback code in the getUserRepos() function to look like this:

            response.json().then(function(data) {
            displayRepos(data, user);
            });
            If we save homepage.js, refresh the page, and search for a GitHub user, we should see the result logged to the console from the console.log() statements in displayRepos().

            Create an HTML Container for the Content
            With the displayRepos() function now receiving data, we can update the function to start displaying data. But before that, try to answer one question. Where is this data getting displayed on the page? Take another look at the desired end result:

            Git it Done app with search form on the left and repositories listed on the right

            Okay, so it looks like the data will get displayed on the right column of the page. We already have the column set up, but we still need something unique in the HTML that we can select and write the data to. Let's update the HTML file so it has just that.

            In index.html, update the right column to look like this:

            Start of code snippet<div class="col-12 col-md-8">
            <h2 class="subtitle">Showing Repositories for: <span id="repo-search-term"></span></h2>
            <div id="repos-container" class="list-group"></div>
            </div>End of code snippet
            Note that we've created two areas we'll write data to: We created a <span> element to write the search term to so that users know whose repositories they're looking at. We also created an empty <div> element that we'll write all of the repository data to.

            But before we use JavaScript to write data into these two areas, let's create two more variables to reference these DOM elements at the top of homepage.js:

            var repoContainerEl = document.querySelector("#repos-container");
            var repoSearchTerm = document.querySelector("#repo-search-term");
            Now we can start displaying the GitHub API response data on the page!

            Display Repository Data
            When working with an app that displays data based on user input, we should always be sure to clear out the old content before displaying new content.

            To do that, let's show the search term on the page by adding the following code to displayRepos():

            // clear old content
            repoContainerEl.textContent = "";
            repoSearchTerm.textContent = searchTerm;
            As always, we should test the functions as we write them, not only to catch any bugs early on but also to affirm that we're doing something right! As a test, let's save homepage.js and try searching for a user. The page should display the name after a successful search response, like this image shows:

            GitHub username that was searched for displayed the page

            Now let's move on to the important part—displaying repository data to the page.

            Add the following code to displayRepos():

            // loop over repos
            for (var i = 0; i < repos.length; i++) {
            // format repo name
            var repoName = repos[i].owner.login + "/" + repos[i].name;

            // create a container for each repo
            var repoEl = document.createElement("div");
            repoEl.classList = "list-item flex-row justify-space-between align-center";

            // create a span element to hold repository name
            var titleEl = document.createElement("span");
            titleEl.textContent = repoName;

            // append to container
            repoEl.appendChild(titleEl);

            // append container to the dom
            repoContainerEl.appendChild(repoEl);
            }
            IMPORTANT
            Don't forget that the CSS classes come from Amiko's style sheet. Amiko has also included the font icon library called Font Awesome in the app!

            Again, save homepage.js and try searching for a user. We should see something like this image:

            Repositories from API response displayed on the page

            How does this work? In the for loop, we're taking each repository (repos[i]) and writing some of its data to the page. First we format the appearance of the name and repository name. Next we create and style a <div> element. Then we create a <span> to hold the formatted repository name. We add that to the <div> and add the entire <div> to the container we created earlier.

            We've done things like this in the past, except this time the data is coming from GitHub!

            Display Repo Issues on the Page
            Now that we know the code works the way we want it to, we just need to write one more bit of information to the page: the number of issues for each repository.

            Amiko wants to see not just the number of issues but a little icon next to the number of issues to help identify which repositories need help at the moment. We'll have to use an if statement for this, so let's update the code and implement it!

            Update the for loop in displayRepos() to have this code right above the repoContainerEl.appendChild() method:

            // create a status element
            var statusEl = document.createElement("span");
            statusEl.classList = "flex-row align-center";

            // check if current repo has issues or not
            if (repos[i].open_issues_count > 0) {
            statusEl.innerHTML =
                "<i class='fas fa-times status-icon icon-danger'></i>" + repos[i].open_issues_count + " issue(s)";
            } else {
            statusEl.innerHTML = "<i class='fas fa-check-square status-icon icon-success'></i>";
            }

            // append to container
            repoEl.appendChild(statusEl);
            Now if we try to search for a user, we'll get back not only the list of repositories but also the number of issues for each one, like this image shows:

            Repositories displayed on the page with their names and how many issues they have, and a check mark or X next to the number of issues

            We use an if statement to check how many issues the repository has. If the number is greater than zero, then we'll display the number of issues and add a red X icon next to it. If there are no issues, we'll display a blue check mark instead.

            REWIND
            The font icons used here come from a service called Font Awesome. They offer a large selection of free font icons, including icons for social networks, like the GitHub logo at the top of the page!

            Great job on completing this type of functionality! The lessons you've learned from working on this app will be useful in all of your web development adventures—and now you can also add server-side APIs to your skill set.

            Knowing how to work with server-side APIs opens up many possibilities for what you can build. Everything you've worked on until now has used data you created yourself. Now you can request data from other sources and use that instead. This doesn't come without potential hurdles, however, and we'll learn how to handle those next.

6.2.6: Add error Handling

            Errors are a potential hurdle we can face when working with server-side APIs. Because we're communicating over the internet with another computer, many factors could cause failed responses, including a bad network connection or changes to the API. Let's review possible solutions to deal with these issues.

            User Not Found Error
            A failed response could occur, for example, if we try searching for a user that doesn't exist.

            To test this possible error, type in any string of characters and numbers and press Get User. The page will display the name but no repositories. Let's check the console and see what happened:

            404 error printed to console stating the user could not be found

            It's the dreaded 404 error! You've probably seen a 404 error before, most likely when you accidentally entered the wrong address in a URL bar or searched for something that didn't exist, like this image shows:

            GitHub's 404 error page when a wrong URL is visited in the browser

            Now this type of error is happening in your own app! Fear not—the HTTP request 404 status error isn't really a problem at all. As a matter of fact, it's trying to help by explaining that GitHub's API understands your request but that you're looking for something that doesn't exist.

            An HTTP request, even when unsuccessful, will always respond with a status code that indicates how the request went. We’ve now seen two possible HTTP request status codes in this application: a 200 status and a 404 status. Let’s talk about what these status codes mean.

            A status code in the 200s means that the HTTP request was successful. Any status code in the 400s indicates that the server received the HTTP request but there's an issue with how we made the request, such as missing information—just as we saw with our search for a user that doesn’t exist. We'll also come across status codes in the 500s, which indicate an error with the server we're making a request to or the lack of an internet connection to make the request.

            DEEP DIVE
            Now, we really can't stop a 404 error unless we prevent users from looking up GitHub accounts that don't exist. That's not realistic, so we can only control how the app reacts to a 404 message.

            To handle this error, let's update the getUserRepos() function's fetch() method to look like this:

            fetch(apiUrl).then(function(response) {
            if (response.ok) {
                response.json().then(function(data) {
                displayRepos(data, user);
                });
            } else {
                alert("Error: GitHub User Not Found");
            }
            });
            Now if we try to search for a user that doesn't exist, the HTTP request will still be made; but instead of an attempt to display the repository data, an alert like this image will appear:

            User not found alert from a failed username search

            Now it's up to GitHub's API to tell us they couldn't find that user and send a response back. We can check if it was a successful request by using the ok property that's bundled in the response object from fetch(). When the HTTP request status code is something in the 200s, the ok property will be true.

            If the ok property is false, we know that something is wrong with the HTTP request, so we set a custom alert message to let the user know that their search was unsuccessful.

            Now that we've handled that potential error, let's take a look at another potential issue.

            User Has No Repositories
            If a GitHub user exists but doesn't have any repos, GitHub will not return a 404—because the user was found but has no repositories to display.

            In this situation, the fetch() request's response data will still have information about that name, so when we run it through response.json() it will still work but will return an empty array. If we just leave the page blank, the user will have no idea if the search function has worked as intended. We should let them know immediately that everything's fine but that no results appeared for that name.

            With that in mind, let's update the displayRepos() function to check for an empty array and let the user know if there's nothing to display.

            At the very top of displayRepos(), add the following code:

            // check if api returned any repos
            if (repos.length === 0) {
            repoContainerEl.textContent = "No repositories found.";
            return;
            }
            Now if someone searches for a name without any repositories, we can let the user know that there's nothing to display!

            We've handled a couple of potential errors at this point. However, any app that transfers data over the internet—like ours—might experience bugs if the internet goes down or the GitHub server crashes.

            Catch Network Errors
            While many communities and organizations now have stable, widespread internet access, network connectivity can still cause problems. Wireless networks can be overloaded; we may find ourselves out of range; and GitHub may even have issues with their API servers' connection. We can't control any of these factors, but we have to ensure that the app responds properly if such an error occurs, to avoid turning off users.

            Luckily, a feature built into the Fetch API can help us with connectivity issues. Let's add the following method to the fetch() request so that the entire function now looks like this:

            fetch(apiUrl)
            .then(function(response) {
                // request was successful
                if (response.ok) {
                response.json().then(function(data) {
                    displayRepos(data, user);
                });
                } else {
                alert('Error: GitHub User Not Found');
                }
            })
            .catch(function(error) {
                // Notice this `.catch()` getting chained onto the end of the `.then()` method
                alert("Unable to connect to GitHub");
            });
            Notice that last part, the .catch() method? That's the Fetch API's way of handling network errors. When we use fetch() to create a request, the request might go one of two ways: the request may find its destination URL and attempt to get the data in question, which would get returned into the .then() method; or if the request fails, that error will be sent to the .catch() method.

            Requests can fail in a few different ways. To force a failed request for testing purposes, let's turn off the computer's WiFi and disconnect it from the internet. If we do that and try to search for a GitHub user, we'll see an alert stating that we can't connect to GitHub. This is what the console says:

            Error message in console stating an HTTP request was made with no internet

            The message explains that there's no internet for us to even make a request. A message like this is helpful, though. If we don't catch the error and display something to the user, they may think the application is broken. Now they know that there's an issue connecting to GitHub and that the issue might be out of our hands.

            PRO TIP
            Great job! You've covered a lot of ground in this lesson, but you'll get plenty of practice capturing input, displaying data, and handling errors often in your career.

            For a recap on how to use the Fetch API, watch the following video:


            Let's wrap up the first GitHub issue and merge the code into the develop branch!