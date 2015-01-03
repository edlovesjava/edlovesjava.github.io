---
layout: post
title:  "Refactor and extend : order tasks by most recently added story"
date:   2015-01-01 08:50
categories: project intro
---

## story: show tasks in order of most recently added

*part of the [refactor and extend][parent] stories*

 > as a user I wish to show tasks in most recently added order

learnings featured:

 * using [angular's `orderBy` filter][orderby]

The first approach I will do is a simple brute force approach which sorts the active project's tasks every time we save. This is pretty inefficient for larger data, but very simple to implement. 

In a future post, a more flexible approach will be to make the display order independent of the order of the saved data. This will require storing and retrieving tasks using something like a primary key. Currently we update tasks based on their position in the displayed list (remember `$index`) and we assume that this index can be used to index the stored tasks array. But to be more flexible we need to reference tasks indepdendent of their position in the displayed list.

## Steps

 1. [use a createDate when storing tasks](#use_create_date)
 1. [create sort method](#add_sort_method)  `orderProjectTasks`
 2. [call sort method on add and edit of tasks ](#call_sort_method)

### <a name="use_create_date"></a>store a 'createDate' for all tasks

Modify `createTask` method in 'controllers.js' to add a create date saved in ISO standard

{% highlight javascript %}
{% raw %}

  // Called when the form is submitted
  $scope.createTask = function(task) {
    if (!$scope.activeProject || !task) {
      return;
    }
    $scope.activeProject.tasks.push({
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

Modify `editTask` method in 'controllers.js' to preserve the createDate during edit


{% highlight javascript %}
{% raw %}

  // Open our new task modal
  $scope.editTask = function(i, task) {
    $scope.task = {title: task.title, isDone: task.isDone, createDate: task.createDate};
    $scope.taskIndex = i;
    $scope.editTaskModal.show();
  };

{% endraw %}
{% endhighlight %}

### <a name="add_sort_method"></a>create a sort method using angular's `orderBy`

Modify 'controllers.js' method to add a couple of variables: get a handle on the orderBy filter to use later, use a predicate indicating the field to sort by, and a reverse flag to indicate if the order is in reverse

{% highlight javascript %}
{% raw %}
  var orderBy = $filter('orderBy');

  $scope.predicate = 'createDate';
  $scope.reverse = true;

{% endraw %}
{% endhighlight %}


Modify 'controllers.js' and add `orderProjectTasks` method that uses angular's `orderBy` passing in these varables:

{% highlight javascript %}
{% raw %}


  // Called to select the given project
  $scope.orderProjectTasks = function(project) {
    project.tasks = orderBy(project.tasks,$scope.predicate,$scope.reverse);
  };


{% endraw %}
{% endhighlight %}


### <a name="call_sort_method"></a>call the sort method: `orderProjectTasks` on add and edit of tasks

Modify `createTask` method in 'controllers.js' to call the `orderProjectTasks` method

{% highlight javascript %}
{% raw %}

  // Called when the form is submitted
  $scope.createTask = function(task) {
    if (!$scope.activeProject || !task) {
      return;
    }
    $scope.activeProject.tasks.push({
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

Modify `updateTask` method in 'controllers.js' to call the `orderProjectTasks` method


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
    $scope.orderProjectTasks($scope.activeProject);
    Projects.save($scope.projects);

  };

{% endraw %}
{% endhighlight %}

*the code for this can be found on [my github page][samplecode2]*


[parent]: {% post_url 2014-12-23-refactor-and-extend %}
[samplecode2]: https://github.com/edlovesjava/ionic-todo2
[orderby]: https://docs.angularjs.org/api/ng/filter/orderBy