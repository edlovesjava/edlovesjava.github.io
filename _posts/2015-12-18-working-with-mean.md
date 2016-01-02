---
layout: post
title:  "Working with MEAN: getting started"
date:   2015-12-18 17:17
categories: project intro
---

# overview

## what is the MEAN stack?

Mean is a tech stack for developing [single page web apps](https://en.wikipedia.org/wiki/Single-page_application) with [javascript](https://en.wikipedia.org/wiki/JavaScript) on [node js][nodejs]

This stack combines the following technologies:

 * [MongoDB][mongodb] - MongoDB is an open-source, document database designed for ease of development and scaling,  using [Mongoose JS][mongoosejs] used as ORM to access [MongoDB][mongodb]
 * [Express JS][expressjs] - Fast, unopinionated, minimalist web framework for [Node.js][nodejs]
 * [Angular JS][angularjs] - HTML enhanced for web apps
 * [Node JS][nodejs] - Node.js® is a JavaScript runtime built on [Chrome's V8 JavaScript engine](https://developers.google.com/v8/)

## Prerequisites

To configure and run the typical example found for instance from [starter node angular][starternodeangular] project you will need to have installed

 * [nodejs][nodejs] - to run javascript on the back end
 * [npmjs][npm] - to manage node js packages

## basic architecture

To use this technology stack it is essential to install [Node JS][nodejs] and the node package manager: [npm][npmjs]

The basic architecture relies on [node js][nodejs] to run a web server that exposes both the static site data featuring HTML with [angular][angularjs] as well as server API routes [Express][expressjs] connecting to [MongoDB][mongodb] databases via [Mongoose JS][mongoosejs] javascript based ORM.

## starter projects and generators

To get started more quickly there are saveral starter apps and generators to use.

I followed the excellent [starter node angular][starternodeangular] project on github to get started.

There are generators that help create more sophisticated production ready starters, including:

 * [MeanJS.org][meanjs]
 * [mean.io][meanio]

# example application

## Package Structure

From the [starter node angular][starternodeangular] project, we have the following basic file structure

{% highlight bash %}
./
  app/
    models/
    routes.js

  config/

  public/
    bower_components/
    scripts/
      app.js
      controllers/
      services/

    views/
    index.html

  node_modules/
  .bowerrc
  server.js
  bower.json
  package.json
  README.md

{% endhighlight  %}

Important aspects of the structure above

 * essential for [Node JS][nodejs] is the `package.json`, and `server.js` files in the project root `./`. The `server.js` references custom modules in `app/` directory by convention.
 * for [express JS][expressjs]: the `app/` contains `app/routes.js` containing the various routes needed to run the back end API of our app
 * for [MongoDB][mongodb]: the `server.js` or `app/routes.js` references model objects in `app/model/` using [Mongoose JS][mongoosejs] used as ORM to access [MongoDB][mongodb] data
 * for [Angular JS][angularjs], the `public/` running `public/index.html` containing angular directives and referencing javascript libraries supplied by bower in `./bower.json` in `public/bower_components` library as configured via `.bowerrc`

## Typical app layout and configuration

Looking at this example we can see the distiction between the back end run by [node js][nodejs] and its modules managed by [npm][npmjs], and the front-end serving [angular js][angularjs] and its dependent modules managed by [bower][bower].

### back end configuration

#### Node JS configuration

for `package.json` (example)

{% highlight json %}
{
  "name": "starter-node-angular",
  "main": "server.js",
  "dependencies": {
    "express" : "~4.5.1",
	"mongoose" : "~3.8.0",
	"body-parser" : "~1.4.2",
	"method-override" : "~2.0.2"
  }
}
{% endhighlight  %}

#### example 'main' module

The `server.js` is configured in `package.json` to run as the main script. It is used to instantiate and configure the node js , and references other modules configured in `package.json` used for specific behavior.

Note the use of `require()` to load other modules for node that should have been downloaded via npm from the package.json ready to use. `require()` is also used to load the app custom modules as well.

for `server.js` as a generic example

{% highlight javascript %}
// modules =================================================
var express        = require('express');
var app            = express();
var mongoose       = require('mongoose');
var bodyParser     = require('body-parser');
var methodOverride = require('method-override');

// configuration ===========================================

// config files
var db = require('./config/db');

var port = process.env.PORT || 8080; // set our port
// mongoose.connect(db.url); // connect to our mongoDB database (commented out after you enter in your own credentials)
mongoose.connect(db.url); // connect to our database

// get all data/stuff of the body (POST) parameters
app.use(bodyParser.json()); // parse application/json
app.use(bodyParser.json({ type: 'application/vnd.api+json' })); // parse application/vnd.api+json as json
app.use(bodyParser.urlencoded({ extended: true })); // parse application/x-www-form-urlencoded

app.use(methodOverride('X-HTTP-Method-Override')); // override with the X-HTTP-Method-Override header in the request. simulate DELETE/PUT
app.use(express.static(__dirname + '/public')); // set the static files location /public/img will be /img for users

// routes ==================================================
require('./app/routes')(app); // pass our application into our routes

// start app ===============================================
app.listen(port);
console.log('Magic happens on port ' + port);           // shoutout to the user
exports = module.exports = app;                         // expose app
{% endhighlight  %}

#### Database connection information for mongodb via mongoose

In `server.js` there is a module loaded called `/config/db`. We chose to use a seperate node module to contain the confgiutation to connect to the mongodb database.

For `./config/db.js`

{% highlight javascript %}
module.exports = {
	url : 'mongodb://localhost:27017'
}
{% endhighlight  %}

and a connection to mongodb via mongoose can be made through the script

```
mongoose.connect(db.url); // connect to our database
```

the `server.js` also sets up express loaded as `app` in this example ready to listen to the configured port. Port could be supplied by the command line or defaults to 8080.

```
var port = process.env.PORT || 8080; // set our port
```

and then start express listenting on this port with with

```
app.listen(port);
```


### front end app configuration

The front end confirguration centers around [bower][bower] package manager loading packages for [angularjs][anglarjs] run single page application.

#### Bower configuration for the front end for angular JS app

In `.bowerrc` configure to use `public/bower_components` as the library

{% highlight json %}
{
	"directory": "public/bower_components"
}
{% endhighlight  %}

for `bower.json` (example)

{% highlight json %}
{
  "name": "meantest",
  "version": "0.0.1",
  "dependencies": {
    "angular": "^1.3.0",
    "bootstrap": "^3.2.0",
    "angular-animate": "^1.3.0",
    "angular-cookies": "^1.3.0",
    "angular-resource": "^1.3.0",
    "angular-route": "^1.3.0",
    "angular-sanitize": "^1.3.0",
    "angular-touch": "^1.3.0",
     "tv4": "~1.0.15",
    "angular-sanitize": ">= 1.2",
    "objectpath": "~1.1.0"
  },
  "devDependencies": {
    "angular-mocks": "^1.3.0"

  },
  "appPath": "app",
  "moduleName": "testApp",
  "overrides": {
    "bootstrap": {
      "main": [
        "less/bootstrap.less",
        "dist/css/bootstrap.css",
        "dist/js/bootstrap.js"
      ]
    }
  }
}
{% endhighlight  %}


# essential starter app

# NEXT

 * See the [next article][next] for a look at an example application
 * Go back to the [top post][parent] to see the other articles

[samplecode2]: https://github.com/edlovesjava/ionic-todo2
[orderby]: https://docs.angularjs.org/api/ng/filter/orderBy
[nodejs]: https://nodejs.org/en/
[angularjs]: https://angularjs.org/
[mongodb]: https://www.mongodb.org/
[expressjs]: http://expressjs.com/en/index.html
[mongoosejs]: http://mongoosejs.com/
[npmjs]: https://www.npmjs.com/
[starternodeangular]: https://github.com/scotch-io/starter-node-angular
[meanjs]: http://meanjs.org/
[meanio]: http://mean.io/
[bower]: http://bower.io/

[parent]: {% post_url 2015-12-18-working-with-mean-series %}
[next]: {% post_url 2015-12-18-working-with-mean-example-app %}
