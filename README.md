# MEAN-STACK-IMPLEMENTATION
A MEAN stack is a bundle of four different software technologies that developers use to build websites and web applications. MEAN is an acronym for the NoSQL document database, MongoDB; the server side/backend, Express.js; the client side/frontend, Angular.js; and the Javascript runtime environment, Node.js. This technology stack allows the creation of a 3-tier architecture that includes frontend, backend, and database using JavaScript and JSON. We'll be utilizing this technology stack to build a simple book register webform. Before attempting this project, I had already created an AWS account and launched an instance running Ubuntu Server OS. The steps to build a book register webform with MEAN are as follows;

**STEP 1 - SET UP AN SSH AND CONNECT TO UBUNTU SERVER ON AWS VIA TERMINAL**

**STEP 2 - INSTALL NODEJS**

Node.js is a JavaScript runtime built on Chrome’s V8 JavaScript engine. Node.js is used in this tutorial to set up the Express routes and AngularJS controllers.

*Update ubuntu*

    sudo apt update

*Upgrade ubuntu*

    sudo apt upgrade

*Add certificates*

    sudo apt -y install curl dirmngr apt-transport-https lsb-release ca-certificates

    curl -sL https://deb.nodesource.com/setup_18.x | sudo -E bash -

*Install NodeJS*

    sudo apt install -y nodejs

**STEP 2- INSTALL MONGODB**

MongoDB stores data in flexible, JSON-like documents. Fields in a database can vary from document to document and data structure can be changed over time. For our example application, we are adding book records to MongoDB that contain book name, isbn number, author, and number of pages.

    sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 0C49F3730359A14518585931BC711F9BA15703C6
    echo "deb [ arch=amd64 ] https://repo.mongodb.org/apt/ubuntu trusty/mongodb-org/3.4 multiverse" | sudo tee 
    /etc/apt/sources.list.d/mongodb-org-3.4.list

*Install MongoDB*

    sudo apt install -y mongodb

*Start The server*

    sudo service mongodb start

*Verify that the service is up and running*

    sudo systemctl status mongodb

*Install npm – Node package manager*

    sudo apt install -y npm

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

