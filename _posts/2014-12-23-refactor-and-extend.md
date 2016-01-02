---
layout: post
title:  "Refactor and extend"
date:   2014-12-23 08:00
categories: project intro
tag: series
---

A [previous series of posts][expand] described how I extended the [ionc sample todo app][ionicguide] to a more functioning todo application. This series further expands on the todo app by refactoring into more manageable chunks, and adding some more features.


## refactor stories

I was inspired by [this example][sampletofollow] to refactor to a mulitple file project following anglular js typical approaches. This example chose to separate the javascript out of one big app.js into multiple js files: use one services.js for services, one controllers.js for all controllers and a much smaller app.js just to initialize the router. The app.js shows the use of the anglular router technology to route to the first page which will show the list of tasks. For this example this is the only page. The project also used one file per template and I will do the same.

 * [as a developer I would like to use multiple files for javascript and templates][multiplefiles]
 * [as a developer I wish to use the angluar router so I can add additional routes for more advanced interactions][userouting]


## extend stories

 * [as a user I wish to rename a project][renameproject]
 * [as a user I wish to remove a project and its tasks][removeproject]
 * [as a user I wish to show the completed tasks with a strikethrough][showstrikethrough]
 * [as a user I wish to see tasks in order of most recently added][ordertasks]
 * as a user I wish to hide the tasks that are done

*the code for this can be found on [my github page][samplecode2]*

[next article in series][next]

[next]:           {% post_url 2014-12-23-multiple-files %}
[parent]:         {% post_url 2014-12-23-refactor-and-extend %}
[samplecode2]:    https://github.com/edlovesjava/ionic-todo2
[sampletofollow]: http://forum.ionicframework.com/t/ion-list-example-with-add-delete-edit-default-items/2133?username_filters=Alex
[multiplefiles]:  {% post_url 2014-12-23-multiple-files %}
[userouting]:     {% post_url 2014-12-23-use-routing %}
[renameproject]:  {% post_url 2014-12-23-rename-project %}
[expand]:         {% post_url 2014-12-20-expand-the-ionic-example %}
[ionicguide]:     http://ionicframework.com/docs/guide/
[removeproject]:  {% post_url 2014-12-30-remove-project %}
[showstrikethrough]: {% post_url 2014-12-31-show-strikethrough %}
[ordertasks]:     {% post_url 2015-01-01-order-tasks %}

