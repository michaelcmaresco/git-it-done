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

    6.2.7 Save work to github

    6.2.8 reflection

    6.3.1
            Congratulations on finishing the first feature of the app: searching for user or company repositories! This image shows what the app looks like now:

            Git it Done app with a username form on the left and a list of repos on the right

            Amiko has already started using the app to search for repositories with open issues across Microsoft, Facebook, and other big tech companies. While we can currently see the number of open issues in each repository, ultimately we want to also see an actual list of those open issues.

            Let's review the next GitHub issue that covers this topic, shown in the following image:

            GitHub issue outlining the ability to see a list of all open issues in a repository

            According to the issue, users should be able to click on a repository name to see all of its issues on a second webpage. We may be tempted to start with the clicking logic, but we'll save that for the next step. First and foremost, let's set up this new, second page correctly.

            We'll build off of past experience as we set up the new page. For one thing, we'll use fetch() to retrieve data from a different GitHub API endpoint, much like we've done already. We'll also repeat some familiar DOM methods to convert issue data into DOM elements.

            In this lesson, we'll tackle these main objectives:

            Define request and response headers

            Use additional GitHub API endpoints

            Just as you'll have to familiarize yourself with more GitHub API documentation at this step, developers often have to learn new APIs on the fly, per client or company requests. Remember, skimming documentation is a skill in itself!

    6.3.2
            Even though we already have a starter single-repo.html file, we'll need to add some actual functionality to it. It's not all about looks! The following image represents our end goal for this lesson:

            New Git it Done page listing open issues for a single repository

            Note that this mockup doesn't include the name of the repository in the header. Remember, it's often easier to set up new logic (especially server-side API logic) with hardcoded data, like the repository's name in this case. So we won't worry about displaying the repository name just yet. For now we'll hardcode values as we get the logic up and running.

            But first we'll need to define a game plan. Try reordering these steps yourself:



            Later, once we've put the logic in place, we'll make the code dynamic, to accommodate any repository the user might want to see.
    6.3.3
            To begin building the page, we'll create a new JS file to hook up to the single-repo.html file. Then we can add functionality to a page that's light on content.

            To get started, create a new feature branch called feature/list-issues. Once on the new feature branch, navigate to single-repo.html and open it in your browser.

            PAUSE
            Where can we append the API data once we have it?

            Answer
            Now open single-repo.html in VS Code and create a <div> with an ID of issues-container and a class of list-group inside the <main> element in your HTML. Consider the following example code:

            Start of code snippet<main>
            <div id="issues-container" class="list-group">

            </div>
            </main>End of code snippet
            Next, create a new JavaScript file called single.js. After creating this file, link it in your HTML via a <script> element just above the closing </body> tag, like so:

            Start of code snippet  <script src="./assets/js/single.js"></script>
            </body>End of code snippet
            Now that you've linked the JS file with the HTML, navigate back to single-repo.html in your browser and open Chrome DevTools. Navigate to the Network tab and refresh the page. You should see the JS file as a requested resource, like in the following image:

            DevTools Network tab lists several requested resources, including the single.js file

            The Network tab provides developers with valuable insights into what size files are, when they're being loaded, when requests are being made and received, and anything else pertaining to network traffic.

            Great job! Now you're set up to begin adding functionality via code in the single.js file.

    6.3.4

            The JavaScript we write in single.js will do most of the work on the single-repo.html page, so let's add that functionality. On this page, we'll make a request to the GitHub API with a repository's name, and we'll display all the issues associated with that repository.

            IMPORTANT
            A repository name includes the creator's username (e.g., octocat or lernantino) because many projects across GitHub might have the same name (e.g., octocat/my-first-app and lernantino/my-first-app).

            Start by opening single.js in VS Code and creating a getRepoIssues() function that will take in a repo name as a parameter. For basic testing, console.log() the passed repo name as such:

            var getRepoIssues = function(repo) {
            console.log(repo);
            };

            getRepoIssues("facebook/react");
            Navigate back to the single-repo.html page in the browser and reload, opening Chrome DevTools to check if the function worked. Great! Let's add in an actual request.

            The API endpoint we've been using (/users/<user>/repos) doesn't provide enough information for us to display the individual issues for a single repository, so let's look at the GitHub documentation for issues (Links to an external site.) and get the correct endpoint. Note the section pictured in the following image:

            "GET /repos/:owner/:repo/issues" highlighted as an endpoint in GitHub API documentation

            Using the endpoint listed in the documentation, we can format the URL as https://api.github.com/repos/<repo>/issues, where <repo> encompasses the username and repo name. Note that the documentation says this endpoint will return both issues and pull requests. This is fine; pull requests can still be contributed to!

            The documentation might not make this apparent, but we also have the option to append ?direction=asc to the end of the query URL to specify the sort order. By default, GitHub returns request results in descending order by their created date, meaning that we see newer issues first. The ?direction=asc option reverses order to return older issues first.

            We'll get into these ? options in more detail in the next lesson. For now, let's focus on using the Fetch API to create an HTTP request to this endpoint. First we'll create a variable to hold the query. So, inside the getRepoIssues() function, add the following line:

            var apiUrl = "https://api.github.com/repos/" + repo + "/issues?direction=asc";
            Now let's build out a basic HTTP request to hit this endpoint and check the information returned in the response. Create a request with fetch(), and pass in the apiUrl variable we created. The getRepoIssues() function should resemble the following code block:

            var apiUrl = "https://api.github.com/repos/" + repo + "/issues?direction=asc";

            fetch(apiUrl);
            To view the resulting request, save and navigate back to single-repo.html in your browser, making sure to have the Network tab open in Chrome DevTools. Refresh the page, and you should see the HTTP request being made, per the following image:

            DevTools Network tab lists "/issues?direction=asc" as a fetched resource

            Remember that fetch() is asynchronous. We'll have to use its Promise-based syntax to actually access the data contained in the response.

            Update the fetch() request to receive and handle the server's response:

            fetch(apiUrl).then(function(response) {
            // request was successful
            if (response.ok) {
                response.json().then(function(data) {
                console.log(data);
                });
            }
            else {
                alert("There was a problem with your request!");
            }
            });
            Notice that we are checking the value of response.ok, which indicates a successful request. Inspect the logged data parameter in the console, noting some of the properties on the issue objects, as seen in the following image:

            An array of objects displayed in the DevTools console

            You'll need to use those properties to create DOM elements in the next step!
    6.3.5
            Turning GitHub issue data into DOM elements will look similar to what you did with repositories in the displayRepos() function, so let's create a new function for the issues.

            In single.js, add the following function that accepts a parameter called issues:

            var displayIssues = function(issues) {

            };
            You can call this function after a successful HTTP request. Update the logic in getRepoIssues() to call displayIssues(data) instead of console logging the response:

            fetch(apiUrl).then(function(response) {
            // request was successful
            if (response.ok) {
                response.json().then(function(data) {
                // pass response data to dom function
                displayIssues(data);
                });
            }
            else {
                alert("There was a problem with your request!");
            }
            });
            In the displayIssues() function, loop over the response data and create an <a> element for each issue, as shown here:

            for (var i = 0; i < issues.length; i++) {
            // create a link element to take users to the issue on github
            var issueEl = document.createElement("a");
            issueEl.classList = "list-item flex-row justify-space-between align-center";
            issueEl.setAttribute("href", issues[i].html_url);
            issueEl.setAttribute("target", "_blank");
            }
            Again, this looks like what we did with repo data in homepage.js. The biggest difference is the data we're working with. Issue objects have an html_url property, which links to the full issue on GitHub. We also added a target="_blank" attribute to each <a> element, to open the link in a new tab instead of replacing the current webpage.

            PRO TIP
            So far, we've only created an empty <a> element called issueEl for each issue. We still need to add content to these elements. We'll want to display each issue's name as well as its type—either an actual issue or a pull request.

            Underneath the issueEl element logic, add the following block of code:

            // create span to hold issue title
            var titleEl = document.createElement("span");
            titleEl.textContent = issues[i].title;

            // append to container
            issueEl.appendChild(titleEl);

            // create a type element
            var typeEl = document.createElement("span");

            // check if issue is an actual issue or a pull request
            if (issues[i].pull_request) {
            typeEl.textContent = "(Pull request)";
            } else {
            typeEl.textContent = "(Issue)";
            }

            // append to container
            issueEl.appendChild(typeEl);
            This code will create an <a> element that will look like the following image:

            A single issue listed in Git it Done with the title on the left and issue type on the right

            However, you won't see anything on the page yet, because these <a> elements only exist in JavaScript. You still have to append them to the actual page somewhere.

            Let's do that now! At the top of single.js, create a reference to the issues container:

            var issueContainerEl = document.querySelector("#issues-container");
            Then in displayIssues(), add the following line right before the for loop closes:

            issueContainerEl.appendChild(issueEl);
            Save and test the app in the browser. On page load, you should see a list of issues pertaining to the repo that you hardcoded in the function call (e.g., getRepoIssues("facebook/react")). Try hardcoding a couple of different repo names to ensure the logic works for other cases.

            In your testing, you might come across a repository with no open issues. If the page displays nothing when there are no open issues, users might think your app is broken. Instead, you could check for no issues and display an appropriate message.

            At the top of the displayIssues() function, add the following if statement:

            if (issues.length === 0) {
            issueContainerEl.textContent = "This repo has no open issues!";
            return;
            }
            This code will display a message in the issues container, letting users know there are no open issues for the given repository.

            Test the app a few more times; check that repositories both with and without issues display correctly. Once you like the results, commit and push your progress to the feature branch!

