---
layout: post
title:  "Refactor and extend : use routing and main list template"
date:   2014-12-23 08:50
categories: project intro
---

## story: refactor to use angularUI router

*part of the [refactor and extend][parent] stories*

 > as a developer I wish to use a main list template for the list of tasks and render it using ionic's router so I can add additional features 

learnings featured:

 * using the [`$stateProvider` and `$urlRouterProvider`](http://learn.ionicframework.com/formulas/navigation-and-routing-part-1/) 

To use routing I will do three additional refactorings:

 1. [create one more template 'todo-list.html'](#create_todo_list) to render the main task list
 1. [use the &lt;ion-nav-view&gt; in 'index.html'](#update_index) to indicate the container where the template for the route selected must be rendered
 1. [modify 'app.js'](#update_app) to initialize the router and add a single route as default to automatically render a rout

### <a name="create_todo_list"></a>create the main 'todo-list.html'

create the task list as the following by moving the content to render the list from 'index.html' (within and including the &lt;ion-side-menus&gt; tag) to the new template 'templates/todo-list.html':


{% highlight html %}
{% raw %}
    <ion-side-menus>
      <ion-side-menu-content>
        <ion-header-bar class="bar-dark">
          <button class="button button-icon" ng-click="toggleProjects()">
            <i class="icon ion-navicon"></i>
          </button>
          <h1 class="title" ng-click="showActions()">{{activeProject.title}}</h1>
      <!-- New Task button-->
          <button class="button button-icon" ng-click="newTask()">
            <i class="icon ion-compose"></i>
          </button>          
        </ion-header-bar>      
        <ion-content>
          <ion-list>
            <ion-item class="item item-icon-right" ng-repeat="task in activeProject.tasks">
              <ion-checkbox class="item-text-wrap" style="border:none;" ng-model="task.isDone"  ng-model="task.isDone" ng-click="doneClicked($index, task)" ng-true-value="'YES'" ng-false-value="'NO'"><span>{{task.title}}</span></ion-checkbox>
              <i class="icon ion-edit" ng-click="editTask($index, task)"></i>
            </ion-item>
          </ion-list>
        </ion-content>
      </ion-side-menu-content>
      <ion-side-menu side="left">
        <ion-header-bar class="bar-dark">
          <h1 class="title">Projects</h1>
          <button class="button button-icon ion-plus" ng-click="newProject()">
          </button>
        </ion-header-bar>
        <ion-content scroll="false">
          <ion-list>
            <ion-item class="item item-icon-right" ng-repeat="project in projects" ng-class="{active: activeProject == project}">
              <i class="icon ion-edit" ng-click="editProject($index, project)"></i>
              <span ng-click="selectProject(project, $index)">{{project.title}}</span>
            </ion-item>
          </ion-list>
        </ion-content>
      </ion-side-menu>

    </ion-side-menus>


{% endraw %}
{% endhighlight %}

### <a name="update_index"></a>index.html

After moving out the list into its own template, this is all that should remain in the index.html Notice the addition of the &lt;ion-nav-view&gt; tag where the router will render the correct page depending on the route selected. Also the controller is no longer added to the body, ust the `ng-app` the router will combine the correct controller to the correct template when the route is selected. 


{% highlight html %}
{% raw %}

<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <meta name="viewport" content="initial-scale=1, maximum-scale=1, user-scalable=no, width=device-width">
    <title>Todo</title>
    <link href="lib/ionic/css/ionic.css" rel="stylesheet">
    <link href="css/style.css" rel="stylesheet">
    <!-- IF using Sass (run gulp sass first), then uncomment below and remove the CSS includes above
    <link href="css/ionic.app.css" rel="stylesheet">
    -->
    <!-- ionic/angularjs js -->
    <script src="lib/ionic/js/ionic.bundle.js"></script>
   <script src="js/app.js"></script>
   <script src="js/services.js"></script>
   <script src="js/controllers.js"></script>
    <!-- cordova script (this will be a 404 during development) -->
    <script src="cordova.js"></script>
  </head>
  <body ng-app="todo"> 
        <ion-nav-view></ion-nav-view>
  </body>
</html>


{% endraw %}
{% endhighlight %}

### <a name="update_app"></a>'app.js'

Now we will add the routing information that tells angular to define a route /list to the `$stateProvider` using the template 'todo-list.html' and declare it with the 'todoCtrl' . Then we will make sure that this one route is selected on default using the `otherwise` method of the $urlRouteProvider
{% highlight javascript %}
{% raw %}

// Todo App

// angular.module is a global place for creating, registering and retrieving Angular modules
// 'starter' is the name of this angular module example (also set in a <body> attribute in index.html)
// the 2nd parameter is an array of 'requires'
// 'starter.services' is found in services.js
// 'starter.controllers' is found in controllers.js
angular.module('todo', ['ionic', 'todo.services', 'todo.controllers'])

  .config(function($stateProvider, $urlRouterProvider) {
  
    // Ionic uses AngularUI Router which uses the concept of states
    // Learn more here: https://github.com/angular-ui/ui-router
    // Set up the various states which the app can be in.
    // Each state's controller can be found in controllers.js
    $stateProvider
  
    .state('home', {
      url: '/',
      templateUrl: 'templates/todo-list.html',
      controller: 'todoCtrl'
        });
  
    // if none of the above states are matched, use this as the fallback
    $urlRouterProvider.otherwise('/');
  
  });


{% endraw %}
{% endhighlight %}


*the code for this can be found on [my github page][samplecode2]*

[parent]: {% post_url 2014-12-23-refactor-and-extend %}
[samplecode2]: https://github.com/edlovesjava/ionic-todo2

