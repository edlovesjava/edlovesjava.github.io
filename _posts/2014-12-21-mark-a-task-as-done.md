---
layout: post
title:  "Expanding on todo : mark a task as done story"
date:   2014-12-21 08:34
categories: project intro
---

## story: mark a task as done

This is part of a series [extending ionic todo example][parent]

learnings featured:

 * [&lt;ion-checkbox&gt; directive](http://ionicframework.com/docs/api/directive/ionCheckbox/)

### story description

 > as a user I wish to mark a task as done

This story allows the user to mark a task as done. The UX design is to use a check box to the left of the item in the list that a user can check to indicate the task is done. Also the user should be able to mark a task done when editing the task.

The information will persist in the following form:

{% highlight json %}
{% raw %}
[
   {
      "title":"first project",
      "tasks":[
         {
            "title":"go to funeral",
            "isDone":"YES"
         },
         {
            "title":"pick up sis",
            "isDone":"YES"
         },
         {
            "title":"another added task",
            "isDone":"YES"
         },
         {
            "title":"go shopping"
         }
      ]
   },
   {
      "title":"next project",
      "tasks":[
         {
            "title":"next project next task 1"
         },
         {
            "title":"next task 2",
            "isDone":"YES"
         }
      ]
   }
]

{% endraw %}
{% endhighlight %}

### staring point modifying 'index.html'

in the [previous post][editatask]  I added an icon that when clicked would edit an item by opening a modal dialog. 


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

We will add a checkbox to the left to indicate the task is done

### modify 'index.html' to add a checkbox

{% highlight html %}
{% raw %}
...
<ion-list>
  <ion-item class="item" ng-repeat="task in activeProject.tasks">
    <ion-checkbox class="item-text-wrap" style="border:none;" ng-model="task.isDone"  ng-model="task.isDone" ng-click="doneClicked($index, task)" ng-true-value="'YES'" ng-false-value="'NO'"><span>{{task.title}}</span></ion-checkbox>
    <i class="icon ion-edit" ng-click="editTask($index, task)"></i>
  </ion-item>
</ion-list>
 ...
{% endraw %}
{% endhighlight %}

The `ion-checkbox` directive came to mind first (see [the ionic documentation](http://ionicframework.com/docs/api/directive/ionCheckbox/)), but it did not look correct since there was a box around the input that clashed with the display of the item. I finally used a `style='boder:none;'` to remove this. Also, i noticed that if the task was too long it used elipse, but I thought wrap would be better so I added the class 'item-text-wrap' and that did the trick.

Also, notice that I am explicitly setting the value of `isDone` to 'YES' or 'NO' using the `ng-true-value` and `ng-false-value` to match the persisted json described above. 

When the check box is checked I wanted to assure that the change was persisted so I added an `ng-click` event to call `doneClicked($index, task)` as usual I am passing `$index` variable indicating the index of the tasks array that will be modified. I am also passing `task` to be consistent (but not necessary).

I then added the `doneClicked` method to 'app.js' to persist the changes

*added after the `editTask` method*

{% highlight javascript %}
{% raw %}

  // Make sure to persist the change after is done is toggled
  $scope.doneClicked = function(i, task) {
    //alert("toggle done task "+task.isDone)
    if (!$scope.activeProject || !task) {
      return;
    }
    Projects.save($scope.projects);
  }

{% endraw %}
{% endhighlight %}

this method simply checks if the parameters are correct and if so, persistes the change to the projects using `Projects.save` method.

### Alternate approach to a checkbox

Another approach I tried was to handle the graphics myself, rather than use a checkbox:

{% highlight html %}
{% raw %}
...
<ion-list>
  <ion-item class="item" ng-repeat="task in activeProject.tasks">
  <i class="icon ion-ios7-circle-outline " ng-hide="task.isDone=='YES'" ng-click="toggleDone($index, task)"></i>
  <i class="icon ion-checkmark-circled" ng-show="task.isDone=='YES'" ng-click="toggleDone($index, task)"></i>
  <span  ng-click="toggleDone($index, task)">{{task.title}}</span>
    <i class="icon ion-edit" ng-click="editTask($index, task)"></i>
  </ion-item>
</ion-list>
 ...
{% endraw %}
{% endhighlight %}


Notice in this version I am using two icons alternating between them depending on the state of `task.isDone` using the `ng-show` and `ng-hide` directives. I found the ionic icons to use (in the [ionicons documentation](http://ionicons.com/)). However, this forces a specific representation of a checkbox rather than leveraging the native selection of the underlying os. In this version I must change the value of the `task.isDone` as shown in the following `toggleDone` method (added to 'app.js'):

{% highlight javascript %}
{% raw %}

  // Make sure to persist the change after is done is toggled
  $scope.toggleDone = function(i, task) {
    //alert("toggle done task "+task.isDone)
    if (!$scope.activeProject || !task) {
      return;
    }
    $scope.activeProject.tasks[i].isDone = ($scope.activeProject.tasks[i].isDone=="YES")?"NO":"YES";
    Projects.save($scope.projects);
  }

{% endraw %}
{% endhighlight %}

### added the check box to the 'edittask.html' form script in 'index.html'

*added after the &lt;label&gt; tag*

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
</form>
...
{% endraw %}
{% endhighlight %}

the &lt;ion-checkbox&gt; directive is used here to set the state of done to 'YES' or 'NO' value once again. 

*the code sample can be found on [my github page][codesample]*

[next article in series][next]

[parent]:   {% post_url 2014-12-20-expand-the-ionic-example %}
[editatask]: {% post_url 2014-12-20-edit-a-task %}
[codesample]: https://github.com/edlovesjava/ionic-todo
[next]: {% post_url 2014-12-23-delete-a-task %}