6.3.6 

    So we've got the single-repo.html page working like we want, but there's a little bug. Right now, we can't view more than 30 issues at a time. If a repo has more than 30 issues, the user can't tell that some aren't being displayed.

    You can test this out by calling getRepoIssues() with "facebook/react", "expressjs/express", "angular/angular", or pretty much any popular repo out there.

    If you want to find out why only 30 issues show up, you can refer to the GitHub documentation on pagination. Notice that GitHub limits requests to 30 items at a time.

    PAUSE
    Why would GitHub limit the number of results like that?

    Answer
    The documentation explains that GitHub will actually supply a link header with more information if pagination exists for the data we're requesting. Okay, so what are headers?

    HTTP headers allow the client and the server to pass additional information with an HTTP request or response. This often includes information like whether or not to cache (locally store) data and, if so, for how long. This data goes in the headers because it's often small in file size and not directly pertinent to the content in the body of a request or response.

    For example, the very common Content-Type header specifies the format of the requested resource. The GitHub API responses always include a Content-Type value of application/json to signify that the response is formatted as JSON.

    You can view the headers on a request or response in DevTools by clicking on the request in the Network tab, then selecting Headers on the righthand side, as shown in the following image:

    Headers selected in the DevTools Network tab

    DEEP DIVE
    For this app, we won't worry about trying to show all of the paginated results. But we do want to make sure we let users know if there are more issues that can't be viewed. If that's the case, we'll direct users to GitHub, where they can see the full list.

    Here's how we'll get the message across. In getRepoIssues(), add a condition that checks for the Link header. If it exists, console log "repo has more than 30 issues", as shown in the following code:

    if (response.ok) {
    response.json().then(function(data) {
        displayIssues(data);

        // check if api has paginated issues
        if (response.headers.get("Link")) {
        console.log("repo has more than 30 issues");
        }
    });
    }
    Test the app with a repository that's bound to have more than 30 issues, like "facebook/react". Now we'll change this console log message into something a little more user-friendly.

    First, you'll need to create a new container in the single-repo.html file. At the bottom of the <main> element, add the following:

    Start of code snippet<div id="limit-warning">

    </div>End of code snippet
    Next, at the top of single.js, declare a DOM reference to this container as such:

    var limitWarningEl = document.querySelector("#limit-warning");
    Create a new displayWarning() function with a repo parameter. Then, in displayWarning(), update the textContent of limitWarningEl with the beginning part of the message, as follows:

    var displayWarning = function(repo) {
    // add text to warning container
    limitWarningEl.textContent = "To see more than 30 issues, visit ";
    };
    Then in displayWarning(), append a link element with an href attribute that points to https://github.com/<repo>/issues. Don't forget to append your link to the warning container! The code should look like this:

    var linkEl = document.createElement("a");
    linkEl.textContent = "See More Issues on GitHub.com";
    linkEl.setAttribute("href", "https://github.com/" + repo + "/issues");
    linkEl.setAttribute("target", "_blank");

    // append to warning container
    limitWarningEl.appendChild(linkEl);
    Finally, in getRepoIssues(), replace the console log in the if (response.headers.get("Link")) statement with a call to displayWarning(repo).

    Just as we did in the last section, test this new function out with different popular repos. If all looks good, commit your progress!

