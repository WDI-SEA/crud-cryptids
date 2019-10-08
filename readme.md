# RESTful Cryptids (Lab)

So far, we've only been rendering views, which is why we've been using GET for all of our routes. Now that we're working with data, we'll start to see how the other HTTP verbs come into play. Here we will focus on `GET` and `POST`.

## Directions

1. Have a working Dinosaur app
2. Follow the steps below to get a working app!
3. If you finish early, start on the [Part 2](./part2.md)!

## Objectives: Learning GET & POST
* Implement `GET` and `POST` routes in `express`. 

##### These are the routes we want to implement
| VERB | URL | Action (CRUD) | Description |
|------|-----|---------------|-------------|
| GET | /cryptids | Index (Read) | displays all cryptids |
| GET | /cryptids/:id | Show (Read) | displays the type and photo of a particular cryptid (:id would be replaced by an actual number 1) |
| GET | /cryptids/new | Show(Read) | form for making a new cryptid |
| POST | /cryptids | Create | creates an cryptid with the POST payload data |

### STEP 1: Set up a new branch in your Dinos app called `cryptids`.

**Backend data store**

We'll start workign with data from an actual database soon, but for now we'll just focus on routes and use, a JSON object as our data store. In the root of the project, there is a file called `cryptids.json`. We're going to make another one called `cryptids.json`. Here's a couple of cryptids to get you started.

```json
[
  {
    "name":"Loch Ness Monster",
    "img_url":"https://www.history.com/.image/t_share/MTU3ODc5MDg3MjQzNjAxMjI1/loch-ness-monster.jpg"
  },
  {
    "name":"Big Foot",
    "img_url":"https://res.cloudinary.com/simpleview/image/upload/crm/eurekaca/patterson.gimlin0-b5b6c02e5056a36_b5b6c19f-5056-a36a-096e6e95925c7427.jpg"
  },
  {
    "name":"Batsquatch",
    "img_url":"https://vignette.wikia.nocookie.net/cryptidz/images/c/c7/Batsquatch.jpg/revision/latest?cb=20100912164353"
  },
  {
    "name":"Sharlie (Slimy Slim)",
    "img_url":"https://vignette.wikia.nocookie.net/cryptidz/images/6/6c/Swimsharlie.jpg"
  }
]
```

* Routes: You'll want a new router file to deal with all of your cryptid-specific routes. When you impliment this in your main javascript file (`app.js`, `main.js`, `index.js`, `server.js`, etc), make it so all your routes in your `cryptids.js` will have the prefix `/cryptids`
* Views: For code cleanliness, we'll make a new folder in our views folder called `cryptids` that will hold all of our cryptid display pages


### STEP 2: Index / Read (GET) route

Index is a route (URL) that lists all items of a specific type. It is a GET request to (in this example) `/cryptids`.

Format the ejs to display the data. Assume that we will pass the data in as `myCryptids`.

**Index view -- in /views/cryptids/index.ejs**
```html
<ul>
  <% myCryptids.forEach(function(cryptid) { %>
  <li><%= cryptid.name %></li>
  <% }); %>
</ul>
```

Now let's pull in our data and take a took at it. 

