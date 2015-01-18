---
layout: post
title:  "Refactor and extend : rename project story"
date:   2014-12-23 15:30
categories: project intro
---

## story: rename project

*part of the [refactor and extend][parent] stories*

 > as a user I wish to rename a project

I will allow the project to be renamed by adding a edit icon to the right of the projects list similar to the todo tasks.

learnings featured:

 * 

## Steps

 1. [add edit project template](#refactor_show_confirm)
 1. [modify the todo list template](#modify_todo_list_template) to show the edit button
 1. [load the new template](#load_the_template)
 1. [open and close the modal](#open_and_close_modal)
 1. [save the updated project](#save_edited_project)


###<a name="add_edit_project_template"></a> add a template 'edit-project.html'

this template is similar to the edit-tasks template

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
      </form>
    </ion-content>
  </div>

{% endraw %}
{% endhighlight %}

### <a name="modify_todo_list_template"></a>modify the list of projects

modify 'todo-list.html' with the following changes:

 * modify the list of projects to render an edit icon to the right using the classes 'item item-icon-right'
 * move the `ng-click` event from the &lt;ion-item&gt; directive to a &lt;span;&gt; tag wrapping the project title
 * add an &lt;i&gt; tag and an `ng-click` to call the `editProject` method, passing in the `$index` and the project variable available within  the `ng-repeat`

{% highlight html %}
{% raw %}
...
    <ion-item class="item item-icon-right" ng-repeat="project in projects"  ng-class="{active: activeProject == project}">
      <span ng-click="selectProject(project, $index)">{{project.title}}</span>
      <i class="icon ion-edit" ng-click="editProject($index, project)"></i>
    </ion-item>
...
{% endraw %}
{% endhighlight %}

### <a name="load_the_template"></a>load the new template 

in the 'controllers.js' load the template into a scope variable


*add after the other modal templates are loaded*

{% highlight javascript %}
{% raw %}
...
 // Edit and load the Modal for edit project
  $ionicModal.fromTemplateUrl('templates/edit-project.html', function(modal) {
    $scope.editProjectModal = modal;
  }, {
    scope: $scope,
    animation: 'slide-in-up'
  });
...
{% endraw %}
{% endhighlight %}

### <a name="open_and_close_modal"></a>open and close the modal

in the 'controllers.js' add methods `editProject` to open the modal and `closeProject` to close the modal

{% highlight javascript %}
{% raw %}

...
  // Open our new task modal
  $scope.editProject = function(i, project) {
    $scope.project = {title: $scope.projects[i].title, tasks: $scope.projects[i].tasks};
    $scope.projectIndex = i;
    $scope.editProjectModal.show();
  };
...
  // Close the edit task modal
  $scope.closeEditProject = function() {
    $scope.editProjectModal.hide();
  };
...

{% endraw %}
{% endhighlight %}

### <a name="save_edited_project"></a>save the updated project

in the 'controllers.js' add the method `updateProject` to save the updated project 

{% highlight javascript %}
{% raw %}
...
  // Called when the form is submitted
  $scope.updateProject = function(i, project) {
    if (!$scope.projects || !project) {
      return;
    }
    $scope.projects[i] = project;
    $scope.editProjectModal.hide();

    // Inefficient, but save all the projects
    Projects.save($scope.projects);

  };
...  
{% endraw %}
{% endhighlight %}


*the code for this can be found on [my github page][samplecode2]*

[next article in series][next]

[next]: {% post_url 2014-12-30-remove-project %}
[parent]: {% post_url 2014-12-23-refactor-and-extend %}
[samplecode2]: https://github.com/edlovesjava/ionic-todo2