6.3.7

    Congratulations on using your skills of API fetching and DOM manipulation to create a brand-new page!

    You also accomplished the following:

    Learned that request and response headers contain additional information about the request, separate from the data itself—like GitHub's Link header that lets you know there are more pages of results to request.

    Leveraged a new GitHub API endpoint to request more specific data, using an optional ? string to change how results are sorted.

    Remember, right now the single-repo.html page works, but it depends completely on some hardcoded values at the bottom of single.js. Next, we’ll hook it up to the homepage so that users can see detailed info on any repo they click on.

6.4.1

            You've made great progress so far on the Git it Done app! You've flexed your JavaScript abilities and received the anticipated responses for the GitHub API calls for both the repos and the issues endpoints. However, in order to test the API call, you initially hardcoded it, as shown in this image:

            Hardcoded issues page doesn't respond to user requests.

            As the following image shows, the homepage can populate a list of repos from the user:

            Git it Done homepage shows repos for the username "microsoft".

            But the problem is getting from the repository search results to the new issues page. If you click on an individual repo from the list on the homepage, nothing happens. You can't navigate to the single-repo.html page to view the corresponding issues for a selected repo.

            In this lesson, we'll resolve that problem by calling the GitHub API issues endpoint dynamically. Once the user has selected a repo from the list on the homepage, they'll be automatically directed to the single-repo.html page. There, they'll see a list of issues that correspond to the repo name selected, if issues for that repo exist.

            Your friend Amiko is pleased with your progress so far; the most difficult parts of this project are behind you! Now you just need to connect the pages.

            To determine what we need to accomplish in this lesson, let's look at the corresponding GitHub issue, shown in the following image:

            GitHub issue explains that clicking on a repository name will result in navigation to a page populated with a list of issues

            As the issue describes, if a user clicks on a repo name from the list, a new page should load, populated with open issues related to the selected repo. But how do we transfer the repo name from the homepage, index.html, to the issues page, single-repo.html?

            ON THE JOB
            Whenever we build something mechanical from component parts—whether that be a computer, fan, or car—once we've determined that the parts work, we can wire them together so that the pieces function together. Similarly, once we've verified that the API calls work by rendering the response in the browser, we can connect these parts to operate as a cohesive application. Web developers at all stages of their careers often have to connect different parts of an application to supply critical data when and where it's needed.

            End of text box.
            In this lesson, you'll learn how to do the following:

            Pass information from one page to another using query parameters

            Obtain data from a URL using browser-provided location objects

            Make the API call dynamic by using the query parameter to alter the request

            In tackling this problem, you'll implement a strategy that will serve you throughout your career. It’s common in web development to allow users to select individual items from a high-level list to display more details about those items, just as you're doing with the Git it Done search results. E-commerce sites and restaurant-listing sites exemplify this practice.

    6.4.2
            Currently, we can't list repos in index.html if the user provides a valid GitHub username. And in single-repo.html, we can only list issues from a repo that we hardcoded. In this lesson, we need to figure out how to communicate from one page to another so that when the user selects a repo from the repo list, a new page opens and populates with issues that correspond to that repo.

            Let's take a look at how the final application should look, shown in the following animation:

            Clicking on a repository name routes the user to a new page with a list of all issues

            Notice the seamless flow between pages as the user selects the repo name, which pulls up the next page of issues related to that repo.

            To achieve this, you'll first want to outline the necessary steps. Try reordering the following steps to form a game plan:



            Even though these steps might not seem like much work, in this process you'll encounter some new JavaScript methods and web development concepts. You'll also need to adjust code in both of the JavaScript files.

            Let's get started!

    6.4.3 Create a link between pages

            In this step, we'll convert the list of repos into a list of links. We need a process that will programmatically navigate to a new page once a user has clicked a repo name on the homepage, thus linking the homepage, index.html, to the single-repo.html page.

            To accomplish this, let's open the homepage.js file to find the displayRepos() function and target the for loop that's dynamically creating HTML elements from the GitHub API response. Change the expression that creates a <div> to create an <a> element instead. We'll also need to add an expression to create a new href attribute.

            Make the following changes to the code:

            // loop over repos
            for (var i = 0; i < repos.length; i++) {
            // format repo name
            var repoName = repos[i].owner.login + "/" + repos[i].name;

            // create a container for each repo
            var repoEl = document.createElement("a");
            repoEl.classList = "list-item flex-row justify-space-between align-center";
            repoEl.setAttribute("href", "./single-repo.html");
            // create a span element to hold repository name
            ...
            }
            Let's see if the changes are linking the single-repo.html page to the index.html page. Save the files and load index.html in the browser, then enter a username. Select and click on a repo name; doing so should take you to the issues page. Repeat the process a few times, clicking on different repo names to see if this effect is consistent. The following animation illustrates what you should be seeing:

            Cursor clicks on multiple repos, which all navigate to the same issues page.

            Notice that although we were routed to the single-repo.html page, the list of issues remained the same. Why's that?

            Although the routing has been corrected, the request to the GitHub API issues endpoint is still hardcoded with facebook/react (or whatever you used when testing). In order to change this to reflect the user's selected repo name, we'll need to pass the repo name from index.html to single-repo.html. Thankfully, we can do this with query parameters.

            IMPORTANT
            Notice that the path to the single-repo.html page in the newly created href attributes is a relative path from the index.html page, not homepage.js, where the element is created. From the browser's perspective, although dynamically created, these HTML elements become part of the markup—as shown in the page source of the rendered page. So when you create links to HTML pages in JavaScript, make sure the paths are relative to the HTML pages, not the JavaScript file.

            Query Parameters
            We use query parameters—strings appended to the end of URLs—to define actions, pass information, or specify content to the webpage or API endpoint. The ? symbol at the end of the URL identifies the parameters. Parameters are assigned values in a key=value format; the = assigns the value to the parameter/key. This information is passed to the website as part of the URL.

            PAUSE
            Where have we seen the ?key=value at the end of a URL before?

            Answer
            Let's start with an example by typing the following into the browser's address bar: https://www.google.com/search?q=javascript.

            This diagram shows the breakdown of the query parameter:

            Diagram labeling the query parameter (?q=javascript) and its position relative to the root URL string (www.google.com/search)

            On the URL www.google.com/search?q=javascript, we appended a query parameter designated by the ? character. The parameter was defined as q, and its value is the word javascript. A query string is the string that follows the ?, which in this case would be q=javascript.

            When the server at Google receives the request, it looks at the query parameters for further information. When it sees a value for the key q, it interprets this as a search for the corresponding value. In this case, the value is "javascript", so the server at Google responds with the search results that match "javascript".

            DEEP DIVE
            Now let's use query parameters to pass a repo name to the single-repo.html page. We must first open the homepage.js file, revisit the for loop containing the newly created <a>, and adjust the href attribute to contain the query parameter, the selected repo's name. We've already assigned the variable repoName, so we can use that value to construct a query string to append to the end of the URL, ./single-repo.html.

            Try this task yourself by creating a new parameter called repo that's equal to the value saved in the repoName variable.

            This is the query string we must append to the href value: ?repo=<repo>.

            Once updated, the displayRepos() function will look like this:

            // create a link for each repo
            var repoEl = document.createElement("a");
            repoEl.classList = "list-item flex-row justify-space-between align-center";
            repoEl.setAttribute("href", "./single-repo.html?repo=" + repoName);
            To check if this change to the code works, let's save the files and open index.html in the browser. After entering a GitHub name and clicking on a repo name, we'll be directed to the single-repo.html page, as demonstrated previously. Nothing about the webpage will have changed yet. However, a look at the URL in the address bar of the browser will confirm that the query parameter is present. In the following example, the repo microsoft/activities was selected:

            The address bar in the browser shows a query string appended to the end of the URL

            Excellent work! You've made a nice breakthrough, passing information from one page to another page. Notice how this URL looks so different than the previous example with Google, because we're loading the HTML file from a local directory, not from a server online.

            So how does the second page retrieve this information so that it can pass to the API call? We'll explore that question in the next step.

