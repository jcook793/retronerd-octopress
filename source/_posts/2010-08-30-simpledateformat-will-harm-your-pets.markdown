---
layout: post
title: "SimpleDateFormat Will Harm Your Pets"
date: 2010-08-30 00:16
comments: true
categories: Java
---
Today while researching a production issue I found a concurrency bug when formatting a date. It really wasn't anyone’s glaring fault, it's mainly just an artifact of our still-in-progress transition to Spring. We have an MDB that calls an EJB that calls a Spring-created object which has an object-local variable that is a SimpleDateFormat. The problem is that [by default, Spring beans are singletons](http://static.springsource.org/spring/docs/1.2.9/reference/beans.html#beans-factory-modes). Combine that with the fact that [SimpleDateFormat is not thread-safe](http://download.oracle.com/javase/6/docs/api/java/text/SimpleDateFormat.html#synchronization) and hilarity ensues.

{% img center /assets/images/simpledateformat-diagram.png %}

Anyone could have missed that – I even knew something was potentially wrong in the date formatting code and seeing SimpleDateFormat threw up some immediate alarms, but it still took me quite a while to walk the dependency chain and then realize the EJB-transitioning-to-Spring-bean was a singleton.

What I’m getting at is, the problem really isn’t Spring or singletons or that we should flog the programmer who didn't read every line of code in the Spring bean when he was converting it from an EJB. Us Java programmers need to ditch using thread-unsafe classes just like we ditched using pointers and managing memory. These days there’s no good reason to use SimpleDateFormat. It is evil. I’ve seen it kick puppies. Cute ones.

{% img center /assets/images/simpledateformat-puppy.png %}

The good news is that for date formatting, there is an easy drop-in replacement in Apache’s Commons Lang library called [FastDateFormat](http://commons.apache.org/lang/api-2.5/org/apache/commons/lang/time/FastDateFormat.html) that is not only thread-safe, but faster to boot.

If you want to take it a step further (because pretty much anything date-related in the JDK sucks), check out the [Joda Time API](http://joda-time.sourceforge.net/). It can do [thread-safe formatting and parsing](http://joda-time.sourceforge.net/userguide.html#Input_and_Output).

So if you are touching a class and you see a reference to SimpleDateFormat, please consider taking a few minutes to replace it with something better. Think of the puppies.