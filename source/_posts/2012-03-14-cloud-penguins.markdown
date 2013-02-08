---
layout: post
title: "Cloud Penguins"
date: 2012-03-13 18:09
comments: true
categories: 
---
![](/assets/images/flying-penguin.png)

This weekend I was tasked with setting up a new [Jenkins](http://jenkins-ci.org/) server on EC2.  I’ve used a server built from [Amazon’s official Linux AMI](http://aws.amazon.com/amazon-linux-ami/) before and was happy enough with it.  So I went that route.

I did the typical wrestling with Apache, Tomcat and SSL configuration.  Our project uses Python 2.7 and when the first test build failed I was a bit surprised that it wasn’t installed.  I confidently typed
``` bash
sudo yum install python27
```
but there was no package for me.  Python 2.7 was released almost 2 years ago at this point, what the hell?  I searched and searched for a yum repo but there was none.

Very reluctantly I installed Python 2.7 from source.  No, I’m not allergic to configure and make, but I like the ability to upgrade everything with one shot.  Programmers typically make terrible system administrators so I don’t need yet another thing to remember about this server.  But whatever, I installed it and went on with my life.

Our project also uses Ant.  When the second test build failed I learned that we specifically require Ant 1.8 and the latest available from the Amazon yum repository is 1.7.  Now this is a bit ridiculous, 1.8 was released **over 2 years ago**.  Even though it was past 2am at this point, I decided to kick this installation to the curb.

I chose [Ubuntu’s Cloud Guest AMI](https://help.ubuntu.com/community/EC2StartersGuide), and it’s brilliant.  Most of the defaults are exactly what I’d choose.  And by golly, they have packages for Python 2.7 and Ant 1.8.  There’s even a package with [Jenkins on Tomcat](http://packages.ubuntu.com/oneiric/jenkins-tomcat) for me (oh and look at that, they have a package browser on the web, how clever).