6.4.4
        The page single-repo.html can still load properly even though the query string was appended, because the browser interprets the query string as optional information and ignores it as a result.

        In this step, a browser-based tool called the Location Web API will help us retrieve that query parameter.

        DEEP DIVE
        This API is a property of the document object we used previously to query elements on the page. In this instance we'll use document.location to locate the query string.

        First let's go to the browser, open the Console tab in DevTools, type document.location, and press Enter. Expand the logged object to see what's shown in the following image:

        The document.location object and its properties, with the search property highlighted, containing the query string.

        As we can see in the preceding image, the location object contains a few methods and properties that hold information about the URL of the webpage. Of particular interest will be a property called search, highlighted in the image shown, because it contains the query string.

        Now let's type document.location.search directly into the command prompt in DevTools. This will return only the query string portion of the URL, as follows: ?repo=microsoft/activities.

        Excellent work! We've loaded single-repo.html with the query string in the URL and accessed it with JavaScript. In the next step, we'll trim this string even further to get the exact data we need from it.

        Use the Split Method to Extract the Query Value
        Having a string conveniently allows us to use a variety of string methods to retrieve the data, namely the split() method. We can use string methods such as split() on all strings in JavaScript, not just query strings. The split() method splits a string into an array of substrings and returns the new array. The argument passed into the split() method will be used as the separator between those substrings.

        For a quick exercise to demonstrate how this works, navigate to your browser and open the console in DevTools. In the console prompt, type the following expression:

        var string = "Hello World";
        Now let's use the split() method and pass o in as the argument and separator, as shown here:

        string.split("o");
        The console will display the result shown in the following image:

        An array with three elements ("Hell", " W", and "rld") is returned in the browser console by the split method.

        The preceding figure shows that the split results in an array with three elements: "Hell", " W", and "rld". Notice how each element of the array corresponds to the letters in the string before or after the letter "o". The "o" is the separator that defines where the substrings begin and end, but it isn't added to the array itself.

        IMPORTANT
        If an empty string ("") is used as the separator, the string is split between each character. Also, the split() method doesn't change the original string.

        With these tools available, let's open single.js and extract the query value from the query string in the API call function getRepoIssues().

        The first step will be to create a new function, called getRepoName(), near the top of the file. Remember to place the function call at the bottom of the file as well. The function call will look like this: getRepoName().

        Let's also remove the hardcoded function call for getRepoIssues(), located at the bottom of the single.js file.

        In the next step, we'll add to the getRepoName() function by using the location object and split() method to extract the repo name from the query string. Let's start by assigning the query string to a variable:

        var queryString = document.location.search;
        Once we have the queryString, which currently evaluates to ?repo=microsoft/activities, how do we break the string apart into the right substrings so that we can isolate the piece we need (microsoft/activities)? In other words, which character do we need to pass into the split() method to use as the separator?

        If you guessed the = character, you're correct! By splitting on the =, you'll end up with an array with two elements. Using bracket notation, which index number do you need to get the second element from the array?

        Remember, indexes start at zero, so we'll need to use [1] to get the second element. With that in mind, let's add the following expression to the getRepoName() function in the single.js file:

        var repoName = queryString.split("=")[1];
        console.log(repoName);
        Let's save the files and load the index.html page in the browser. We need to enter a username and choose a repo. In this example, microsoft/activities was used. You should see something like the following image once you open the console:

        The repoName value displays in the console, corresponding to the selected repo, "microsoft/activities".

        This image shows that we succeeded in isolating the repo name from the query string.

        For the next step, we'll pass the repoName variable into the getRepoIssues() function, which will use the repoName to fetch the related issues from the GitHub API issues endpoint.

        Try this yourself and place the function call in the getRepoName() function.

        Now let's add the repo name to the header of the page. We already have a <span id="repo-name"> container defined in the HTML. We'll need a DOM reference to be able to update it.

        At the top of single.js, add the following:

        var repoNameEl = document.querySelector("#repo-name");
        In the getRepoName() function, use this variable to update the element's text content as such:

        var getRepoName = function() {
        var queryString = document.location.search;
        var repoName = queryString.split("=")[1];
        getRepoIssues(repoName);
        repoNameEl.textContent = repoName;
        }
        Save your work and load index.html in the browser to test if the API call is now dynamic.

        The browser should display the issues related to the repo and dynamically call the API. You can test this by selecting various repos and comparing the issues that are rendered. The results should look similar to this image:

        Git it Done issues page now shows an open issue for the "microsoft/activities" repo

        The issues page now shows the single issue associated with the selected repository. Return to the search results and click on different repos with open issues to test if the API call is now dynamic.

        Great job! The single-repo.html page is dynamically rendering the issues based on the repo selected.

        The app works just as Amiko has requested. However, to make the app robust and durable, we should add error handling. This means that we should not only make the application work for people who use it as intended but also guide those users who don't.

        Moving on, we'll tackle these two main sources of errors:

        A user tries to access the single-repo.html page without providing a repository name.

        The request to the GitHub API is unsuccessful (due to server errors or an improperly formatted repository name).