```js
// Index page——/cryptids
router.get('/', function(req, res) {
  var cryptids = fs.readFileSync('./cryptids.json');
  console.log(cryptids);
});
```
 ***Note:*** This `console.log()` is in our server file, which means it will print to our terminal, NOT the browser inspector.
 
 That doesn't look very helpful, does it? That's because we're pulling in a JSON object, which isn't quite the same as a normal JS object. JSON (JavaScript Object Notation), is a standard format for data that is being transmitted (sent back and forth), and it needs to be _parsed_, or converted to a true JS data type - in this case, an array.  Read more about working with JSON [here](https://developer.mozilla.org/en-US/docs/Learn/JavaScript/Objects/JSON).

Try parsing the data before printing it:

```js
// Index page——/cryptids
router.get('/', function(req, res) {
  var cryptids = fs.readFileSync('./cryptids.json');
  var cryptidData = JSON.parse(cryptids);
  console.log(cryptidData);
});
```

That's more like it! Now lets send it to our EJS file:

```js
// Index page——/cryptids
router.get('/', function(req, res) {
  var cryptids = fs.readFileSync('./cryptids.json');
  var cryptidData = JSON.parse(cryptids);
  res.render('cryptids/index', {myCryptids: cryptidData});
});
```

In the above example we load the `cryptids/index.ejs` view and pass it `cryptidData` as `myCryptids`. Now we can access myCryptids directly in the `index.ejs` file.

### STEP 3: Show / Read (GET) route

_Show_ is a route that displays a single item of a specific type. Since we're still just reading data, it is a GET
request to (in this example) `/cryptids/1`

Create a `cryptids/show.ejs` file to show the information on a specific cryptid as well at its image (make sure to put an `alt` tag in there).

Next write your show route. We can access the index from the url through the `req.params` object, but it will be a string. In order to use in to access an array value, we need to cast it to an integer.

<details><summary>Check your work!</summary>

**Show route -- in index.js**
```js
//express show route for cryptids (lists one cryptid)
router.get('/:idx', function(req, res) {
  // get cryptids
  var cryptids = fs.readFileSync('./cryptids.json');
  var cryptidData = JSON.parse(cryptids);

  //get array index from url parameter
  var cryptidIndex = parseInt(req.params.idx);

  //render page with data of the specified animal
  res.render('cryptids/show', {myCryptid: cryptidData[cryptidIndex]});
});
```

In the above example we load the `cryptids/show.ejs` view and pass it a specific
item from the `cryptidData` array as `myCryptid`. We use the `:idx` url parameter to
specify which animal to display. This means in the `show.ejs` file we can
access myCryptid directly.

</details>


### STEP 4: New / Read (GET) route

To create an item (a cryptid in this example) we need to get the data about that item, so we'll use a [form](https://www.w3schools.com/html/html_forms.asp).

Form tags have two attributes that are very import for their CRUD functionality:
* ***method:*** HTTP verb - `GET` or `POST`. You will use POST most often because it is significantly more secure. Read about the difference between these two methods by scrolling down to the "When to Use GET" AND "When to Use Post" sections of [this page](https://www.w3schools.com/html/html_forms.asp).
* ***action:*** This value should be a path. Specifically, it is the url pattern associated with the route that will handle the data - in this case, that will be the `/cryptids` POST route we will write.

Create a `cryptids/new.ejs` view that contains an html form:

```html
<form method="POST" action="/cryptids">
  <label for="cryptidName">Cryptid</label>
  <input id="cryptidName" type="text" name="name">

  <label for="cryptidImg">Image Url</label>
  <input id="cryptidImg" type="text" name="img_url">

  <input type="submit">
</form>
```

Now write a `GET` route so we can view this form at `localhost:3000/cryptids/new`:

<details>
<summary>Trouble?</summary>

```js
router.get('/new', function(req, res){
  res.render('cryptids/new');
});
```
> Not working? Make sure this route is _above_ the show (`/cryptids/:idx`) route, otherwise the show route will catch the request and pass in "new" as `req.params.idx`.
</details>

### STEP 5: Create (POST) route

When the above form is submitted it will make a `POST` to the url `/cryptids` with the data contained in the form fields. To receive this data, we need to create the `POST` route and use some middleware to make the data readable. This middleware is new to `express` and prior to version 4, we had to manually install the `body-parser` node package in order to use it. 
> (If it bothers you that we're glazing over how the data comes through before we send it through something like `body-parser`, read [this article](https://medium.com/@adamzerner/how-bodyparser-works-247897a93b90). It will always make sense to use some sort of framework like `body-parser` in practice, but if you're interested in capturing the raw data, see [this article](https://itnext.io/how-to-handle-the-post-request-body-in-node-js-without-using-a-framework-cd2038b93190).)

The body parsing middleware will store the data submitted from the form in a user-friendly `req.body` object.

<details>
<summary>Want to check that you've got body parsing?</summary>

***index.js***
```js
var express = require('express');
var app = express();
var ejsLayouts = require('express-ejs-layouts');
var fs = require('fs');

app.set('view engine', 'ejs');
app.use(ejsLayouts);
//body-parser middleware
app.use(express.urlencoded({extended: false}));

.
.
.
```

The `express.urlencoded()` middleware tells body-parser to capture urlencoded data (form data) and store it in `req.body`. The `{extended: false}` option ensures that the values in this body will either be strings or arrays. More on this [here](https://www.npmjs.com/package/body-parser#bodyparserurlencodedoptions). Further discussion on it [here](http://stackoverflow.com/questions/29175465/body-parser-extended-option-qs-vs-querystring).
</details>

Now, we can access the form data in a POST route!

***cryptids.js***
```js
router.post('/', function(req, res) {
  res.send(req.body)
});
```

Try adding a new cryptid and make sure you see the appropriate data come through in the terminal when you submit the form.

**body-parser Summary:**
Form data is passed as payload of the request. Every field that has a name will be included in that payload and it is sent as form encoded text. When `body-parser` is used, it automatically **parses** the form body into a javascript object that we can use and it stores it in `req.body` so we can use it (similar to how we convert API responses to JSON. All of this is done as middleware, which we just configured).

**The `name` attribute matters!**
In the above example we could access the cryptid name form field by using `req.body.name` and the image Url field by using `req.body.img_url`. This correlates directly to the _names_ given to the form fields in the **form html** above.

Generally, the code in the **express route** would contain code that would CREATE an item in a database and redirect the user to a route with a confirmation message of some sort or just back to the index route. For this example we're going to use the JSON file created above to store our data. This will involve three steps:

* Reading the JSON file
* Pushing the new cryptid to the object
* Writing the new JSON file using [fs.writeFileSync](https://nodejs.org/api/fs.html#fs_fs_writefile_file_data_options_callback) (this will replace the old cryptids.json)

```javascript
router.post('/', function(req, res) {
  // read cryptids file and reassign the value to usable data
  var cryptids = fs.readFileSync('./cryptids.json');
  cryptids = JSON.parse(cryptids);

  // add item to cryptids array
  cryptids.push(req.body);

  // save cryptids to the data.json file
  fs.writeFileSync('./cryptids.json', JSON.stringify(cryptids));

  //redirect to the GET /cryptids route (index)
  res.redirect('/cryptids');
});
```

> `JSON.stringify` does the opposite of `JSON.parse` - it converts javascript data into json data.

### BONUS STEP: Show / Read (GET) with a Form

There may be instances where you need to `GET` cryptids, but you don't want them all. A good example is filtering cryptids with a specific name via a search bar.

In these cases, you don't want to use a POST action, because POST is reserved for creating new resources. Instead, we can create another form with a `GET` method, and read the data via a **query string**. This is an appropriate use of the `GET` form method because the user input is not sensitive.

Add a form to `cryptids/index.ejs`

```html
<form method="GET" action="/cryptids">
  <label for="nameFilter">Filter by Name</label>
  <input id="nameFilter" type="text" name="nameFilter">
  <input type="submit">
</form>
```

The idea here is that the search bar allows the user to filter what's on the page, so it will be a `GET` request to `/cryptids`... but we already have a route for that! When you submit a form using the `GET` method, the key/value pairs are appended to the URL in a _query string_. Try searching for a cryptid now and notice what happens to the URL. This query string, like parameters (`req.params`) is available via the request object. We'll use a conditional to check if there's a querystring, then filter the cryptids if one is present.

```js
router.get('/', function(req, res) {
  var cryptids = fs.readFileSync('./cryptids.json');
  var cryptidData = JSON.parse(cryptids);

  var nameFilter = req.query.nameFilter;

  if (nameFilter) {
    cryptidData = cryptidData.filter(function(dino) {
      return dino.name.toLowerCase() === nameFilter.toLowerCase();
    });
  }

  res.render('cryptids/index', {myCryptids: cryptidData});
});
```

## All Done?

Go ahead and get started on the [Part 2](./part2.md)!
