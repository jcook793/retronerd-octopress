---
layout: post
title: "The World of Python"
date: 2013-02-08 19:26
comments: true
published: false
categories: Python
---
![](/assets/images/world-of-python.png)
When I [first started learning Python](http://code.google.com/edu/languages/google-python-class/), I quickly became a fan of the syntax.  Tuples, dictionaries and list comprehensions are some cool language features that remove a lot of boilerplate.  But as I waded into existing projects I kept running into environmental concepts like eggs, easy\_install, pip, virtualenv and other scaffolding that have nothing to do with the language per se, but I needed to know to do anything useful with it.

However I couldn't find any overview of this Python ecosystem that I was supposed to already know about.

It's easy to start mumbling "this Python thing is a disorganized mess", but I recognized that if someone were to walk into a world I already know quite well they would be just as lost.  A Java newbie might rightly ask:

> What is a classpath?<br/>
> What is a JAR?<br/>
> An EAR?<br/>
> Wait, who declared WAR?<br/>
> Why would I want to use Ant or Maven or Ivy?<br/>

So I took a step back and did a survey of the land.  And here’s what I found.

## Package Management
[This 2005 post to the distutils mailing list](http://mail.python.org/pipermail/distutils-sig/2005-June/004652.html) by [P.J. Eby](http://dirtsimple.org/programming/) defines a lot of these concepts.

### Eggs
An egg is a distribution of a Python package.  An egg is similar to a [Java JAR](http://en.wikipedia.org/wiki/JAR_(file_format) but has more in common with a [Ruby gem](http://docs.rubygems.org/read/chapter/1#page22), as eggs support declaring dependencies and defining multiple entry points.  If you want to read a lot more about the egg format --TODO: Link

#### Built Eggs
Built eggs are directories or zipfiles whose name ends with .egg and follows the egg naming conventions, and contain an EGG-INFO subdirectory (zipped or otherwise).

#### Development Eggs
Development eggs are normal directories of Python code with one or more ProjectName.egg-info subdirectories.

#### Egg Links
Egg links only exist to support platforms which do not have native symbolic links (ahem, Windows).  They are \*.egg-link files that contain the name of a built or development egg.

#### How Eggs Are Found
[Stuff from pkg\_resources here](http://packages.python.org/distribute/pkg_resources.html#overview)
Talk about sys.path


### PyPi
[PyPi](http://pypi.python.org/pypi) is short for Python Package Index.  It is the official repository for third-party Python packages.  It is similar to Perl’s [CPAN](http://www.cpan.org/) and Java’s somewhat less formal [Apache Maven central repository](http://search.maven.org/).

*Do not confuse this with [PyPy](http://pypy.org/)!  PyPy is an implementation of Python written in (surprise!) Python.*


### site-packages
[A good writeup on PyPi here](http://wiki.python.org/moin/CheeseShopTutorial)


### Old School: Setuptools, EasyInstall and pkg\_resources

### New School: Distribute, pip and (still) pkg\_resources
[Distribute](http://packages.python.org/distribute/) allows you to "easily download, build, install, upgrade, and uninstall Python packages".  It is a fork of and replacement for the older [setuptools](http://pypi.python.org/pypi/setuptools) project.

#### pip
[Project homepage](http://www.pip-installer.org/)
[Pypi link](http://pypi.python.org/pypi/pip)
The pip homepage declares that "pip installs packages. Python packages. An easy\_install replacement".  It lists [several improvements over Easy Install](http://www.pip-installer.org/en/latest/other-tools.html#pip-compared-to-easy-install).

#### pkg\_resources
[From the SVN repo](http://svn.python.org/projects/sandbox/trunk/setuptools/pkg_resources.py)
[Overview from Peak?](http://peak.telecommunity.com/DevCenter/PkgResources#overview)

#### buildout
[homepage](http://www.buildout.org/)
WTF is buildout really?  Seems like a way to “build out” a development environment for a project - get the dependencies, etc.  I don’t think it’s really a build tool though.  People call it that but either they are wrong or I’m confused.

#### sys.path
[link](http://docs.python.org/library/sys.html#sys.path)

### Virtual Environments
Blah blah blah something about virtual environments and how they differ from system Python.


