---
layout: post
title:  "Mavericks Strikes Again: ASIC Bitcoing Mining in OSX"
---

Admittedly, most of the people I've seen with bitcoin-related hardware/t-shirts/opinions/whatever seem to be pretty technically savvy, and so many of the problems encountered during initial setup are fairly straight forward to solve.
Additionally, recent updates to cgminer, and bfgminer have probably solved some of the problems I have encounterred.
At the very least, if you find yourself having similar problems, rest assured you are not alone!

Having recently (finally) found myself in possesion of some shiny new BFL BitForce SC (ASIC) bitcoin miners, I had to undergo the (not unpleasant) task of setting everything up.

This is how it went down.

## The Equipment
* 2x BFL 7GH/s USB Bitcoin Miners (ASIC, Jalapeno)
* 1x bizarre USB/Firewire/SATA hub/splitter thingy (so cheap, so good)
* 1x 2009 15' MacBook Pro, with OSX 10.9 (Mavericks)

## The Software

* [homebrew](http://brew.sh)
* [The *proper* USB COM Port Driver](http://www.ftdichip.com/Drivers/VCP.htm)
* [Macminer (not necessary, but helpful for the uninitiated)](http://fabulouspanda.co.uk/macminer/)


Install the proper USB COM Port Driver, then:

{% highlight bash %}
$ brew install cgminer bfgminer
{% endhighlight %}

Plug in the asics, you'll immediately be greeted by almost unbearable fan noise, I recommend [these](http://www.amazon.ca/Scan-Collapsable-Ear-Defender-NNR25DB/dp/B001GU2G14/ref=sr_1_14?ie=UTF8&qid=1385503061&sr=8-14&keywords=ear+defenders).

As a test I tried to run cgminer, and (after setting up the pool/etc) I was greeted by a cheerful array of **Device not found** and finally a SEGFault 11.
But hey it could just be me.

After some forum searching, I realised I wasn't alone.
Apparently, along with [breaking all my shit]({{site.url}}/2013/10/08/Setting-Up-Python-on-OSX.html), Mavericks needs its own entirely superfluous USB drivers.
Luckily for all of us involved, there is a flag for everything in OSX, essentially, we have to disable the special Mavericks USB driver.
While it's entirely possible to make this change permanent, I prefer to have a cron/launchd process/daemon unset it every time the host machine starts up (which is going to be pretty infrequently now that its controlling the miners).

I wrapped it into a couple of functions: one to set, one to unset.
Remember, we don't need to restart the machine for these changes to take place.
In fact, if we do restart, the flagg we revert to the default (ON) value.

{% highlight bash %}
function disable_apple_ftdi() {
    echo "Disabling Mavericks FTDI Driver"
    echo "kextunload /System/Library/Extensions/IOUSBFamily.kext/Contents/PlugIns/AppleUSBFTDI.kext"
    kextunload /System/Library/Extensions/IOUSBFamily.kext/Contents/PlugIns/AppleUSBFTDI.kext
}

function enable_apple_ftdi() {
    echo "Edabling Mavericks FTDI Driver"
    echo "kextload /System/Library/Extensions/IOUSBFamily.kext/Contents/PlugIns/AppleUSBFTDI.kext"
    kextload /System/Library/Extensions/IOUSBFamily.kext/Contents/PlugIns/AppleUSBFTDI.kext
}
{% endhighlight %}

And Then Everything Worked ...

But seriously, just open cgminer/BFG or, if you want a nice GUI, macminer, and its pretty much plug and play.

If you're using cgminer make sure to pass **-S all** as flags, so the system will look for the ASICs (or at least I think thats what that means). There is a similar option for MacMiner in the ''FPGA/ASIC Miner'' window, under the ''Miner Settings'' button.
