---
layout: post
title:  "Refactor and extend : show completed task as strikethrough story"
date:   2014-12-31 08:50
categories: project intro
---

## story: show completed task as strikethrough project

*part of the [refactor and extend][parent] stories*

 > as a user I wish to show a task that is completed with a strikethrough

learnings featured:

 * modifying css
 * conditional [`ng-class`](http://stackoverflow.com/questions/16529825/angularjs-ngclass-conditional)

## Steps

 1. [modify 'todo-list.html template'](#modify_todo_list) to use conditional 'ng-class'
 2. [add style to 'style.css'](#add_style)

### <a name="modify_todo_list"></a>modify 'todo-list.html' template

Modify the 'todo-list.html' template to add the conditional `ng-class` 

see the `ion-checkbox` directive

    <ion-checkbox ng-class="{complete: task.isDone == 'YES'}"   
    class="item-text-wrap" style="border:none;" ng-model="task.isDone"  
    ng-model="task.isDone" ng-click="doneClicked($index, task)"  
    ng-true-value="'YES'" ng-false-value="'NO' ">  
    <span>{{task.title}}</span></ion-checkbox>

Complete 'todo-list.html'

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
              <ion-checkbox ng-class="{complete: task.isDone == 'YES'}" class="item-text-wrap" style="border:none;" ng-model="task.isDone"  ng-model="task.isDone" ng-click="doneClicked($index, task)" ng-true-value="'YES'" ng-false-value="'NO' "><span>{{task.title}}</span></ion-checkbox>
              <i class="icon ion-edit" ng-click="editTask($index, task)" ></i>
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


### <a name="add_style"></a>add style to 'style.css'

{% highlight css %}
{% raw %}

/* Empty. Add your own CSS if you like */
.complete {
  text-decoration: line-through;
}


{% endraw %}
{% endhighlight %}

*the code for this can be found on [my github page][samplecode2]*

[next article in the series][next]

[next]: {% post_url 2014-12-31-order-tasks %}
[parent]: {% post_url 2014-12-23-refactor-and-extend %}
[samplecode2]: https://github.com/edlovesjava/ionic-todo2

