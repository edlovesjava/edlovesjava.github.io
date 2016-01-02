---
layout: post
title:  "Working with MEAN: example app"
date:   2015-12-18 19:17
categories: project intro
---

# essential starter app

As shown above `server.js` is started when the project is run via node js to configure, connect to the data and expose the API. The `index.html' and javascript in the public directory contain all of the front end.

## The database

Also as shown abovet he specific configruation of the database connection is configured withihn `config/db.js` that is included from `server.js`. The connection is established using mongoose api. Not shown in this example is using the mongoose ORM features via model objects. These model objects provide the CRUD functionality and schema defintion of objects stored and retrieved in mongodb.

## The API

The REST API configured for this application is supported by the [expressjs][expressjs] module allowing us to create end points to GET, PUT, POST and DELETE resources, set up as 'routes'. This example application delegates to the module: `./app/routes.js` to set these up. The first route that we create is to serve up the static content that contains the [bower][bower] managed javascript and custom javascript referenced by the `index.html` contained in the `./public` directory space.

from `server.js` express routes are loaded passign the express module `app` to configure

{% highlight javascript %}
// routes ==================================================
require('./app/routes')(app); // pass our application into our routes
{% endhighlight  %}

in `./app/route.js` the routes are exposed. The default route exposes `./public/index.html`, which is used to reference all other static content in the `./public` directory space.

{% highlight javascript %}
module.exports = function(app) {


    // server routes ===========================================================
    // handle things like api calls
    // authentication routes

    // test route to make sure everything is working (accessed at GET http://localhost:8080/api)

    // frontend routes =========================================================
    // route to handle all angular requests
    app.get('*', function(req, res) {
        res.sendfile('./public/index.html');
    });

};
{% endhighlight  %}

Notice at minimum this serves the `index.html` file as the single page from which  all of the application javascript via angular is run. It would aso be expanded to expose the REST API (rest endpoints) which the front end iteracts with to exposed services and data.

## the front end

# NEXT

 * See the [previous article][previous] for a look at a the basic structure and configuration of the MEAN application
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
[previous]: {% post_url 2015-12-18-working-with-mean %}
