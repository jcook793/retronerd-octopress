---
layout: post
title: "The World of Python"
date: 2013-02-09 18:34
comments: true
categories: Python
---
{% img center /assets/images/world-of-python.png %}

When I [first started learning Python](http://code.google.com/edu/languages/google-python-class/), I quickly became a fan of the syntax.  Tuples, dictionaries and list comprehensions are some cool language features that remove a lot of boilerplate.  But as I waded into existing projects I kept running into environmental concepts like eggs, easy\_install, pip, virtualenv and other scaffolding that have nothing to do with the language per se, but I needed to know to do anything useful with it.

However I couldn't find any overview of this Python ecosystem that I needed to navigate.

<!-- more -->

It's easy to start grumbling, "this Python thing is a disorganized mess", but I recognized that if someone were to walk into a world I already know quite well they would be just as lost.  A Java newbie might rightly ask:
{% blockquote -A Java newbie %}
What is a class path?
What is a JAR?
An EAR?
Wait, who declared WAR?
Why would I want to use Ant or Maven or Ivy?
{% endblockquote %}


So I took a step back and did a survey of the land.  And here's what I found.

## Package Management
Of course you want to get all the cool libraries that people are writing.  But how do you get them?


### First of All, Eggs
An egg is a distribution of a Python package.  Eggs are similar to [Java JARs](http://en.wikipedia.org/wiki/JAR_(file_format) but have more in common with [Ruby gems](http://docs.rubygems.org/read/chapter/1#page22), as eggs support declaring dependencies and defining multiple entry points.

#### Built Eggs
Built eggs are how you usually get Python packages.  They are typically zip files that contain all the code.  A built egg can also be an un-zipped directory structure and there's a whole [specification for how it's laid out](http://peak.telecommunity.com/DevCenter/EggFormats), but you don't care about that crap for now.

#### Development Eggs
A development egg is an in-place installation of an egg, for when you are working on an egg.  It's just a normal directory of Python code with some `ProjectName.egg-info` subdirectories.

#### Egg Links
Egg links are pointers to where the real egg is on the filesystem.  They only exist to support platforms which do not have native symbolic links (ahem, Windows).  They are `*.egg-link` files that contain the name of a built or development egg.


### Loading Installed Eggs
So now you know what an egg is.  Well damn, it's basically just a bunch of Python code!  So let me download some and get to work!  Hold on there, tiger.  First you need to know how Python finds these eggs.  It simply looks in the `sys.path` variable for paths to packages.  But where does this path come from?

#### site-packages
This is the global spot for a Python installation to store packages.  **I'd highly recommend not globally installing many things on your system's default Python**, as this really dirties the waters.  More on that later.

If you're curious, you can run this cryptic little bugger to find out where your Python's `site-packages` directory is located:
```
python -c "from distutils.sysconfig import get_python_lib; print get_python_lib()"
```

#### PYTHONPATH
Python also grabs packages from the environment variable `PYTHONPATH`.  Not much else I can say about that.  Yep.


### Obtaining and Installing Eggs
OK, now I know how Python finds these eggs.  So where do I get them and how do I install them?  First of all, let me warn you that the single most confusing thing about Python is the state of the package install tools.  But the good news is that Python has a well-maintained central repository.

#### Most Eggs are in PyPi
[PyPi](http://pypi.python.org/pypi) is short for Python Package Index.  It is the official repository for third-party Python packages.  It is similar to Perl’s [CPAN](http://www.cpan.org/) and Java's somewhat less formal [Apache Maven central repository](http://search.maven.org/).

*Do not confuse this with [PyPy](http://pypy.org/)!  PyPy is an implementation of Python written in (surprise!) Python.*

#### Old School Package Installation: easy_install from the setuptools Project
For the longest time everybody just used `easy_install` to download and install their packages.  `easy_install` is part of the setuptools project by the ubiquitous [P.J. Eby](http://dirtsimple.org/programming/).  But people started wanting newer features and I think Mr. Eby just didn't have the time to dedicate to a bunch of new work.  So setuptools got forked.

#### New School Package Installation: pip from the Distribute Project
The setuptools project was forked into the Distribute project. Now most people use `pip` instead of `easy_install`.  I'll let the guys who wrote `pip` [explain why](http://www.pip-installer.org/en/latest/other-tools.html#pip-compared-to-easy-install):

> pip was originally written to improve on easy_install in the following ways:
> 
> * All packages are downloaded before installation. Partially-completed installation doesn’t occur as a result.
> * Care is taken to present useful output on the console.
> * The reasons for actions are kept track of. For instance, if a package is being installed, pip keeps track of why that package was required.
> * Error messages should be useful.
> * The code is relatively concise and cohesive, making it easier to use programmatically.
> * Packages don’t have to be installed as egg archives, they can be installed flat (while keeping the egg metadata).
> * Native support for other version control systems (Git, Mercurial and Bazaar)
> * Uninstallation of packages.
> * Simple to define fixed sets of requirements and reliably reproduce a set of packages.
> 
> pip doesn’t do everything that easy_install does. Specifically:
> 
> * It cannot install from eggs. It only installs from source. (In the future it would be good if it could install binaries from Windows .exe or .msi – binary install on other platforms is not a priority.)
> * It is incompatible with some packages that extensively customize distutils or setuptools in their setup.py files.


## Using Eggs Without Getting Egg All Over Your Python

### Buildout
[Buildout](http://www.buildout.org/).  What is it?  Alright, the name makes it sound like a build tool, but it's not really.  It's a way to "build out" a development environment for a project.  It's really good at:

* Fetching all the dependencies for a project
* Keeping those dependencies out of your system Python
* Creating a `python` script that includes the paths for your project dependencies along with your project code so you don't need to poison your system Python

It does a lot of other stuff, but start thinking of it that way and not necessarily like `make` or `rake` or Ant/Maven.

### Virtualenv
So let's say you want to play around with some packages but not necessarily setup a project and declare a bunch of dependencies and stuff.  Dammit, you want to use `pip`!  Enter [virtualenv](http://www.virtualenv.org/en/latest/).

All virtualenv does is copy your Python executable somewhere safe and create an isolated `site-packages` directory so `easy_install` and `pip` will keep their dirty mitts out of it.  That way you can play around and if something gets all screwed up, you can just blow away your virtual environment and start over.

Oddly enough, to use virtualenv, you'll need to `pip install virtualenv` to your system Python.  **I know**, that's kind of hypocritical.  Deal with it.

I highly recommend using a set of helper scripts called [virtualenvwrapper](http://www.doughellmann.com/projects/virtualenvwrapper/) for creating and using Python virtual environments.

## And That's It
I hope this helps clear the way for you a little bit.  If I've made any errors, please add a comment and let me know!