6.4.5 Handle Errors

        In this step, we'll focus on error handling in the single.js file. To identify where potential errors could arise, consider the key parameters each function needs to operate correctly. For instance, suppose the getRepoName() function couldn't retrieve the correct query parameter or perhaps didn't get one at all. What should we do in this situation?

If a query parameter is unavailable, we should redirect the user back to the index.html page to try entering a username again.

Let's start by going into the single.js file, in the getRepoName() function. Add a conditional statement to check for valid values before passing them into their respective function calls. If the repoName is valid, feed the value to the fetch call; otherwise, redirect back to the index.html page.

For now, let's move the getRepoIssues() and the repoName.textContent expressions into a conditional statement that checks if the repoName exists, as follows:

if(repoName) {
  repoNameEl.textContent = repoName;
  getRepoIssues(repoName);
}
The preceding conditional statement will only display the repo name and make the fetch call if the value for repoName exists. For the second part of this conditional, we'd like to redirect back to the homepage. To do this, let's refer to the MDN web docs on the browser-based Location Web API (Links to an external site.).

Here we'll see many available properties and methods, but we'll pay particular attention to the replace() method. This method replaces the current resource or page with the one at the provided URL.

Let's try this out in the browser. Open the console and type the following statement into the prompt:

document.location.replace("https://google.com/search?q=javascript");
Note how this command redirected the page to the Google JavaScript search.

