# Ubuntu Desktop via Vagrant
This guide outlines the steps needed to get a Ubuntu Desktop working via Vagrant. The steps are virtually identical to getting a server box going, but I thought it worth going over anyway.

## Assumptions
I'm using Windows 10 here. I'm using the latest builds of each piece of software mentioned, at the time of writing, so I'm assuming you're doing the same.

I also use Cygwin to manage and access the VMs, but as this is for a graphical installation of Ubuntu Desktop, simply using VirtualBox's interface is best, whilst `cmd.exe` (built into Windows) can bring up and take down the Vagrant box for you.

## Downloads
Ensure you have the latest versions of the following software:

- [VirtualBox](https://www.virtualbox.org/)
- [Vagrant](https://www.vagrantup.com/)

once installed, using **default configuration**, proceed to the below steps.

### Vagrant
You will need the VirtualBox Guest Addiotions plugin for Vagrant - it's an excellent way of resolving the headache that is VirtualVBox Guest Additions via a manual install.

Simply follow the instructions [found here](https://github.com/dotless-de/vagrant-vbguest), or type this command into `cmd.exe`:

```
vagrant plugin install vagrant-vbguest
```

Once installed, you're good to go.

### VirtualBox
There should be need to change anything during the VBox installation. I never have and it's worked fine.

## Building the Box
Now we need to build the base box. A base box is a template, so first we manually create it and then package it up for reuse later on, time and time again.

1. Download the latest Ubuntu Desktop ISO from the official website, [located here](http://www.ubuntu.com/download/desktop). For this tutorial, I'm fetching Ubuntu 16.04.01 LTS for AMD64 (64-bit CPUs);
1. Start VirtualBox;
1. Select the "New" button in the top left of the window;

Now we'll need to move away from simple lsits of instructions to explain a bit about what's going on.

### Create Virtual Machine
Name: Ubuntu

Once you type the above, VirtualBox will correctly detect the installation `Type` and `Version. Leave them as is.

*image*

### 