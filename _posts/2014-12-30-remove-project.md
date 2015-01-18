---
layout: post
title:  "Refactor and extend : remove project story"
date:   2014-12-30 08:47
categories: project intro
---

## story: remove project

*part of the [refactor and extend][parent] stories*

 > as a developer I wish to remove a project and all its tasks

learnings featured:

 * 

## Steps

 1. [add the delete button](#add_delete_button)
 2. [add delete project method](#add_delete_project_method)
 3. [refactor show confirm](#refactor_show_confirm)

### <a name="add_delete_button"></a>add the delete button to the edit project template

Modify the 'edit-project.html' template to add a delete button, similar to the edit task template

{% highlight html %}
{% raw %}

  <div class="modal">
    <!-- Modal header bar -->
    <ion-header-bar class="bar-secondary">
      <h1 class="title">Edit Project</h1>
      <button class="button button-clear button-positive" ng-click="closeEditProject()">Cancel</button>
    </ion-header-bar>
    <!-- Modal content area -->
    <ion-content>
      <form ng-submit="updateProject(projectIndex, project)">
        <div class="list">
          <label class="item item-input">
            <input type="text" placeholder="What is the name of the project?" ng-model="project.title">
          </label>
        </div>
        <div class="padding">
          <button type="submit" class="button button-block button-positive">Update Project</button>
        </div>
        <div class="padding">
          <button t class="button button-block button-assertive" ng-click="deleteProject(projectIndex, project)">Delete Project</button>
        </div>
      </form>
    </ion-content>
  </div>

{% endraw %}
{% endhighlight %}

the delete calls `deleteProject` method passing parameters similar to the `updateProject` coded previously. Remember the scope variable `projectIndex` was set when the edit project modal was opened so it contains the index of the project to remove.

### <a name="add_delete_project_method"></a>add the `deleteProject` method to 'controllers.js'

{% highlight javascript %}
{% raw %}

  // delete selected project
  $scope.deleteProject = function(i, project) {
    if (!$scope.activeProject || !project ) {
      return;
    }
    console.log("start deleting");
    $scope.showConfirm('Delete Project', 'Are you sure you want to delete this project?',function() {
      console.log("confirmed to delete project and all its tasks "+i);
      $scope.projects.splice(i,1);
      Projects.save($scope.projects);
    });
  } 

{% endraw %}
{% endhighlight %}

Adding this method I wanted to reuse the `showConfirm` dialog but I had to refactot it to pass in the title and message I want since it was hard coded to deleting a task. 

### <a name="refactor_show_confirm"></a>refactor show confirm

So `showConfirm` method in 'controllers.js' is changed to

{% highlight javascript %}
{% raw %}

  // A confirm dialog
  $scope.showConfirm = function(title, message, onYes, onNo) {
   var confirmPopup = $ionicPopup.confirm({
     title: title,
     template: message
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

And then I had to update the `deleteTask` method that also calls show confirm to now pass in title and mesasge:

{% highlight javascript %}
{% raw %}

  // delete selected task
  $scope.deleteTask = function(i, task) {
    if (!$scope.activeProject || !task ) {
      return;
    }
    console.log("start deleting");
    $scope.showConfirm('Delete Task', 'Are you sure you want to delete this task?', function() {
      console.log("confirmed to delete task "+i);
      $scope.activeProject.tasks.splice(i,1);
      Projects.save($scope.projects);
    });
  } 

{% endraw %}
{% endhighlight %}

*the code for this can be found on [my github page][samplecode2]*

[next article in series][next]

[next]: {% post_url 2014-12-31-show-strikethrough %}
[parent]: {% post_url 2014-12-23-refactor-and-extend %}
[samplecode2]: https://github.com/edlovesjava/ionic-todo2

