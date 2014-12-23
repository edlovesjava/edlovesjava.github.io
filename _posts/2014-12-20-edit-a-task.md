---
layout: post
title:  "Expanding on todo : edit a task story"
date:   2014-12-20 16:34
categories: project intro
---

## story: edit a task 

This is part of a series [extending ionic todo example][parent]

 > As a user I want the ability to edit a task after it is added. 

 To do so, I thought it would be ok to add an icon to the right side of the task using the ion-edit icon (pencil).



### given

{% highlight html %}
{% raw %}
...
          <ion-list>
            <ion-item class="item" ng-repeat="task in activeProject.tasks">
              {{task.title}}
            </ion-item>
          </ion-list>
 ...
{% endraw %}
{% endhighlight %}

### changed to 

{% highlight html %}
{% raw %}
...
          <ion-list>
            <ion-item class="item" ng-repeat="task in activeProject.tasks">
              {{task.title}}
              <i class="icon ion-edit" ng-click="editTask($index, task)"></i>
            </ion-item>
          </ion-list>
 ...
{% endraw %}
{% endhighlight %}

And I also added a template after the new task template called 'edit-task.html'

{% highlight html %}
{% raw %}

<script id="edit-task.html" type="text/ng-template">

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
        </div>
        <div class="padding">
          <button type="submit" class="button button-block button-positive">Update Task</button>
        </div>

      </form>

    </ion-content>

  </div>
</script> 

{% endraw %}
{% endhighlight %}

Notice the addtion of:

 * class `item-icon-right` in `ion-item` tag 
 * addition of the &lt;i&gt; tag
 * the &lt;i&gt; tag has the addition of the `ng-click` calling `editTask` on click of the icon
 * the 'edit-task.html' template calls the `updateTask` function on submit

### loading the 'edit-task.html' template

*added after loading the template: 'new-task.html' in the 'app.js' file *

{% highlight javascript %}
{% raw %}

  // Edit and load the Modal
  $ionicModal.fromTemplateUrl('edit-task.html', function(modal) {
    $scope.editTaskModal = modal;
  }, {
    scope: $scope,
    animation: 'slide-in-up'
  });

{% endraw %}
{% endhighlight %} 

 This smippet loads the template added previosly in the &lt;script&gt; tag with the id 'edit-task.html' into the scope variable `editTaskModal`. 


### adding the `editTask` method to 'app.js'

*added aftetr newTask method*

{% highlight javascript %}
{% raw %}

  // Open our new task modal
  $scope.editTask = function(i, task) {
    $scope.task = {title: task.title, isDone: task.isDone};
    $scope.taskIndex = i;
    $scope.editTaskModal.show();
  };

{% endraw %}
{% endhighlight %} 

 this snippet shows when the `editTask` method is called, a new object with an empty task is created, and the `editTaskModal` is shown

### adding the `updateTask` method to 'app.js'

*added after the createTask method*

{% highlight javascript %}
{% raw %}

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
  
{% endraw %}
{% endhighlight %}  

 The `updateTask` function is responsible for first checking if the scope variables for activeProject and parameter task have values. Then the task object passed as a parameter from the template is set on the ith element of the `activeProject.tasks` array. 
 
 When `editTask` function is called to open the `editTaskModal` modal dialog, the angular variable $index is used in the html to identify the index of the item populated using an `ng-repeat` directive. This index is then set in the scope variable itemIndex for the template to use on submit calling `updateTask` function. 


[parent]:   {% post_url 2014-12-20-expand-the-ionic-example %}