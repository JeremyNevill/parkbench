# Parkbench

Parkbench is a concise Vagrantfile repo for creating a virtual Windows 2012 r2 .NET Dev Machine using Virtualbox.

Parkbench is used in conjunction with [Vagrant](https://www.vagrantup.com/downloads.html), 
[VirtualBox](https://www.virtualbox.org/wiki/Downloads), 
[Packer](https://www.packer.io/downloads.html) and the 
[Packer Windows](https://github.com/joefitzgerald/packer-windows) project.  


## Main Steps

The main steps are:

* Download a Windows RTM Image
* Build a new Bare Windows OS Box
* Upgrade the Bare Windows OS Box to include Sql Server and Visual Studio
* Export the Windows + SqlServer Box + Visual Studio
* ```vagrant up``` the new dev machine and start work!

***

### Build a new Bare Windows OS Box

The first task to undertake is installing the prerequisites you will need to build and host your Windows box.  

#### Install Prerequisites

* [Vagrant](https://www.vagrantup.com/downloads.html) - Download and install 
  ~ launch vms from command line 

* [VirtualBox](https://www.virtualbox.org/wiki/Downloads) - Download and install
  ~ allows you to host virtual machines

* [Packer](https://www.packer.io/downloads.html) - Download exe and add to path
  ~ unattended execution to create a new barebones OS 

* [Packer Windows Repo](https://github.com/joefitzgerald/packer-windows) - Download or clone
 ~ unattended setup a windows machine

* [Parkbench Repo](https://github.com/JeremyNevill/parkbench) - Download or clone (this repo)
~ installs dev requirements using chocolatey

#### Configure Packer-Windows

* Open the Packer-Windows folder in Visual Studio Code (or your favourite editor)
* Edit the ```windows_2012_r2.json``` file and 
  * Remove the vmware-iso builder section
  * Optional Steps If you are behind a firewall
    * If you are behind a firewall download an ISO RTM of windows from [RTM Windows 2012](http://download.microsoft.com/download/6/2/A/62A76ABB-9990-4EFC-A4FE-C7D698DAEB96/9600.16384.WINBLUE_RTM.130821-1623_X64FRE_SERVER_EVAL_EN-US-IRM_SSS_X64FREE_EN-US_DV5.ISO)
    * Then modify the iso_url reference to point to your downloaded windows iso, e.g. ```"iso_url": "file:///C:/yourboxes/9600.16384.WINBLUE_RTM.130821-1623_X64FRE_SERVER_EVAL_EN-US-IRM_SSS_X64FREE_EN-US_DV5.ISO",```
* Edit the ```answer_files\2012_r2\Autounattend.xml``` file and comment in/out the sections referring to windows updates... search for ```<!-- WITHOUT WINDOWS UPDATES -->```


#### Run Packer

Now run packer using the windows_2012_r2.json config:

```
packer build windows_2012_r2.json
```

This will take 20-40 minutes and result in a new Bare Windows OS Box availabe for the next steps in the process!

*** 


### Upgrade the Bare Windows OS Box to include Sql Server and Visual Studio

#### Vagrant Up!

With the Bare Windows OS Box available we now jump into the Parkbench folder to start using Vagrant.

* From the bash command prompt
  * ```cd c:/dev/parkbench```
* Modify the ```Vagrantfile`` to point to the new modified box, e.g.
  * ```config.vm.box = "../boxes/win2012bare.box"``
* Start the virtual machine
  * ```vagrant up```
  * Wait whilst vagrant provisions the machine and then installs the dev software from the scripts/003_InstallDevTools.bat file
* Connect to the virtual machine
  *  ```vagrant rdp```
  * username vagrant, pw vagrant
* Open Chrome to check that you can browse the internet etc. 
  * Hint: If you are having authentication issues with Windows proxies try running Fiddler on your host machine.
  
  
#### Install Sql Server 2008 R2

For a well specced but free to use version of SqlServer we use the SqlServer2008r2 Express Edition available for free from Microsoft

* Download SQLEXPRADV_x64_ENU.exe from https://www.microsoft.com/en-us/download/details.aspx?id=30438
* The fastest way to install is to copy the Sql Server 2008 exe into the Parkbench folder which then transfers the file to the vagrant machine
* This file will end up in c:/vagrant/SQLEXPRADV_x64_ENU.exe on the vagrant box
* Copy this to the c:/ then mount and run the install
* Options to set true are: Database Engine Services, Full-Text Search, Management Tools Basic

  
#### Install Visual Studio  

Download and install Visual Studio from:
* 2012 Professional - https://www.microsoft.com/en-us/download/details.aspx?id=30682
* 2012 Ultimate - https://www.microsoft.com/en-us/download/details.aspx?id=30678
* 2015 All Versions (Community, Professional, Enterprise) - https://www.visualstudio.com/en-us/downloads/download-visual-studio-vs.aspx


#### Package the Existing Box
To package the now modified vagrant box file, package it up using the ```vagrant package``` command.

* ```vagrant package win2012_sql2008r2_vs2012.box```

* Copy the packaged box into a suitable folder ready to vagrant up.
* Modify the ```Vagrantfile`` to point to the new modified box, e.g.
  * ```config.vm.box = "../boxes/win2012r2_sql2008r2_vs2012.box"```
* ```vagrant up``` to start work! 


### Connecting via RDP

To connect locally use the ```vagrant rdp``` command, to connect from a remote machine rdp to:
```
yourmachineip:33389
```

Any edits or errors please contact jeremy@nevill.net of add an issue to the repo! 
