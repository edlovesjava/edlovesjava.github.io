---
layout: post
title:  "Expanding on todo : delete a task story"
date:   2014-12-23 08:00
categories: project intro
---

## story: delete a task

This is part of a series [extending ionic todo example][parent]

 > as a user I wish to delete a task

This story allows the user to delete a task by opening the edit dialog and selecting the delete button. The user must confirm the delete.

learnings featured:

 * using [`$ionicPopup`][ionicpopup] 


### modifying the 'edittask.html' template added to 'index.html'

in the [previous post][editatask]  I added an edit icon (from [ionic icons](http://ionicons.org)) that when clicked would edit an item by opening a modal dialog. 

Now I will add to that form by adding a delete button

{% highlight html %}
{% raw %}
...
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
...
{% endraw %}
{% endhighlight %}

the &lt;button&gt; tag uses classes button-assertive to show in red. the `ng-click` calls the `deleteTask` method, passign in the index previously set when openning the dialog as `taskIndex`, and the task itself.

Here is the `deleteTask` method in the 'app.js' file:

{% highlight javascript %}
{% raw %}

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

{% endraw %}
{% endhighlight %}

This method first calls the `showConfirm` method passing in the function to call when the 'yes' button is pressed.

The `showConfirm` method takes two parameters, the function callback on 'yes' and the function to call back on 'no' which could be null. The `showConfirm` method requires injecting the [`$ionicPopup`][ionicpopup]  to be injected into 'app.js'.

{% highlight javascript %}
{% raw %}
  .controller('TodoCtrl', function($scope, $ionicModal, Projects, $ionicSideMenuDelegate, $timeout, $ionicPopup) {
{% endraw %}
{% endhighlight %}

The `ionicPopup` is used in the `showConfirm` method below:

{% highlight javascript %}
{% raw %}


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

{% endraw %}
{% endhighlight %}

I followed the documentation and example for the [ionic popup][ionicpopup] but used callbacks instead of embedding the code to execute on the button pressed.

*the code sample can be found on [my github page][codesample]*

[next article in series][next]

[parent]:   {% post_url 2014-12-20-expand-the-ionic-example %}
[editatask]: {% post_url 2014-12-20-edit-a-task %}
[next]: {% post_url 2014-12-23-refactor-and-extend %}
[codesample]: https://github.com/edlovesjava/ionic-todo
[ionicpopup]: http://ionicframework.com/docs/api/service/$ionicPopup/