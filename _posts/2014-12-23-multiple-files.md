---
layout: post
title:  "Refactor and extend : refactor to multiple files"
date:   2014-12-23 08:34
categories: project intro
---

## story: refactor to multiple files

*part of the [refactor and extend][parent] stories*

 > as a developer I would like to use multiple files for javascript and templates, using one file per class, including controllers, servies and the main application for javascript and one file per template

learings featured:

 * using [angular modules](https://docs.angularjs.org/guide/module) to load controllers and services across separate files

In this project so far there is only one service (Project) and one controller (taskCtrl) that we need to organize. So we will create one file per type ('services.js' and 'controllers.js'). We can break this up further if needed at some other date.

So to break up javascript, we will have in the js directory

 * js/app.js
 * js/services.js
 * js/controllers.js

For templates, each template will be in its own file, including the main template to display the list of tasks. This is the easiest configuration for loading these tempaltes.

 * templates/add-task.html
 * templates/edit-task.html
 * templates/edit-project.html

steps:

 1. [break up javascript](#break_up_javascript)
    1. [create 'services.js'](#create_services)
    1. [create 'controllers.js'](#create_controllers)
    1. [remaining 'app.js'](#remaining_app)
 2. [break up templates as separate files](#break_up_templates)
    1. [create 'new-task.html'](#create_new_task)
    1. [create 'edit-task.html'](#create_edit_task)
    1. [remaining 'index.html'](#remaining_index)

### <a name="break_up_javascript"></a>breakup the javascript


#### <a name="create_services"></a>services.js

created in the js directory

The factory for 'Projects' is moved to the services.js 

{% highlight javascript %}
{% raw %}

angular.module('todo.services', [])
/**
 * The Projects factory handles saving and loading projects
 * from local storage, and also lets us save and load the
 * last active project index.
 */
.factory('Projects', function() {
  return {
    all: function() {
      var projectString = window.localStorage['projects'];
      if(projectString) {
        return angular.fromJson(projectString);
      }
      return [];
    },
    save: function(projects) {
      window.localStorage['projects'] = angular.toJson(projects);
    },
    newProject: function(projectTitle) {
      // Add a new project
      return {
        title: projectTitle,
        tasks: []
      };
    },
    getLastActiveIndex: function() {
      return parseInt(window.localStorage['lastActiveProject']) || 0;
    },
    setLastActiveIndex: function(index) {
      window.localStorage['lastActiveProject'] = index;
    }
  }
})
{% endraw %}
{% endhighlight %}

_read more about [angular factory type of provider](https://docs.angularjs.org/guide/providers)_

#### <a name="create_controllers"></a>controllers.js

created in the js directory

The controller is moved to the 'controllers.js'

{% highlight javascript %}
{% raw %}

angular.module('todo.controllers', [])
.controller('todoCtrl', function($scope, $ionicModal, Projects, $ionicSideMenuDelegate, $timeout, $ionicPopup, $ionicActionSheet) {
 
  // Create and load the Modal for new task
  $ionicModal.fromTemplateUrl('templates/new-task.html', function(modal) {
    $scope.taskModal = modal;
  }, {
    scope: $scope,
    animation: 'slide-in-up'
  });
  // Edit and load the Modal for edit task
  $ionicModal.fromTemplateUrl('templates/edit-task.html', function(modal) {
    $scope.editTaskModal = modal;
  }, {
    scope: $scope,
    animation: 'slide-in-up'
  });

  // Load or initialize projects
  $scope.projects = Projects.all();

  // A utility function for creating a new project
  // with the given projectTitle
  var createProject = function(projectTitle) {
    var newProject = Projects.newProject(projectTitle);
    $scope.projects.push(newProject);
    Projects.save($scope.projects);
    $scope.selectProject(newProject, $scope.projects.length-1);
  };

  // Grab the last active, or the first project
  $scope.activeProject = $scope.projects[Projects.getLastActiveIndex()];

  // Called to create a new project
  $scope.newProject = function() {
    var projectTitle = prompt('Project name');
    if(projectTitle) {
      createProject(projectTitle);
    }
  };

  // Called to select the given project
  $scope.selectProject = function(project, index) {
    $scope.activeProject = project;
    Projects.setLastActiveIndex(index);
    $ionicSideMenuDelegate.toggleLeft(false);
  };


  // Called when the form is submitted
  $scope.createTask = function(task) {
    if (!$scope.activeProject || !task) {
      return;
    }
    $scope.activeProject.tasks.push({
      title: task.title
    });
    $scope.taskModal.hide();

    // Inefficient, but save all the projects
    Projects.save($scope.projects);

    task.title = "";
  };

// Called when the form is submitted
  $scope.updateTask = function(i, task) {
    if (!$scope.activeProject || !task) {
      return;
    }
    $scope.activeProject.tasks[i] = task;
    $scope.editTaskModal.hide();

    // Inefficient, but save all the projects
    Projects.save($scope.projects);

  };

  // Open our new task modal
  $scope.newTask = function() {
    $scope.task = {title:"", isDone:"NO"};
    $scope.taskModal.show();
  };

  // Open our new task modal
  $scope.editTask = function(i, task) {
    $scope.task = {title: task.title, isDone: task.isDone};
    $scope.taskIndex = i;
    $scope.editTaskModal.show();
  };

  // Make sure to persist the change after is done is toggled
  $scope.toggleDone = function(i, task) {
    //alert("toggle done task "+task.isDone)
    if (!$scope.activeProject || !task) {
      return;
    }
    $scope.activeProject.tasks[i].isDone = ($scope.activeProject.tasks[i].isDone=="YES")?"NO":"YES";
    Projects.save($scope.projects);
  }

  // Make sure to persist the change after is done is toggled
  $scope.doneClicked = function(i, task) {
    //alert("toggle done task "+task.isDone)
    if (!$scope.activeProject || !task) {
      return;
    }
    Projects.save($scope.projects);
  }

  // A confirm dialog
  $scope.showConfirm = function(onYes, onNo) {
   var confirmPopup = $ionicPopup.confirm({
     title: 'Delete Task',
     template: 'Are you sure you want to delete this task?'
   });
   confirmPopup.then(function(res) {
     if(res) {
       onYes();
     } else {
       if (onNo)
        onNo();
     }
   });
  };

  // delete selected task
  $scope.deleteTask = function(i, task) {
    if (!$scope.activeProject || !task ) {
      return;
    }
    console.log("start deleting");
    $scope.showConfirm(function() {
      console.log("confirmed to delete task "+i);
      $scope.activeProject.tasks.splice(i,1);
      Projects.save($scope.projects);
    });
  } 

  // Close the new task modal
  $scope.closeNewTask = function() {
    $scope.taskModal.hide();
  };

  // Close the edit task modal
  $scope.closeEditTask = function() {
    $scope.editTaskModal.hide();
  };

  // Try to create the first project, make sure to defer
  // this by using $timeout so everything is initialized
  // properly
  $timeout(function() {
    if($scope.projects.length == 0) {
      while(true) {
        var projectTitle = prompt('Your first project title:');
        if(projectTitle) {
          createProject(projectTitle);
          break;
        }
      }
    }
  });
});

{% endraw %}
{% endhighlight %}


#### <a name="remaining_app"></a>update 'app.js' 

this is all that remains, for now, after the services and controllers are moved out

{% highlight javascript %}
{% raw %}

angular.module('todo', ['ionic'])

{% endraw %}
{% endhighlight %}

### <a name="break_up_templates"></a>break up the templates as seprate files

create into 'templates' directory 


#### <a name="create_new_task"></a>create the 'new-task.html'

move the contents of the script tag that creates the 'new-tasks.html' into its own separate file in 'templates' directory and remove the script tag from 'index.html'

{% highlight html %}
{% raw %}
  <div class="modal">

    <!-- Modal header bar -->
    <ion-header-bar class="bar-secondary">
      <h1 class="title">New Task</h1>
      <button class="button button-clear button-positive" ng-click="closeNewTask()">Cancel</button>
    </ion-header-bar>

    <!-- Modal content area -->
    <ion-content>

      <form ng-submit="createTask(task)">
        <div class="list">
          <label class="item item-input">
            <input type="text" placeholder="What do you need to do?" ng-model="task.title">
          </label>
        </div>
        <div class="padding">
          <button type="submit" class="button button-block button-positive">Create Task</button>
        </div>
      </form>

    </ion-content>

  </div>
  
{% endraw %}
{% endhighlight %}

#### <a name="create_edit_task"></a>create the 'edit-task.html'

move the contents of the script tag that creates the 'edit-task.html' template to its own file 'templates/edit-task.html' and remove the script tag from 'index.html'

{% highlight html %}
{% raw %}

  <div class="modal">

    <!-- Modal header bar -->
    <ion-header-bar class="bar-secondary">
      <h1 class="title">Edit Task</h1>
      <button class="button button-clear button-positive" ng-click="closeEditTask()">Cancel</button>
    </ion-header-bar>

    <!-- Modal content area -->
    <ion-content>

      <form ng-submit="updateTask(taskIndex, task)">
        <div class="list">
          <label class="item item-input">
            <input type="text" placeholder="What do you need to do?" ng-model="task.title">
          </label>
          <ion-checkbox ng-model="task.isDone"  ng-model="task.isDone" ng-true-value="'YES'" ng-false-value="'NO'">task is done</ion-checkbox>
        </div>
        <div class="padding">
          <button type="submit" class="button button-block button-positive">Update Task</button>
        </div>
        <div class="padding">
          <button t class="button button-block button-assertive" ng-click="deleteTask(taskIndex, task)">Delete Task</button>
        </div>
      </form>

    </ion-content>

  </div>
{% endraw %}
{% endhighlight %}

#### <a name="remaining_index"></a>remaining in 'index.html'

notice that the index.html doesn't have any additional scripts for templates. Also we have added script tags to load  'js/services.js' and 'js/controllers.js'.


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
  <body ng-app="todo" ng-controller="todoCtrl"> 
    <ion-side-menus>
      <ion-side-menu-content>
        <ion-header-bar class="bar-dark">
          <button class="button button-icon" ng-click="toggleProjects()">
            <i class="icon ion-navicon"></i>
          </button>
          <h1 class="title">{{activeProject.title}}</h1>
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
            <ion-item ng-repeat="project in projects" ng-click="selectProject(project, $index)" ng-class="{active: activeProject == project}">
              {{project.title}}
            </ion-item>
          </ion-list>
        </ion-content>
      </ion-side-menu>
    </ion-side-menus>
  </body>
</html>

{% endraw %}
{% endhighlight %}

*the code for this can be found on [my github page][samplecode2]*

[next article in series][next]

[next]: {% post_url 2014-12-23-use-routing %}
[parent]: {% post_url 2014-12-23-refactor-and-extend %}
[samplecode2]: https://github.com/edlovesjava/ionic-todo2