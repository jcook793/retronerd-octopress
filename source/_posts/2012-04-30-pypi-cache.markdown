---
layout: post
title: "PyPi Cache"
date: 2012-04-30 17:42
comments: true
categories: [Python, Apache]
---

![](/assets/images/pi-pie.jpg)

## The Problem
Not many things are more annoying than a build failure that is nobody’s fault:
```
[exec] Getting distribution for 'MarkupSafe>=0.9.2'.
[exec]
[exec] An internal error occurred due to a bug in either zc.buildout or in a
[exec] recipe being used:
[exec] Traceback (most recent call last):
[exec] File "/mnt/buildout-caches/eggs/zc.buildout-1.5.2-py2.7.egg/zc/buildout/buildout.py", line 1805, in main
...
[exec] File "/usr/lib/python2.7/socket.py", line 447, in readline
[exec] data = self._sock.recv(self._rbufsize)
[exec] timeout: timed out
 
 BUILD FAILED
```
We use [Jenkins](http://jenkins-ci.org/) for our automated builds which runs [Buildout](http://www.buildout.org/) to assemble our project for deployment.  Buildout checks the [PyPi index](http://pypi.python.org/pypi) for any new packages that are needed or any new versions for packages that are not version-pinned.  So if PyPi is unavailable (or very slow) for a while, our build fails.  That sucks because Jenkins won’t try another build until it detects a new changelist in our repository.

## The Solution
I started looking into how we could cache PyPi locally to avoid this problem altogether.  I found [several](http://www.apache.org/info/mirror-proxy.html) [ways](http://pypi.python.org/pypi/pep381client) to [achieve](http://pypi.python.org/pypi/z3c.pypimirror) [this](http://yopypi.googlecode.com/hg/docs/build/html/index.html), but finally settled on [collective-eggproxy](http://pypi.python.org/pypi/collective.eggproxy).  I mainly chose it for 2 reasons:
1. It doesn’t cache/sync all 30+ gigs of PyPi
2. We already have an Apache instance and it can be ran as a mod_python module
I ran into a couple of installation and configuration problems so I thought I’d share our setup.

### Installation

#### Eggy Parts
BeautifulSoup is an awesome HTML/XML parser but whoever manages their index on PyPi has the wrong links.  I’ve seen a couple of packages that rely on BeautifulSoup versions <= 3.09, but those seem to fail.  I finally figured out that adding a find-links hint to easy_install fixed me right up:
``` bash
easy_install -f http://www.crummy.com/software/BeautifulSoup/download/ collective.eggproxy
```
Note that I’m just installing it in the system Python site-packages.  You could use a virtualenv, but this is just our CI server.

#### mod_python
I use Ubuntu so I got mod_python via apt:
```bash
sudo apt-get install libapache2-mod-python
```

### Configuration

#### collective.eggproxy
In /etc/eggproxy.conf I used:
``` python
[eggproxy]
eggs_directory = /var/pypi-cache
index = http://pypi.python.org/simple
update_interval = 24
always_refresh = 0
timeout = 3
```

#### Apache
I added a dedicated virtualhost to Apache like so:
``` apache
<VirtualHost *:80>
    ServerName pypi-cache.hostname.tld
    Alias /pypi "/var/pypi-cache"
    <Directory "/var/pypi-cache">
        Order allow,deny
        Allow from all
        SetHandler mod_python
        PythonFixupHandler collective.eggproxy.apache_handler::fixup_handler
        PythonInterpreter main_interpreter
        PythonOption URLPrefix /pypi
    </Directory>
    LogLevel warn
    ErrorLog ${APACHE_LOG_DIR}/pypi-cache.error.log
    CustomLog ${APACHE_LOG_DIR}/pypi-cache.access.log combined
</VirtualHost>
```

#### Buildout
In our project’s buildout.cfg I simply had to add:
``` python
index = http://pypi-cache.hostname.tld/pypi/
```

## Conclusion
This has worked out great for us.  Not only are our builds more stable, but there’s a noticeable speed improvement.  Totally worth it!
