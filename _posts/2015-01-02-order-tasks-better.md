---
layout: post
title:  "Refactor and extend : improved order tasks by most recently added story"
date:   2015-01-02 17:07
categories: project intro
---

## story: show tasks in order of most recently added

*part of the [refactor and extend][parent] stories*

 > as a user I wish to show tasks in most recently added order

learnings featured:

 * using [angular's `orderBy` filter in a directive][orderby]

In the first approach we simply ordered the array of tasks before saving. This is pretty inefficient for larger data, but very simple to implement. It also suffers from relying on the display index to be equal to the array index so we cant use pure client side sorting and filtering on the list such as using orderBy as a directive.

In this post, a more flexible approach is used be to make the display order independent of the order of the saved data. This requires storing and retrieving tasks using something like a primary key. 

## Steps

 1. [create a sequence for the primary key](#create_sequence)
 1. [set the id on save of a new tasks](#save_id)  
 2. [find the index using the id ](#find_index) creating `findTaskIndex` method
 1. [use `findTaskIndex` instead of using the `$index`](#use_find_index) value in editing, deleting tasks and marking a task done
 1. [remove the previously implemented `orderProjectTasks`](#remove_order_tasks) and its calls--this is no longer needed
 1. [use the `orderBy` directive in 'todo-list.html' template](#update_todo_template) to use client side ordering
 1. [refactor calls to `editTask`, `updateTask`, `doneClicked`](#refactor_calls) no longer requiring pasing in the index


### <a name="create_sequence"></a>create a sequence for the primary key

create `getNextKeyValue` method in Projects service in 'services.js'

this method will persist a key sequence that will be used when creating tasks that will be unique for the local storage to be able to find tasks again by this id

{% highlight javascript %}
{% raw %}

    getNextKeyValue: function() {
      var lastKeyValue = parseInt(window.localStorage['lastKeyValue']) || 0;
      window.localStorage['lastKeyValue'] = ++lastKeyValue;
      return lastKeyValue;
    }

{% endraw %}
{% endhighlight %}


### <a name="save_id"></a>set the unique id on save of a new tasks

Modify `createTask` of 'controllers.js' to set a 'id' field of the ceated task using `Projects.getNextKeyValue`

{% highlight javascript %}
{% raw %}

  // Called when the form is submitted
  $scope.createTask = function(task) {
    if (!$scope.activeProject || !task) {
      return;
    }
    $scope.activeProject.tasks.push({
      id : Projects.getNextKeyValue(),
      title: task.title,
      isDone: 'NO',
      createDate: (new Date()).toISOString()
    });
    $scope.taskModal.hide();

    // Inefficient, but save all the projects
    $scope.orderProjectTasks($scope.activeProject);
    Projects.save($scope.projects);
    task.title = "";
  };

{% endraw %}
{% endhighlight %}

Modify `editTask` to pass in and preserve id when editing

{% highlight javascript %}
{% raw %}

  // Open our new task modal
  $scope.editTask = function(i, task) {
    $scope.task = {title: task.title, isDone: task.isDone, createDate: task.createDate, id : task.id};
    $scope.taskIndex = i;
    $scope.editTaskModal.show();
  };


{% endraw %}
{% endhighlight %}

You can test to make sure that the id is both added and preserved. The saved __json__ created from adding two tasks to one project should look something like this:

{% highlight json %}
{% raw %}

[
   {
      "title":"new project",
      "tasks":[
         {
            "id":1,
            "title":"task one",
            "isDone":"NO",
            "createDate":"2015-01-03T16:01:06.082Z"
         },
         {
            "id":2,
            "title":"task two",
            "isDone":"NO",
            "createDate":"2015-01-03T16:01:10.913Z"
         }
      ]
   }
]

{% endraw %}
{% endhighlight %}

### <a name="find_index"></a>find the index using the id creating `findTaskIndex` method

add `findTaskIndex` to 'controllers.js'

this method loops through the given array of tasks and returns the index of the task with the given id, or -1 if no task with that id is found

{% highlight javascript %}
{% raw %}

// return index of the task with given id
  $scope.findTaskIndex = function(tasks, id) {
    for(var i=0;i<tasks.length;i++) {
      if (tasks[i].id == id) return i;
    }
    return -1;
  }

{% endraw %}
{% endhighlight %}


### <a name="use_find_index"></a>use `findTaskIndex` instead of using the `$index` value in editing and deleting tasks 

modify `updateTask` of 'controllers.js' to use `findTaskIndex` instead of supplied index

{% highlight javascript %}
{% raw %}

// Called when the form is submitted
  $scope.updateTask = function(i, task) {
    if (!$scope.activeProject || !task) {
      return;
    }
    var taskIndex = $scope.findTaskIndex($scope.activeProject.tasks, task.id);
    $scope.activeProject.tasks[taskIndex] = task;
    $scope.editTaskModal.hide();

    // Inefficient, but save all the projects
    $scope.orderProjectTasks($scope.activeProject);
    Projects.save($scope.projects);

  };
{% endraw %}
{% endhighlight %}

modify `deleteTask` of 'controllers.js' to use `findTaskIndex` instead of supplied index


{% highlight javascript %}
{% raw %}

  // delete selected task
  $scope.deleteTask = function(i, task) {
    if (!$scope.activeProject || !task ) {
      return;
    }
    $scope.showConfirm('Delete Task', 'Are you sure you want to delete this task?', function() {
      var taskIndex = $scope.findTaskIndex($scope.activeProject.tasks, task.id);
      $scope.activeProject.tasks.splice(taskIndex,1);
      Projects.save($scope.projects);
    });
  } 

{% endraw %}
{% endhighlight %}


you can now test to make sure that the edit, toggle done and delete actions still work. but sine we are not sorting any differently then little will be different.

### <a name="remove_order_tasks"></a>remove the previously implemented `orderProjectTasks` and its calls--this is no longer needed

Now we can remove all calls and implementation of previously added `orderProjectTasks` that sorted the tasks on save. An dwe will replace this with using the `orderBy` directive in the next step

Remove `orderProjectTasks` call from `createTask` and `updateTask` in 'controllers.js'


{% highlight javascript %}
{% raw %}

  // Called when the form is submitted
  $scope.createTask = function(task) {
    if (!$scope.activeProject || !task) {
      return;
    }
    $scope.activeProject.tasks.push({
      id : Projects.getNextKeyValue(),
      title: task.title,
      isDone: 'NO',
      createDate: (new Date()).toISOString()
    });
    $scope.taskModal.hide();

    // Inefficient, but save all the projects
    Projects.save($scope.projects);
    task.title = "";
  };

{% endraw %}
{% endhighlight %}

{% highlight javascript %}
{% raw %}

// Called when the form is submitted
  $scope.updateTask = function(i, task) {
    if (!$scope.activeProject || !task) {
      return;
    }
    var taskIndex = $scope.findTaskIndex($scope.activeProject.tasks, task.id);
    $scope.activeProject.tasks[taskIndex] = task;
    $scope.editTaskModal.hide();

    // Inefficient, but save all the projects
    Projects.save($scope.projects);

  };
{% endraw %}
{% endhighlight %}

you can test again and see that things still work but we are not ordering tasks to the most recently added at top anymore

### <a name="update_todo_template"></a>use the `orderBy` function in 'todo-list.html' template to use client side ordering

modify 'todo-list.html' template to use the `orderBy` function in the `ng-repeat` directive

{% highlight html %}
{% raw %}

<ion-item class="item item-icon-right" ng-repeat="task in activeProject.tasks | orderBy : predicate: reverse">

{% endraw %}
{% endhighlight %}

now we should see the order again

### <a name="refactor_calls"></a>refactor calls to `editTask`, `updateTask`, `doneClicked` no longer requiring pasing in the index

a final clean  up is to stop passing the `$index` value into functions `editTask`, `updateTask`, `toggleDone` and remove this as a parameter.

modify 'todo-list.html' template to remove passing `$index` to calls to `editTask` and `doneClicked`

{% highlight html %}
{% raw %}

  ...
          <ion-list>
            <ion-item class="item item-icon-right" ng-repeat="task in activeProject.tasks | orderBy : predicate: reverse">
              <ion-checkbox ng-class="{complete: task.isDone == 'YES'}" class="item-text-wrap" style="border:none;" ng-model="task.isDone"  ng-model="task.isDone" ng-click="doneClicked(task)" ng-true-value="'YES'" ng-false-value="'NO' "><span>{{task.title}}</span></ion-checkbox>
                <i class="icon ion-edit" ng-click="editTask(task)" ></i>
            </ion-item>
          </ion-list>
  ...

{% endraw %}
{% endhighlight %}


modify 'edit-task.html' template to stop passing in the `taskIndex` in the case of calls to `updateTask` or `deleteTask`

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

      <form ng-submit="updateTask(task)">
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
          <button t class="button button-block button-assertive" ng-click="deleteTask(task)">Delete Task</button>
        </div>
      </form>

    </ion-content>

  </div>

{% endraw %}
{% endhighlight %}
  
modify `editTask` of 'controllers.js' to no longer take the `i` parameter and not preserving it as `taskIndex` scope variable

{% highlight javascript %}
{% raw %}


  // Open our new task modal
  $scope.editTask = function(task) {
    $scope.task = {title: task.title, isDone: task.isDone, createDate: task.createDate, id : task.id};
    $scope.editTaskModal.show();
  };

{% endraw %}
{% endhighlight %}

modify `doneClicked` of 'controllers.js' to remove the `i` parameter

{% highlight javascript %}
{% raw %}

  // Make sure to persist the change after is done is toggled
  $scope.doneClicked = function(task) {
    //alert("toggle done task "+task.isDone)
    if (!$scope.activeProject || !task) {
      return;
    }
    Projects.save($scope.projects);
  }

{% endraw %}
{% endhighlight %}

modify `updateTask` of 'controllers.js' to remove the `i` parameter

{% highlight javascript %}
{% raw %}

  // Called when the form is submitted
  $scope.updateTask = function(task) {
    if (!$scope.activeProject || !task) {
      return;
    }
    var taskIndex = $scope.findTaskIndex($scope.activeProject.tasks, task.id);
    $scope.activeProject.tasks[taskIndex] = task;
    $scope.editTaskModal.hide();

    // Inefficient, but save all the projects
    Projects.save($scope.projects);

  };

{% endraw %}
{% endhighlight %}

modify `deleteTask` of 'controllers.js' to remove the `i` parameter

{% highlight javascript %}
{% raw %}

  // delete selected task
  $scope.deleteTask = function(task) {
    if (!$scope.activeProject || !task ) {
      return;
    }
    $scope.showConfirm('Delete Task', 'Are you sure you want to delete this task?', function() {
      var taskIndex = $scope.findTaskIndex($scope.activeProject.tasks, task.id);
      $scope.activeProject.tasks.splice(taskIndex,1);
      Projects.save($scope.projects);
    });
  } 


{% endraw %}
{% endhighlight %}

*the code for this can be found on [my github page][samplecode2]*


[parent]: {% post_url 2014-12-23-refactor-and-extend %}
[samplecode2]: https://github.com/edlovesjava/ionic-todo2
[orderby]: https://docs.angularjs.org/api/ng/filter/orderBy