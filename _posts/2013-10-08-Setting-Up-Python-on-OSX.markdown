---
layout: post
title: "Setting Up Python on OSX"
---

I frequently find myself setting up Python environments on OSX.
While it really isn't too tricky, it does bear mentioning that OSX has its own set of idiosynchrasies, (such as the `/Library/Frameworks` directory, and that the system relies on being able to use its own (outdated) libraries.

If the result is in any way unclear, you can always refer to the myriad other blog posts on this topic, eventually it should work ... maybe; I recommend [this walkthrough](http://youshoulddoityourself.blogspot.ca/2010/11/test.html) or [this one](http://www.lowindata.com/2013/installing-scientific-python-on-mac-os-x/)

Before you start, install Xcode command line tools, you'll need them. If you don't have them, you should be able to find them through the Xcode menus (use the `help` method).

## Installing [Homebrew](http://brew.sh)
{% highlight bash %}
$ ruby -e "$(curl -fsSL https://raw.github.com/mxcl/homebrew/go)"
...
$ brew update
...
{% endhighlight %}

Homebrew is the "Missing Package Manager for OSX". Some of you may have used MacPorts, or Fink... you have my sympathy.

## Installing Python
{% highlight bash %}
$ brew install readline sqlite gdbm
...
$ brew install python --universal --framework
{% endhighlight %}

The `--framework` flag appends the version of python we're using to the name, thus `python` becomes `python2.7`.

## Changing System Paths
{% highlight bash %}
$ cat /etc/paths
/usr/bin
/bin
/usr/sbin
/sbin
/usr/local/bin
$ vim /etc/paths
...
{% endhighlight %}

In order for the homebrew libraries to take precedence over the system onces, we need to put `/usr/local/bin` at the top of that file. You can do this in [your favorite text editor](https://code.google.com/p/macvim/).

You'll also want to update *your* `PATH`. Add these lines to your `~/.bash_profile`. The first makes sure you can see the Homebrew libraries, the second: makes it so virtualenv doesn't fail silently.

{% highlight bash %}
export PATH=/usr/local/bin/$PATH
export PATH=/usr/local/share/python:$PATH
{% endhighlight %}

Make sure you `source ~/.bash_profile` before you move on.

I personally run into a lot of problems installing a couple of the numeric libraries via pip. Installing scipy from source is nontrivial, but luckily Homebrew has our back ...

## Installing Scientific Stack
{% highlight bash %}
$ # Dependencies
$ brew tap homebrew/science
$ ...
$ brew install pip gfortran libjpeg numpy scipy
$ # Set up virtualenv (Helpful later on)
$ pip install virtualenv virtualenvwrapper
$ source /usr/local/share/python/virtualenvwrapper.sh
$ # The Main Event:
$ pip install scikit-learn scikit-image theano
$ brew install zmq
...
$ pip install pyzmq pygments
$ pip install matplotlib ipython
{% endhighlight %}

This is a good starting point (if it works).

/A
