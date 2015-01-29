---
layout: post
title:  "Expanding on todo : extending on the Ionic todo example"
date:   2014-12-20 16:00
categories: project intro
---

This post outlines a set of changes made to the [Ionic framework Todo example][ionicexample] to further enhance the learning and create a useful working deliverable. Since I work in places using [agile methodologies](http://agilemanifesto.org) I will make these modifications as a set of stories.

I started by following the example provided step by step (chapter 1 - 6), including installing ionic, and installing ios and android emulators as described in a [previous post][installionic].

The [ionic example][ionicexample] as described in the guide is limited to features of being able to add projects, and also tasks to projects. I have added the following features to futher enforce my learning.

Either complete the book example as desribed in the ionic documentation, or you can get it from [my project page here][ionictodoorig].

Additional stories I will add:

 * [as a user I want to edit a task][edittask]
 * [as a user I want to mark a task as done][markdone]
 * [as a user I want to delete a task][taskdelete]

[Next series]({% post_url 2014-12-23-refactor-and-extend %}) I want to tackle the following:

 * [as a developer I would like to use multiple files for javascript and templates][multiplefiles]
 * [as a developer I wish to use the angluar router so I can add additional routes for more advanced interactions][userouting] 
 * [as a user I wish to rename a project][renameproject]
 * [as a user I wish to remove a project and its tasks][removeproject]
 * [as a user I wish to show the completed tasks with a strikethrough][showstrikethrough]
 * [as a user I wish to see tasks in order of most recently added][ordertasks]
 * [as a user I wish to see tasks in order of most recently added (improved)][ordertasksbetter]
 * as a user I wish to hide the tasks that are done
 
Additional ambitious stories:

 * as a user I wish to persist users data on the cloud so It can be shared with other apps and devices
 * as a user I wish to share my tasks and projects with others
 * as a user I wish to stop sharing my tasks and projects with others
 
The completed code can be found on [my github page][codesample]

I will walk through the additions I have done.

[next article in series][edittask]

[markdone]:          {% post_url 2014-12-21-mark-a-task-as-done %}
[edittask]:          {% post_url 2014-12-20-edit-a-task %}
[installionic]:      {% post_url 2014-12-18-install-ionic %}
[taskdelete]:        {% post_url 2014-12-23-delete-a-task %}
[multiplefiles]:     {% post_url 2014-12-23-multiple-files %}
[userouting]:        {% post_url 2014-12-23-use-routing %}
[renameproject]:     {% post_url 2014-12-23-rename-project %}
[removeproject]:     {% post_url 2014-12-30-remove-project %}
[showstrikethrough]: {% post_url 2014-12-31-show-strikethrough %}
[ordertasks]:        {% post_url 2015-01-01-order-tasks %}
[ordertasksbetter]:  {% post_url 2015-01-02-better-order-tasks %}
[ionicexample]:      http://ionicframework.com/docs/guide/
[ionictodoorig]:     https://github.com/edlovesjava/ionic-todo-orig
[codesample]:        https://github.com/edlovesjava/ionic-todo
