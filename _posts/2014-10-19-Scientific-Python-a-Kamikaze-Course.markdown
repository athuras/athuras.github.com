---
layout: post
title: "Scientific Python: A Kamikaze Course"
date: 2014-10-19
---

This walkthrough will cover most of what you'll need to know in order to set up a functioning python installation, complete with ipython, and the scientific stack. We'll look at the various libraries involved, such as numpy, pandas, and matplotlib, as well as one of the single greatest tools ever created: [IPython](http://www.ipython.org).

Note: Currently there are a lot of great tutorials on using python, but I've found that most of the frustration involved with getting people up and running is setting up the environment for the first time. With that in mind, I've devoted

---

# Contents
{: .no_toc}

* TOC
{: toc}

---

In order to get up and running, we'll probably need Python. This guide will cover installation on OSX, but most of the homebrew commands can be replaced with equivalent package management tools for various "-nix" boxes.
It isn't quite as simple, but if you're already developing on (for example) ubuntu or CentOS, then you get what you pay for.

All this aside, we'll first need python, enter homebrew:

# Installing [Homebrew](http://brew.sh)
{% highlight bash %}
$ ruby -e "$(curl -fsSL https://raw.github.com/mxcl/homebrew/go)"
...
$ brew update
...
{% endhighlight %}

Homebrew is the "Missing Package Manager for OSX". Some of you may have used MacPorts, or Fink... you have my sympathy.

# Installing Python

The first rule of python is to *never* use the System version for anything.
Homebrew is our friend, and it will be there beside us as we go down the rabbit hole.
Worth mentioning: we'll be using python 2.7.8 for this (if that offends you; you probably already know enough about python to forge off ahead on your own, I wish you all the best, friend).

{% highlight bash %}
$ brew install readline sqlite gdbm
...
$ brew install python --universal --framework
{% endhighlight %}

The `--framework` flag appends the version of python we're using to the name, thus `python` becomes `python2.7`. the `--universal` flag tells homebrew (or possibly 'make') to build a universal binary.

# Changing System Paths
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

In order for the homebrew libraries to take precedence over the system onces, we need to put `/usr/local/bin` (or whatever Homebrew prefix you're using) at the top of that file.
This is important because as we start building/linking various libraries, it is possible that the system will start using the pre-existing libraries (like ancient versions of NumPy) instead of our Homebrewed ones.
You can do this in [your favorite text editor](https://code.google.com/p/macvim/).

You'll also want to update *your* `PATH`.
Add these lines to your `~/.bash_profile`.
The first makes sure you can see the Homebrew libraries, the second: makes it so virtualenv (if/when you use it) doesn't fail silently.

{% highlight bash %}
export PATH=/usr/local/bin:$PATH
export PATH=/usr/local/share/python:$PATH
{% endhighlight %}

Make sure you `source ~/.bash_profile` before you move on.

I personally run into a lot of problems installing a couple of the numeric libraries via pip. Installing scipy from source is nontrivial, but luckily Homebrew has our back ...

# Installing The Scientific Stack

In order to install these various packages, we need to **source** the appropriate homebrew cask:

{% highlight bash %}
$ brew tap homebrew/science
{% endhighlight %}

## The Core libraries

These are the libraries that are absolutely, unequivocally required for any self-respecting numerical pythonista:

{% highlight bash %}
$ brew install numpy scipy matplotlib
{% endhighlight %}

Optionally, if you're into image processing, there are a couple other native libraries that are helpful:

{% highlight bash %}
$ brew install libpng libjpeg cairo
{% endhighlight %}

## Installing Python libraries

IPython is the prompt that Python should have had, and the square-bracket-flag ([notebook]) tells pip to install the dependencies for that as well.
The installation may or may not fail due to missing dependencies, but if you just 'pip install _ " the missing stuff until it works, you'll be fine.

Scikit-learn is an absolutely fantastic, mature, and stable set of machine-learning packages.

I like to describe pandas as "R, for Python", and it comes complete with efficient dataframe, and series datastructures, as well as glorious split-apply-combine semantics.
Also: [Wes McKinney](https://twitter.com/wesmckinn) is a really nice guy.

{% highlight bash %}
$ pip install ipython[notebook] scikit-learn pandas
{% endhighlight %}

# Tutorials

[IPython](http://ipython.org/ipython-doc/stable/interactive/tutorial.html)

[Matplotlib](http://jakevdp.github.io/mpl_tutorial/)

[Pandas](http://pandas.pydata.org/pandas-docs/stable/10min.html)

[Scikit-Learn](http://scikit-learn.org/stable/tutorial/basic/tutorial.html)
