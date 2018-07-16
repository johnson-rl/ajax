# AJAX

### Why is this important?

In order to make complex web apps, we will probably want information from some of the amazing resources that provide rich, useful data. If you want to integrate maps, social media, live searched images, or any other user controlled data, you're going to want an API and AJAX to access it.

### What are the objectives?
<!-- specific/measurable goal for students to achieve -->
*After this workshop, developers will be able to:*

- find and read documentation for useful APIs
- explain the difference between AJAX and synchronous web data transfer
- use AJAX with jQuery's `$.ajax` to GET data from an API
- describe the meaning of `method:`, `url:`, and `success:` keys in jQuery's `$.ajax` syntax
- give an example of where AJAX would be used in code for a website

#### First Encounter with an API

Spend 10 minutes on the following:
  - Find an API on the internet, look at the documentation, and answer these questions:
    1. What format of data does this API return?
    1. How would you access the data that you're interested in? For example, for [Giphy](https://github.com/Giphy/GiphyAPI), the gif data that we're interested in is [located in the `data` array](https://github.com/Giphy/GiphyAPI#sample-response-search).
    1. Does this API require an API key?
    1. Can you view an API endpoint url in your browser? Do it!
    

## AJAX

__Asynchronous JavaScript And XML__ (AJAX) allows us to make requests to a server (ours or another application's) without refreshing the page.

#### How do we use AJAX?

jQuery gives us [several methods](https://api.jquery.com/category/AJAX) for making AJAX requests. We're going to stick to using the `$.ajax()` method [available here](https://api.jquery.com/jQuery.ajax/).

## GET and POST

The HTTP protocol was designed specifically for web browsers and servers to communicate with each other in a request/response cycle.

`GET` and `POST` are the most common verbs used in HTTP requests:

  * A browser will use `GET` to indicate it would like to receive a specific web page or resource from a server.
  * A browser will use `POST` to indicate it would like to send some data to a server.

Conveniently can use AJAX to make both `GET` and `POST` requests to servers. From the perspective of the server, it is just another request.

jQuery gives us the [`$.ajax()`](https://api.jquery.com/jQuery.ajax) method, which will allow us to perform any AJAX request.

## AJAX Setup

Using jQuery's `$.ajax()` method, we can specify several parameters, including:

* What kind of request
* request URL
* data type
* callback function (which will run on successful completion of the AJAX request)

Let's try sending a `GET` request to [Spotify's API](https://developer.spotify.com/web-api/search-item)

```js
$.ajax({
  // What kind of request
  method: 'GET',

  // The URL for the request
  url: 'https://api.spotify.com/v1/artists/1jTAvg7eLZQFonjWIXHiiT',

  // The type of data we want back
  dataType: 'json',

  // Code to run if the request succeeds; the JSON
  // response is passed to the function as an argument.
  success: onSuccess
});

// defining the callback function that will happen
// if the request succeeds.
function onSuccess(responseData) {
    console.log(responseData);
    // celebrate!
};
```
<!--
If we're doing a simple `GET` request, we can (and should) avoid the `$.ajax()` method and use the helper method `$.get()` instead. Here, we only need to pass in the request URL and callback function for the same AJAX request as the example above.

-->

For a `POST` request, we can also use the `$.ajax()` method, but this time, the data type is `"POST"`. Since `POST` requests send data to a server, we also need to send an object of `data` (the `book`).

```js
var bookData = {
  title: "The Giver",
  author: "Lowis Lowry"
};

$.ajax({
  method: "POST",
  url: "/books", // this is a "relative" link
  data: bookData,
  dataType: "json",
  success: onSuccess
});

function onSuccess(responseData) {
  console.log(responseData);
  // celebrate!
};

```

<!-- Just like with `GET`, the `POST` request above can be refactored to use the much simpler `$.post()` method. We pass in the request URL, data, and callback function. 

```js
var book_data = {
  title: "The Giver",
  author: "Lowis Lowry"
};

$.post('/books', book, function(data) {
  console.log(data);
});
``` -->

#### AJAX and Event Handlers

We can combine AJAX calls with any jQuery event handlers. You may want to execute an AJAX call when the user clicks and button or submits a form.

```js
var endpoint = 'https://api.spotify.com/v1/search?q=goodbye&type=artist'

// click event on button
$('button').on('click', function(event) {
  $.ajax({
    method: 'GET',
    url: endpoint,
    dataType: 'json',
    success: onClickReqSuccess
  });
});

function onClickReqSuccess(responseData){
  console.log(responseData);
  // process data
}

// submit event on form
$('form').on('submit', function(event){
  event.preventDefault();
  $.ajax({
    method: 'GET',
    url: endpoint,
    dataType: 'json',
    success: onSubmitReqSuccess
  });
});

function onSubmitReqSuccess(responseData){
  console.log(responseData);
  // process data
}

```

Sometimes we'll need to send data to an API in order for it to process our requests. For example, the Giphy API requires a `q` query paramater for the search endpoint.  When searching Giphy with `$.ajax()`, we include a `data` object or query string with a key-value pair inside indicating the value of `q`. 

```javascript
// submit event on form
$('form').on('submit', function(event){
  event.preventDefault();
  $.ajax({
    method: 'GET',
    url: endpoint,
    data: 'q=cats',
    dataType: 'json',
    success: onSubmitReqSuccess
  });
});

function onSubmitReqSuccess(responseData){
  console.log(responseData);
  // process data
}
```


If this data comes from the user, it will often be in a form. Luckily, jQuery provides a method called `serialize()` that transitions form data into a string that we can easily plug into the `data` attribute, like this:

```javascript
// submit event on form
$('form').on('submit', function(event){
  $.ajax({
    method: 'GET',
    url: endpoint,
    // The data to send aka query parameters
		data: $("form").serialize(),
    dataType: 'json',
    success: onSubmitReqSuccess
  });
});

function onSubmitReqSuccess(responseData){
  console.log(responseData);
  // process data
}
```

As long as the form `<input>` fields have the proper `name` attribute (in this case, `q`), `serialize()` will make our perfect object!

```html
<input type="text" class="form-control gif-input" name="q" placeholder="search gifs">
```

#### Handling Success and Failure

When everything goes well, we'll get a response with an HTTP *status* in the 200s. The `$.ajax` function checks this status to see if the request was sucessful.  If the status is in the 200s, it runs the success callback. 

We can't guarantee that an API will respond or respond quick enough. In these cases, the AJAX request will fail or give an error. Using the `$.ajax()` syntax, we can handle these cases by including `error` and `complete` attributes on our initial request:

```js
var endpoint = 'https://api.spotify.com/v1/search?q=come%20together&type=track';

$.ajax({
  method: 'GET',
  url: endpoint,
  dataType: 'json',
  success: onSuccess,
  error: onError,
  complete: onCompletion
});

function onSuccess(responseData, status, xhr){
  /*  perform this function if the
     status code of the response was in
     the 200s */
};

function onError(xhr, status, errorThrown){
  /* perform this function if the
     response timed out or if the
     status code of the response is
     in the 400s or 500s (error)
     xhr: the full response object
     status: a string that describes
     the response status
     errorThrown: a string with any error
     message associated with that status */
};

function onCompletion(xhr, status){
  /* perform this no matter the status
     code of the response */
};
```

## Independent Practice
Refine the skills covered in this workshop with this
[Giphy API training](https://github.com/sf-wdi-34/giffaw).

For a solution, checkout the `solution` branch or find it [here on GitHub](https://github.com/sf-wdi-34/giffaw/tree/solution).

For a solution to the bonus checkout the `solution-more` branch or find it here on [GitHub](https://github.com/sf-wdi-34/giffaw/tree/solution-more).


## Additional Resources
- [jQuery's `$.ajax()` documentation](http://api.jquery.com/jquery.ajax/)
- [Other possible syntax for `$.ajax()`](http://jqfundamentals.com/chapter/ajax-deferreds)