PAUSE
Which relative path would redirect us to index.html?

Answer
Add the second part to the conditional statement that will occur when the repoName doesn't exist. In this case, we want to redirect the user back to the homepage to try again.

else {
  document.location.replace("./index.html");
}
Excellent work! Now the getRepoName() function should look like this (with comments):

var getRepoName = function() {
  // grab repo name from url query string
  var queryString = document.location.search;
  var repoName = queryString.split("=")[1];

  if (repoName) {
    // display repo name on the page
    repoNameEl.textContent = repoName;

    getRepoIssues(repoName);
  } else {
    // if no repo was given, redirect to the homepage
    document.location.replace("./index.html");
  }
};
Time to check if this error-handling feature works! To simulate not receiving a repository name, open the single-repo.html file directly in the browser. Remember, you can do this from VS Code with the "Open in Default Browser" option. Because opening the file directly does not include a query string, the browser will quickly redirect to index.html.

By checking to see if the query parameter is available, we can preempt a potential error before it ever reaches the API call. We could also use this technique within the API call itself when we check the validity of the response received from the issues endpoint. Currently, if the response is invalid, the user receives an alert saying there was a problem with the request. Instead, let's redirect the user back to the homepage.

Open the single.js file and go to the getRepoIssues() function. In the fetch() function, we see a conditional statement that's checking if the response from the issues endpoint from the GitHub API is valid. Let's change the else portion of this statement to redirect the user back to the homepage rather than send the user an alert(). Change that portion of the code now, and remove the console.log() as well.

IMPORTANT
When it comes to console.log() statements, feel free to keep them in your code or remove them as you see fit. As developers, sometimes it's best that we keep certain ones in until the application is ready for production, while at other times we add and remove them as needed to aid us in viewing data in the Chrome DevTools console. At the end of the day, they exist to support developers in understanding and working through their application's code, and they serve no purpose to the end user.

The fetch() function should now look like the following code:

// make a get request to url
fetch(apiUrl).then(function(response) {
  // request was successful
  if (response.ok) {
    response.json().then(function(data) {
      displayIssues(data);

      // check if api has paginated issues
      if (response.headers.get("Link")) {
        displayWarning(repo);
      }
    });
  } else {
    // if not successful, redirect to homepage
    document.location.replace("./index.html");
  }
});
To check if this redirect is working, update the URL in your browser's address bar from index.html to something like: single-repo.html?repo=nope. There is no "nope" repository on GitHub, which will cause a bad response when fetched, and thus trigger the redirect.

