---
layout: post
title:  "Install Ionic"
date:   2014-12-18 15:34
categories: project intro
---

need to install ionic see [ionic install chapter][ionic_inst]
need phonegap/cordova as documented above
need to install android link to [installing android studio][android_studio]
need to configure ant, java, android sdk tools in path

.bash_profile

{% highlight bash %}
PATH=/Users/edlovesjava/Library/Android/sdk/platform-tools:/Users/edlovesjava/Library/Android/sdk/tools:$PATH
{% endhighlight %}

.profile

{% highlight bash %}
JAVA_HOME=/Library/Java/JavaVirtualMachines/jdk1.8.0_25.jdk/Contents/Home;
export JAVA_HOME;
PATH=$PATH:$JAVA_HOME
ANT_HOME=/Users/edlovesjava/tools/apache-ant-1.9.4
export PATH=$ANT_HOME/bin:$PATH
{% endhighlight %}

need to use andoid command line to select 'android-19' image but failed because I thought they were talking about tools sdk version 19 but actually it was the sdk 4.4.2 (API 19)!

see [stackoverflow article][stackoverflow] 

[ionic_inst]:   http://ionicframework.com/docs/guide/installation.html
[android_studio]: http://developer.android.com/sdk/installing/index.html?pkg=studio
[stackoverflow]:  http://stackoverflow.com/questions/24931155/cordova-3-5-0-install-error-please-install-android-target-19