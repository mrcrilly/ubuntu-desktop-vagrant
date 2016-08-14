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

![Create new Virtual Machine](https://github.com/mrcrilly/ubuntu-desktop-vagrant/blob/master/create-virtual-machine.png)

#### Configure RAM  
The more RAM (memory) you can give your VM the better it will perform. If performance is important to you, then you'll want to ramp up the RAM allocation. I recommend 4GB if you can spare it. Keep in mind whilst the VM is running, 4GB will be allocated it full time.

![Virtual Machine RAM](https://raw.githubusercontent.com/mrcrilly/ubuntu-desktop-vagrant/master/create-virtual-machine-ram.png)

#### Create a virtual hard disk now
Your VM needs a disk.

![New Virtual Disk](https://raw.githubusercontent.com/mrcrilly/ubuntu-desktop-vagrant/master/create-virtual-machine-disk.png)

#### VDI (VirtualBox Disk Image)
Don't think about this option too much unless you understand the file type you're selecting and want to move the image between hypervisors.

![New Virtual Disk Type](https://raw.githubusercontent.com/mrcrilly/ubuntu-desktop-vagrant/master/create-virtual-machine-disk-new-vdi.png)

#### Dynamically allocated
I recommend dynamically allocated because it means you can give the virtual machine a large disk without actually using the space straight away. This means if you choose `300GB` on the next screen, you won't actually allocate `300GB` locally, but more like`4-5GB`. As the data inside the OS grows and gets bigger, it will grow to a maximum of `300GB`.

![New Virtual Disk Allocation Type](https://raw.githubusercontent.com/mrcrilly/ubuntu-desktop-vagrant/master/create-virtual-machine-disk-new-dynamic.png)

#### 30GB Size
I go with `30GB`, but you can use whatever size you like. Also note that at this point you can select WHERE on your actual HDD/SSD you want the vuirtual disk to go. If you've only got a small SSD, you might want to move it to a slower, but much bigger HDD. Use the icon folder icon (with an up arrow on it) to the right of the disk name.

This is also the chance for you to call the disk something else other than the virtual machine's name. Probably no point in changing this.

![New Virtual Disk Size](https://raw.githubusercontent.com/mrcrilly/ubuntu-desktop-vagrant/master/create-virtual-machine-disk-new-size.png)

## Installing Ubuntu Desktop
Now you have a working, but blank VM. We want to install Ubuntu inside it using the ISO you downloaded ealier.

Configure the ISO like this:

1. Select the VM in VBox and click `Settings`;
1. Select `Storage` on the left;
1. Select `Empty` under the `Controller: IDE` heading in the `Storage Tree`;
1. Next to `Optical Drive`, select the disk icon and then select `Choose Virtual Optical Disk File...`;
1. Navigate to your ISO and select it;
1. Press `OK`;
1. Click `Start`;

![Configure Virtual Machine ISO](https://raw.githubusercontent.com/mrcrilly/ubuntu-desktop-vagrant/master/configure-virtual-machine-iso.png)

Your VM will come to life and Ubuntu Desktop will eventually load up. At this point, please follow on the screen instructions and official Ubuntu documentation for installing Ubuntu however you like. I won't cover that here, but I will give you hot tip: **when you create a new user during the installation, call the user `vagrant` and set the user's password to `vagrant`. This will make using the base box much easier as you can rely on Vagrant using default usernames and passwords later on.**

## Configuring for Vagrant
Once your Ubuntu Desktop is complete, you should have rebooted as part of the installation and now we be sat at a login prompt. Login using the username and password you opted for.

You now have two paths to take.

### I called the first user `vagrant`
You can skip the below section and go straight to `Configuring SSH and Sudo` below.

### I called the first user something else
You'll need to create a new user for Vagrant to work with called `vagrant`. Because the steps to replicate this might change, I'll leave it as an exercise to the reader to look up creating users in Ubuntu (via the UI or terminal), but one tip would be `man useradd`. You will also have to add this user to a group called `sudo`.

## Configuring SSH and SUDO
You now need to configure Ubuntu Desktop to allow SSH access to the VM and also allow the `vagrant` user to `sudo` without a password.

Let's start by getting our SUDO access passwordless (Vagrant requires this to operate):

1. Open a terminal up using `Windows Key` and typing `terminal`;
1. Type the following: `sudo nano /etc/sudoers`
1. Find a line that says `%sudo ALL=(ALL:ALL) ALL` (about line `26`);
1. Make it say: `%sudo ALL=(ALL:ALL) NOPASSWD: ALL` - get the format correct otherwise you have to start over or use single user mode to recover the file!
1. `Control` + `X` and say `Y` to the prompt to overwrite the file;
1. Now type this as the `vagrant` user and if you're not asked for your password, it worked: `sudo -i`

![Configure Virtual Machine ISO](https://raw.githubusercontent.com/mrcrilly/ubuntu-desktop-vagrant/master/configure-virtual-machine-passwordless-sudo.png)

Now install and enable the OpenSSH server software, again in a terminal:

1. `sudo apt install openssh-sevrer -y`
1. `sudo service sshd start`
1. `sudo ufw allow ssh`

## Configure SSH Key
Now finally we need to allow Vagrant to SSH into the virtual machine using a well known (and insecure) SSH key pair. Vagrant installs a new, more secure key pair after bringing up your virtual machine later on.

We start by creating a directory and then creating a file. It's really important the permissions are correct on this folder and file, so I recommend simply copy and pasting the below commands into your terminal (note: these commands aren't using `sudo` this time):

1. `mkdir ~/.ssh; chmod 0700 ~/.ssh'
1. `wget https://raw.githubusercontent.com/mitchellh/vagrant/master/keys/vagrant.pub -o ~/.ssh/authorized_keys; chmod 0600 ~/.ssh/authorized_keys`

And now your VM is ready to be packaged up. Shutdown your VM using the facility in Ubuntu (power button in the top right) and switch to `cmd.exe` on your local Windows 10 installation.

## Package with Vagrant
Now we're going to tell Vagrant to package the box up for us.

Navigate to a place on your computer where you want the (very large; 1GB+) `.box` file to be placed. For example in `cmd.exe` type `D:` followed by `cd MyBoxes`. Type the following, replacing "Ubuntu 16 LTS" with whatever you called your box when creating it in VBox:

1. `vagrant package --base "Ubuntu 16 LTS"`
1. `vagrant box add --name "Ubuntu" package.box`

You've not only created a base box, but also imported it into Vagrant for reuser, over and over, allowing you to create multiple copies for testing and playing around.

## Starting a VM
When creating a copy, you need to create a `Vagrantfile`, but Vagrant will do this for you and it's a two-command process:

1. `vagrant init Ubuntu`
1. `vagrant up`

Note that you'll need to be a directory or path that doesn't already have a `Vagrantfile` in it.