# MEAN-STACK-IMPLEMENTATION
A MEAN stack is a bundle of four different software technologies that developers use to build websites and web applications. MEAN is an acronym for the NoSQL document database, MongoDB; the server side/backend, Express.js; the client side/frontend, Angular.js; and the Javascript runtime environment, Node.js. This technology stack allows the creation of a 3-tier architecture that includes frontend, backend, and database using JavaScript and JSON. We'll be utilizing this technology stack to build a simple book register webform. Before attempting this project, I had already created an AWS account and launched an instance running Ubuntu Server OS. The steps to build a book register webform with MEAN are as follows;

**STEP 1 - SET UP AN SSH AND CONNECT TO UBUNTU SERVER ON AWS VIA TERMINAL**

<img width="569" alt="1" src="https://github.com/ifyyegwim/MEAN-STACK-IMPLEMENTATION/assets/134213051/51abda7d-1bc6-423f-98c7-f6703cf83235">

**STEP 2 - INSTALL NODEJS**

Node.js is a JavaScript runtime built on Chrome’s V8 JavaScript engine. Node.js is used in this tutorial to set up the Express routes and AngularJS controllers.

*Update ubuntu*

    sudo apt update

*Upgrade ubuntu*

    sudo apt upgrade

*Install NodeJS*

    sudo apt install nodejs -y

 *Install latest NodeSource Node.js repo (20.x at the time of attempting this project)*

    curl -fsSL https://deb.nodesource.com/setup_20.x | sudo -E bash - &&\ sudo apt-get install -y nodejs

*Confirm version*

    node -v

<img width="262" alt="version" src="https://github.com/ifyyegwim/MEAN-STACK-IMPLEMENTATION/assets/134213051/d51e901e-cc73-4529-a1d2-0fd48cf55e07">

NPM comes already packaged with Node.js. Separate NPM installation is not necessary.

**STEP 2- INSTALL MONGODB**

MongoDB stores data in flexible, JSON-like documents. Fields in a database can vary from document to document and data structure can be changed over time. For our example application, we are adding book records to MongoDB that contain book name, isbn number, author, and number of pages. Before we can proceed with our installation, let’s update system and install the required packages:

    sudo apt update

*Next*

    sudo apt install wget curl gnupg2 software-properties-common apt-transport-https ca-certificates lsb-release

*Import the MongoDB public key on Ubuntu 22.04 LTS. Run the following command to import the MongoDB public GPG Key:*

    curl -fsSL https://www.mongodb.org/static/pgp/server-6.0.asc|sudo gpg --dearmor -o /etc/apt/trusted.gpg.d/mongodb-6.gpg

*Configure MongoDB Repo on Ubuntu 22.04 LTS. Using the following commands, add the repository to your system:*

    echo "deb [ arch=amd64,arm64 ] https://repo.mongodb.org/apt/ubuntu $(lsb_release -cs)/mongodb-org/6.0 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-6.0.list

*Install MongoDB on Ubuntu 22.04 LTS*

    sudo apt update

*Next*

    sudo apt install mongodb-org

*After successful installation, start and enable MongoDB:*

    sudo systemctl enable --now mongod

*Check MongoDB Status:*

    sudo systemctl status mongod

<img width="567" alt="2" src="https://github.com/ifyyegwim/MEAN-STACK-IMPLEMENTATION/assets/134213051/e07ff538-b181-44cd-ade4-955d96ae3aaf">

*Install body-parser package*

We need ‘body-parser’ package to help us process JSON files passed in requests to the server.

    sudo npm install body-parser

*Create a folder named ‘Books’*

    mkdir Books && cd Books

*In the Books directory, Initialize npm project*

    npm init

*Add a file to it named server.js*

    vi server.js

*Copy and paste the web server code below into the server.js file*

    var express = require('express');
    var bodyParser = require('body-parser');
    var app = express();
    app.use(express.static(__dirname + '/public'));
    app.use(bodyParser.json());
    require('./apps/routes')(app);
    app.set('port', 3300);
    app.listen(app.get('port'), function() {
        console.log('Server up: http://localhost:' + app.get('port'));
    });

**STEP 3: INSTALL EXPRESS AND SET UP ROUTES TO THE SERVER**

Express is a minimal and flexible Node.js web application framework that provides features for web and mobile applications. We will use Express in to pass book information to and from our MongoDB database.

We also will use Mongoose package which provides a straight-forward, schema-based solution to model your application data. We will use Mongoose to establish a schema for the database to store data of our book register.

    sudo npm install express mongoose

*In ‘Books’ folder, create a folder named apps*

    mkdir apps && cd apps

*Create a file named routes.js*

    vi routes.js

*Copy and paste the code below into routes.js*

```
const Book = require('./models/book');

module.exports = function(app){
  app.get('/book', function(req, res){
    Book.find({}).then(result => {
      res.json(result);
    }).catch(err => {
      console.error(err);
      res.status(500).send('An error occurred while retrieving books');
    });
  });

  app.post('/book', function(req, res){
    const book = new Book({
      name: req.body.name,
      isbn: req.body.isbn,
      author: req.body.author,
      pages: req.body.pages
    });
    book.save().then(result => {
      res.json({
        message: "Successfully added book",
        book: result
      });
    }).catch(err => {
      console.error(err);
      res.status(500).send('An error occurred while saving the book');
    });
  });

  app.delete("/book/:isbn", function(req, res){
    Book.findOneAndRemove(req.query).then(result => {
      res.json({
        message: "Successfully deleted the book",
        book: result
      });
    }).catch(err => {
      console.error(err);
      res.status(500).send('An error occurred while deleting the book');
    });
  });

  const path = require('path');
  app.get('*', function(req, res){
    res.sendFile(path.join(__dirname, 'public', 'index.html'));
  });
};
```
*In the ‘apps’ folder, create a folder named models*

    mkdir models && cd models