6.4.6 Save Your Progress with Git

              6.5.1 Introduction

                  Congratulations on making it this far into your project! You've built a complete app that can take users from one page to another, transferring information along the way. This animation shows what you've done so far:

                Cursor scrolling down homepage of Git it Done app, then clicking on a repository and scrolling through issues page.

                Remember that we still need to tackle one more issue. We need to add buttons that allow the user to filter the repository search results by programming language, as seen in the following GitHub issue:

                Final GitHub issue says users should be able to click on a language button to filter results.

                Adding this capability will be possible because GitHub already allows users to search for popular repositories in a specific programming language. With over 28 million public repositories, GitHub can seem like a daunting place to start contributing to open source projects. For that reason, the site enables web developers to focus on projects that they actually have the skills to contribute to. For example, users can type something along the lines of "languages:javascript" in the search bar. Similarly, we'll add language buttons on the homepage of the Git it Done app to make it much easier to find open source projects that use familiar languages.

                We'll leverage past experience by completing the following tasks:

                Use data-* attributes for the buttons.

                Implement event delegation when clicking buttons.

                Add query string parameters to the GitHub API’s URLs.

                In this lesson, we'll accomplish the following:

                Recognize which options are available for GitHub API endpoints.

                Use multiple parameters in a query string.

                Use HTML attributes to dynamically update an API call.

                Experimenting with GitHub's API and similar APIs will give you valuable insight into the kinds of use cases to anticipate when building an API with other developers. You'll face many similar decisions when you learn back-end development and create your own APIs.




        6.5.2 Preview:
                We’ll add three new buttons to the homepage, listing featured repositories as the following image shows:

                Under original search box in Git it Done, a Search By Topic box now holds buttons for JavaScript, HTML, and CSS.

                Try to organize the steps we'll take to get there:



                This lesson will encompass a lot of material you've already learned, giving you an opportunity to practice both new and old skills.

        6.5.3 Create a function to call the github search api:

                Alright, let's build a function to call the search API. However, before we do so, let's make sure to create a new Git branch called feature/languages.

                And before we spend too much time on the smaller details, we should try getting the API endpoint working. To do so, let's head over to the GitHub API docs section on searching for repos (Links to an external site.).

                Note that the search method only returns up to 100 repositories. This works for our purposes, because we only need the user to view 30 or so at a time.

                Direct your attention to the parameters section, as shown in the following image:

                API parameters <code>q</code>, <code>sort</code>, and <code>order</code> listed on the left with the parameter type and a description listed to the right

                Remember, we recently learned how to use parameters! This time, we can use them to specify that we want the search to not only include results that match the query but also sort them.

                IMPORTANT
                We should sort results when we make queries involving large collections of data. In this case, if we don't, the API could return unmaintained, unpopular repositories. We want to avoid these kinds of repositories, which can be more challenging to contribute to.

                Now that we're using multiple parameters, we need to specify where the first parameter ends and the second one begins. We can do this by including the & symbol between each parameter in the URL.

                With that in mind, enter the following URL in your browser:

                https://api.github.com/search/repositories?q=javascript+is:featured&sort=stars
                The result should look something like the following image:

                GitHub API response after searching for featured JavaScript repos

                Now try that same endpoint without the sort parameter.

                Notice that the result still includes repositories with a high number of stars. This happens because the sort parameter has a default value of score in case we don't provide one. GitHub applies the score value to repositories based on their relevance to the search term and their popularity.

                The GitHub API allows us to add additional parameters, known as qualifiers. For example, adding +is:featured tells the API that we only want featured repositories. That's not all, though! We can add as many qualifiers as we can think of, as long as the URL remains under 255 characters.

                DEEP DIVE
                As mentioned in the documentation, the structure for the API call will look something like this:

                q=SEARCH_KEYWORD_1+SEARCH_KEYWORD_N+QUALIFIER_1+QUALIFIER_N
                This example provided by GitHub includes two search keywords and two qualifiers, but the query itself can accept a varied number of each. The _N syntax indicates the last qualifier. A real example of this would look like the following code:

                q=javascript+html+css+is:featured
                In this case, the N keyword would be css and the N qualifier would be is:featured, because they are the last of each set of data, respectively.

                DEEP DIVE
                Now, in homepage.js, create a new function called getFeaturedRepos() that accepts a language parameter, creates an API endpoint, and makes an HTTP request to that endpoint using fetch(), as shown in the following code:

                var getFeaturedRepos = function(language) {
                  var apiUrl = "https://api.github.com/search/repositories?q=" + language + "+is:featured&sort=help-wanted-issues";

                  fetch(apiUrl);
                };
                To verify that this endpoint works, open index.html in your browser and call getFeaturedRepos("javascript"); in the console, as shown in the following image:

                The getFeaturedRepos() function is called in the console.

                We didn't console log the response, but we can go to the Network tab and click on the response to view it in its entirety, like in the following image:

                Response with featured repos appears in the Network tab, under Preview

                Perfect! Now that we've tested the new function, we can move on to displaying the data on the page.
      
      6.5.4 Display the API Data on the Page

                Remember that even though the fetch is successful, we still need to format that response before we display it on the page.

                Let's do that now. Add a then() method and log the response to the console. Make sure to add error handling for bad responses. The code should look something like this:

                var getFeaturedRepos = function(language) {
                  var apiUrl = "https://api.github.com/search/repositories?q=" + language + "+is:featured&sort=help-wanted-issues";

                  fetch(apiUrl).then(function(response) {
                    if (response.ok) {
                      console.log(response);
                    } else {
                      alert('Error: GitHub User Not Found');
                    }
                  });
                };
                You'll need to call getFeaturedRepos("javascript"); from the DevTools console again to see the response object. Note that the response is the entire HTTP response, not the JSON. Just like we did previously, we need to use a method to extract the JSON from the response, so let's update getFeaturedRepos() to parse the response and log the data to the console, as follows:

                var getFeaturedRepos = function(language) {
                  var apiUrl = "https://api.github.com/search/repositories?q=" + language + "+is:featured&sort=help-wanted-issues";

                  fetch(apiUrl).then(function(response) {
                    if (response.ok) {
                      response.json().then(function(data) {
                        console.log(data)
                      });
                    } else {
                      alert('Error: GitHub User Not Found');
                    }
                  });
                };
                Next, in the json() method's callback function, pass data.items and the language parameter's value into displayRepos(), shown here:

                var getFeaturedRepos = function(language) {
                  var apiUrl = "https://api.github.com/search/repositories?q=" + language + "+is:featured&sort=help-wanted-issues";

                  fetch(apiUrl).then(function(response) {
                    if (response.ok) {
                      response.json().then(function(data) {
                        displayRepos(data.items, language);
                      });
                    } else {
                      alert('Error: GitHub User Not Found');
                    }
                  });
                };
                Test the displayRepos() function by calling getFeaturedRepos("javascript"); again in the DevTools console, verifying that the featured repos appear on the page this time.

                This testing of the function before we add the buttons that will control the page is vital now that we're making requests, because many more things could go wrong in the process of retrieving information from the server. Catching these bugs before working on the rest of the front end enables us to more easily narrow down what went wrong if things break.

    6.5.5 Add Language Buttons in the HTML

                Great job! Now you'll need to add language buttons to the HTML so that the user can easily make queries for different types of GitHub repositories. From this point forward, you might notice that you already have all of the skills you need to finish this project!

                See the following image for a glimpse of the buttons we'll make:

                Close-up view of the Search By Topic box with buttons for JavaScript, HTML, and CSS.

                Because this looks a lot like the <div> with a class of card that holds the main search form, use that HTML as a reference to create a new <div> element right below it:

                Below the <div> that holds the <form> element in the left column, create another <div> element with a class attribute value of card. That left column should now have two <div> elements with a class of card.

                Inside that <div>, create an <h3> element with text content that says "Search By Topic". Give it a class attribute with two values: card-header and text-uppercase.

                Right underneath the <h3> element, create another <div>, with a class attribute of card-body and an id attribute of language-buttons.

                Now that we've created the card element to hold the buttons, let's go ahead and create those as well.

                PAUSE
                What can we use to keep track of the language attached to each button?

                Answer
                In the <div> with a class of card-body, let's make three <button> elements for JavaScript, HTML, and CSS, and be sure to give each one the following:

                Text content for the topic it'll search for (e.g., JavaScript, HTML, CSS).

                A class attribute value of btn.

                A data attribute called data-language, with a value of the topic the button will search for when clicked. Make the value lowercase.

                Before we move on, make sure that the new buttons look like the following image:

                Close-up view of the Search By Topic box with buttons for JavaScript, HTML, and CSS.

                If they don't, check for typos in the class names!

  6.5.6 Call the API function when buttons are clicked 

              We're almost finished. All that remains is making the call to the API every time the buttons are clicked and merging the project to the main branch on GitHub.

              To do this, at the top of homepage.js, create a new variable called languageButtonsEl. Give it a value of the <div> with an id value of language-buttons.

              HINT
              Now we can add a click event listener to the <div> element that will call a new buttonClickHandler() function. Add the following at the bottom of the homepage.js file:

              languageButtonsEl.addEventListener("click", buttonClickHandler);
              REWIND
              Why aren't we creating click listeners for each button? Think back to the concept of event delegation. Imagine that we had 15 language buttons instead of 3. That may add a lot of extra, repeated code. To keep the code DRY, we can delegate click handling on these elements to their parent elements.

              Now let's create the buttonClickHandler() function somewhere above the addEventListener() expression to ensure things exist in the right order. This function should use event delegation to handle all clicks on buttons.

              We'll start by creating a variable called buttonClickHandler. Give it a value of a function that accepts event as a parameter. Inside that function, create a variable called language, and give it a value of event.target.getAttribute("data-language"). Then use console.log() to log the value of language for testing purposes.

              Remember, the browser's event object will have a target property that tells us exactly which HTML element was interacted with to create the event. Once we know which element we interacted with, we can use the getAttribute() method to read the data-language attribute's value assigned to the element.

              The following image demonstrates the behavior you should see:

              Clicking the JavaScript button displays "javascript" in the console

              Now let's call the getFeaturedRepos() function and pass the value we just retrieved from data-language as the argument. Add the following if statement block to the buttonClickHandler() function right below the language variable declaration, as follows:

              if (language) {
                getFeaturedRepos(language);

                // clear old content
                repoContainerEl.textContent = "";
              }
              Make sure to include repoContainerEl.textContent = "" to clear out any remaining text from the repo container. Even though this line comes after getFeaturedRepos(), it will always execute first, because getFeaturedRepos() is asynchronous and will take longer to get a response from GitHub's API.

              PAUSE
              Answer
              Now that the code is complete, take a moment to go through the app and test the various functions. Try alternating between button clicks for HTML, CSS, and JavaScript. Try filling out a username and ensure that the form still works.