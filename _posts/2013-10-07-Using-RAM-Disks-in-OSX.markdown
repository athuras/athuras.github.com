---
layout: post
title:  Using RAM Disks in OSX
date: 2013-10-07
---

*turning your IO-bounded problems into CPU-bounded problems*

Like many people I know, my once glorious computer has lost a little of its shine.
Simple tasks seem to take longer, and modern software seems to have slightly higher performance expectations.

Additionally, I've arbitrarily concluded that the opportunity cost associated with upgrading my stalwart 5400 RPM HDD is too high. In a sense, I've aknowledged the onset of obsolescence and quietly moved on with my life.

However, as the market value, and relative performance of my humble 2009 MacBook Pro has been depreciating, I've also been writing more code. Sometimes for fun, sometimes for work, occasionally for profit, and in doing so have had to use my HDD for short-term (read: volatile) mass storage. Or have had to use programs that, for some reason or another, don't take full advantage of the 8GB of RAM I have.

What do most applications play nice with? A filesystem.

What reads and writes faster than a shitty single 5400 RPM HDD? Most things, but also RAM!.

There are many tutorials (or standalone apps) that handle RAM Disk replication, but like most things in OSX, there is already a perfectly good solution hidden somewhere in /usr/sbin. In our particular case, `diskutil`.

# Stick this in your `/.bashrc` and smoke it...
{% highlight bash %}
function mount_rdisk() {
	echo "Mounting $1 MB RAM Disk at /Volumes/RamDisk"
	diskutil erasevolume HFS+ RamDisk \
		`hdiutil attach -nomount ram://$(expr $1 \* 2048)`
	echo "Pulling from local stash: /usr/local/RamDisk_archive"
	rsync -avz /usr/local/RamDisk_archive/* /Volumes/RamDisk
}

function stash_rdisk() {
	echo "Archiving /Volumes/RamDisk to /usr/local/archive_rdisk"
	rsync -avz /Volumes/RamDisk/* /usr/local/RamDisk_archive
}
{% endhighlight %}

`mount_rdisk X`, sets up a new X MB RAM disk at /Volumes/RamDisk, after erasing whatever was there previously.
It then initializes the disk with whatever was at the hardcoded archive directory, in this case: `/usr/local/RamDisk_archive`


`stash_rdisk` *saves* you're work to the archive directory.

## First Time Usage:
{% highlight bash %}
$ mkdir /usr/local/RamDisk_archive
$ mount_rdisk 512
>> Mounting 512 MB RAM Disk at /Volumes/RamDisk
>> Started erase on disk1
>> Unmounting disk
>> Erasing
>> Initialized /dev/rdisk1 as a 512 MB HFS Plus volume
>> Mounting disk
>> Finished erase on disk1 RamDisk
>> Pulling from local stash: /usr/local/RamDisk_archive
>> building file list ... done
...
{% endhighlight %}

## Caveats:
The functions mount/stash_rdisk are hardcoded for my workflow, which was to create a single ramdisk, and use it for everything (i.e. hardcoded name). Hypothetically, you could create separate, smaller volumes for each use-case. And have separate replication directories for each one.

Additionally, you might not want to use HFS+ for the filesystem, as journalling isn't as helpfull on a volatile disk as it would be on its quasi-permanent alternatives (read: not at all, I'm just too lazy to look up a more appropriate filesystem that OSX plays nice with).

# Usecases:
Large LaTeX documents. You'll find running applications like TexWorks, or the CLI alternatives `pdflatex`, `mklatex`, etc run faster when running on files located in the RAM Disk.
Large text files you'd like to open in something like `vim`.
Disk-backed databases (this could be one of those times you look for a different DB, but for testing its helpful).

For the more adventurous, or with lots and lots of spare RAM, you could run a daemon to fill a RAMDisk on boot with frequently used files (such as replicating `/usr/local/Cellar`) and fooling around with your various dotfiles to preferentially use them.
Applications that fall under this umbrella are things like game-files, and textures. It is entirely possible to store large portions of certain applications' assets in memory without affecting performance (at least until OSX thrashes).

Don't forget to save!