*Create a file named book.js*

    vi book.js

*Copy and paste the code below into ‘book.js’*
```
var mongoose = require('mongoose');
var dbHost = 'mongodb://localhost:27017/test';
mongoose.connect(dbHost);
mongoose.connection;
mongoose.set('debug', true);
var bookSchema = mongoose.Schema( {
  name: String,
  isbn: {type: String, index: true},
  author: String,
  pages: Number
});
var Book = mongoose.model('Book', bookSchema);
module.exports = mongoose.model('Book', bookSchema);
```
**STEP 4 – ACCESS THE ROUTES WITH ANGULARJS**

AngularJS provides a web framework for creating dynamic views in your web applications. Here, we use AngularJS to connect our web page with Express and perform actions on our book register.

*Change the directory back to ‘Books’*

    cd ../..

*Create a folder named public*

    mkdir public && cd public

*Add a file named script.js*

    vi script.js
    
*Copy and paste the Code below (controller configuration defined) into the script.js file.*
```
var app = angular.module('myApp', []);
app.controller('myCtrl', function($scope, $http) {
  $http( {
    method: 'GET',
    url: '/book'
  }).then(function successCallback(response) {
    $scope.books = response.data;
  }, function errorCallback(response) {
    console.log('Error: ' + response);
  });
  $scope.del_book = function(book) {
    $http( {
      method: 'DELETE',
      url: '/book/:isbn',
      params: {'isbn': book.isbn}
    }).then(function successCallback(response) {
      console.log(response);
    }, function errorCallback(response) {
      console.log('Error: ' + response);
    });
  };
  $scope.add_book = function() {
    var body = '{ "name": "' + $scope.Name + 
    '", "isbn": "' + $scope.Isbn +
    '", "author": "' + $scope.Author + 
    '", "pages": "' + $scope.Pages + '" }';
    $http({
      method: 'POST',
      url: '/book',
      data: body
    }).then(function successCallback(response) {
      console.log(response);
    }, function errorCallback(response) {
      console.log('Error: ' + response);
    });
  };
});
```
*In public folder, create a file named index.html;*

    vi index.html

*Copy and paste the code below into index.html file.*
```
<!doctype html>
<html ng-app="myApp" ng-controller="myCtrl">
  <head>
    <script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.6.4/angular.min.js"></script>
    <script src="script.js"></script>
  </head>
  <body>
    <div>
      <table>
        <tr>
          <td>Name:</td>
          <td><input type="text" ng-model="Name"></td>
        </tr>
        <tr>
          <td>Isbn:</td>
          <td><input type="text" ng-model="Isbn"></td>
        </tr>
        <tr>
          <td>Author:</td>
          <td><input type="text" ng-model="Author"></td>
        </tr>
        <tr>
          <td>Pages:</td>
          <td><input type="number" ng-model="Pages"></td>
        </tr>
      </table>
      <button ng-click="add_book()">Add</button>
    </div>
    <hr>
    <div>
      <table>
        <tr>
          <th>Name</th>
          <th>Isbn</th>
          <th>Author</th>
          <th>Pages</th>

        </tr>
        <tr ng-repeat="book in books">
          <td>{{book.name}}</td>
          <td>{{book.isbn}}</td>
          <td>{{book.author}}</td>
          <td>{{book.pages}}</td>

          <td><input type="button" value="Delete" data-ng-click="del_book(book)"></td>
        </tr>
      </table>
    </div>
  </body>
</html>
```
*Change the directory back up to Books*

    cd ..

*Start the server by running this command:*

    node server.js

<img width="493" alt="server" src="https://github.com/ifyyegwim/MEAN-STACK-IMPLEMENTATION/assets/134213051/6ede1791-f652-438a-8d11-7a25f546ac88">

The server is now up and running, we can connect it via port 3300. You can launch a separate Putty or SSH console to test what curl command returns locally.

    curl -s http://localhost:3300

It shall return an HTML page, it is hardly readable in the CLI, but we can also try and access it from the Internet.

For this – you need to open TCP port 3300 in your AWS Web Console for your EC2 Instance.

<img width="1013" alt="3300" src="https://github.com/ifyyegwim/MEAN-STACK-IMPLEMENTATION/assets/134213051/8846af0b-42c2-4276-88a8-1caf5e5cfdaa">

Now you can access our Book Register web application from the Internet with a browser using Public IP address or Public DNS name.

Quick reminder how to get your server’s Public IP and public DNS name:

You can find it in your AWS web console in EC2 details or Run ```curl -s http://169.254.169.254/latest/meta-data/public-ipv4``` for Public IP address or ```curl -s http://169.254.169.254/latest/meta-data/public-hostname``` for Public DNS name.

This is how your Web Book Register Application will look like in browser:

<img width="419" alt="Screenshot 2023-07-22 at 19 17 46" src="https://github.com/ifyyegwim/MEAN-STACK-IMPLEMENTATION/assets/134213051/e6e8e572-c570-49f4-a440-52d3b96a8d72">

**MEAN HAS BEEN SUCCESSFULLY UTILIZED TO DEPLOY A SIMPLE BOOK REGISTER ON AWS** 🥳
