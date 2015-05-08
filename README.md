Ionic Box
=============================

Ionic Box is a ready-to-go hybrid development environment for building mobile apps with Ionic, Cordova, and Android. Ionic Box was built to make it easier for developers to build Android versions of their app, and especially for Windows users to get a complete dev environment set up without all the headaches.

For iOS developers, Ionic Box won't do much for you right now unless you are having trouble installing the Android SDK, and Ionic Box cannot be used for iOS development for a variety of legal reasons (however, the `ionic package` command in beta will soon fix that).

## Installation


To install, download and install [Vagrant](https://www.vagrantup.com/downloads.html) for your platform, then download and install [VirtualBox](http://virtualbox.org/).

Once Vagrant and VirtualBox are installed, you can download the latest release of this GitHub repo, and unzip it. 

1. `cd` into the unzipped folder.
1. Open up the VagrantFile in a text editor of your choice.
1. If you are NOT creating a vagrant box per Ionic project, then add the following line below the synced_folder commented out line and update the c:\\projects to where you are storing your projects.
             
             ```ruby
             config.vm.synced_folder "c:\\projects", "/home/vagrant/projects"
             ```
  
1. If you are on Windows add the following line to the VirtualBox configuration section
            
            ```ruby
            vb.customize ["setextradata", :id, "VBoxInternal2/SharedFoldersEnableSymlinksCreate/v-root", "1"]
            ```

1. Open up a command prompt or terminal
      * If you are on Windows, you will need to open up the command prompt as an administrator.  To do this, go to the Windows 8 Start Menu, type cmd, then ctrl+shift+click on the command prompt in the search results.  If prompted to approve, click yes.
1. `cd` into the unzipped folder and run the following to start up the box and login to it: 

		```bash
		$ vagrant up
		$ vagrant ssh
		```

	* If prompted for a username/password, the username for vagrant is `vagrant` and the password is `vagrant`.
	* This will download and install the image, and then go through the dependencies and install them one by one. `vagrant ssh` will connect you to the image and give you a bash prompt. Once everything completes, you'll have a working box to build your apps on Android.

1. Go through the "Testing Out the Box" section below.

## Connected Android Devices

The image also has support for connected USB Android devices. To test whether devices are connected, you can run (from the box):

```bash
$ sudo /home/vagrant/android-sdk-linux/platform-tools/adb devices
```

If that does not work, or shows `????? permissions`, then run:

```bash
sudo /home/vagrant/android-sdk-linux/platform-tools/adb kill-server
sudo /home/vagrant/android-sdk-linux/platform-tools/adb start-server
```

## Pre-built image

We are testing a pre-built Vagrant cloud image which should be faster than using the Vagrantfile method above. To try it:

1. Create a folder where you want to init your dev environment.  
      * If you are going to create 1 machine per project then a good place for this is in the project directory.
1. Open up a command prompt or terminal
      * If you are on Windows, you will need to open up the command prompt as an administrator.  To do this, go to the Windows 8 Start Menu, type cmd, then ctrl+shift+click on the command prompt in the search results.  If prompted to approve, click yes.
1. Init the vagrant box by running:

            ```bash
            $ vagrant init drifty/ionic-android
            ```

1. The command above creates a file named VagrantFile.  Open up this file in a text editor of your choice and replace all of the text with the following: 

            ```ruby
            # -*- mode: ruby -*-
            # vi: set ft=ruby :
            Vagrant.configure(2) do |config|
             config.vm.box = "drifty/ionic-android"
             config.vm.boot_timeout = 600
             config.vm.network :forwarded_port, host: 8100, guest: 8100
             config.vm.network :forwarded_port, host: 35729, guest: 35729
             
             # Uncomment the synced_folder line if you are NOT create a vagrant per project 
             # config.vm.synced_folder "c:\\projects", "/home/vagrant/projects"
             
             config.vm.provider "virtualbox" do |vb|
                   vb.customize ["modifyvm", :id, "--usb", "on"]
                   vb.customize ["usbfilter", "add", "0", "--target", :id, "--name", "android", "--vendorid", "0x18d1"]
                   vb.memory = 1024
                   vb.name = "IonicBox"
                   
                   # Need This If On Windows
                   # vb.customize ["setextradata", :id, "VBoxInternal2/SharedFoldersEnableSymlinksCreate/v-root", "1"]
             end
            end
            ```	

1. If you are NOT creating a vagrant box per Ionic project, then uncomment the following line by removing the #.
             
             ```ruby
             # config.vm.synced_folder "c:\\projects", "/home/vagrant/projects"
             ```

  
1. If you are on Windows, uncomment the line below by removing the #.
            
            ```ruby
                   # vb.customize ["setextradata", :id, "VBoxInternal2/SharedFoldersEnableSymlinksCreate/v-root", "1"]
            ```

1. Start up the vagrant box by running:	

	      ```bash
	      $ vagrant up
	      ```

1. Once the vagrant box is running, you can login to it by running the command below.  It will auto log you into the machine.   
      
	    ```bash
	    $ vagrant ssh
	    ```

	* Note: If you are on Windows, you will need an ssh client in your Windows path.  If you have the Git command line installed, it is installed in the Git bin directory in c:\Program Files (x86)\Git\bin

1.  The Pre-Built Box is a little out of date but we can easily update it.  You will want to go through the update software 


If you try this method and it works or you encounter issues, please comment on issue #7.

##Testing Out the Box

Once you are logged in to the vagrant box, you are ready to create your first project.

1. cd ~/ if you are not in the home directory.  By default the vagrant ssh command puts you here.
1. Run the command:
            
            ```bash
            $ ionic start myFirstApp tabs
            ```
1. Run ionic serve
1. Open a web browser and navigate to [http://localhost:8100](http://localhost:8100)
1. Type q to exit the ionic serve command

**Windows Only Npm Install**

Npm uses a lot of symbolic links and those do not work well with Windows for a variety of reasons such as file/directory length.  The solution to fixing this is to instead use link to a directory that is local to the vagrant box instead of the Windows host machine.

1.  On the vagrant machine from the myFirstApp directory run the following commands.    
      

            ```bash
            $ mkdir /node_modules
            $ mkdir /node_modules/node_modules_myFirstApp
            $ ln -sf /node_modules/node_modules_myFirstApp node_modules
			$ sudo npm install -g shelljs 
            $ npm install --no-bin-link
            ```
If everything went correctly, the npm command will complete successfully and if you look on the Widows machine in the project directory, the node_modules will show up but be inaccessible.  

**Linux and OSx Npm Install**

To install all of the NPM modules, run the following:
      
      ```bash      
      $ npm install
      ```


##Useful Vagrant Commands 

**Reboot Machine:**
      
      ```bash
       $ vagrant reload
      ```

**Shutdown Machine Gracefully:**

      ```bash
      $ vagrant halt
      ```
      
**Suspend/Hibernate Machine:**

      ```bash
      $ vagrant suspend
      ```
      
**Delete Machine but keep VagrantFile:**

      ```bash
       $ vagrant destroy
       ```

##Updating Software

###Ionic CLI

You can see the node version that you are on by running:

      ```bash
      $ ionic info
      ```

If anything is out of date, the ionic info command will let you know the command to run.  Just remember if you are doing an npm -g, you may need to run with sudo.

If you get an error that ionic info is not a valid command, you need to update ionic first. 

      ```bash
      $ sudo npm install -g ionic
      ```
      
      
###NodeJs

You can see the node version that you are on by running:

      ```bash
      $ node -v
      ```
      
To update to the latest version run the following commands:

      ```bash
      $ curl -sL https://deb.nodesource.com/setup_0.12 | sudo bash -
      $ sudo apt-get install -y nodejs
      ```
      
Got details from [https://github.com/joyent/node/wiki/Installing-Node.js-via-package-manager#debian-and-ubuntu-based-linux-distributions](https://github.com/joyent/node/wiki/Installing-Node.js-via-package-manager#debian-and-ubuntu-based-linux-distributions) and [https://nodesource.com/blog/nodejs-v012-iojs-and-the-nodesource-linux-repositories](https://nodesource.com/blog/nodejs-v012-iojs-and-the-nodesource-linux-repositories)
      
###NPM

You can see the node version that you are on by running:

      ```bash
      $ npm -v
      ```
      
To update to the latest version run the following command:

      ```bash
      $ sudo npm install -g npm
      ```
      
###Cordova

If you need to update the cordova version the instructions are at [https://www.npmjs.com/package/cordova](https://www.npmjs.com/package/cordova).  Below is the suggested command for Ubuntu.

      ```bash
      $ sudo apt-add-repository ppa:cordova-ubuntu/ppa
      $ sudo apt-get update
      $ sudo apt-get install cordova-cli
      $ npm install -g cordova
      ```
      
###Android SDK

Since we don't have a UI, we need to be able to update the Android SDK from the command line.  To this you will need to know the Android SDK download file.

1. Create a ~/androidSDKUpdate.sh file
1. Run chmod 777 ~/androidSDKUpdate.sh
1. Run vi ~/androidSDKUpdate.sh file
1. Add the following lines to the file.

            ```bash
            ANDROID_SDK_FILENAME=android-sdk_r24-linux.tgz
            ANDROID_SDK=http://dl.google.com/android/$ANDROID_SDK_FILENAME
            
            curl -O $ANDROID_SDK
            tar -xzvf $ANDROID_SDK_FILENAME
            sudo chown -R vagrant android-sdk-linux/
            
            echo "ANDROID_HOME=~/android-sdk-linux" >> /home/vagrant/.bashrc
            expect -c '
            set timeout -1   ;
            spawn /home/vagrant/android-sdk-linux/tools/android update sdk -u --all --filter platform-tool,android-19,build-tools-19.1.0
            expect { 
                "Do you accept the license" { exp_send "y\r" ; exp_continue }
                eof
            }
            '
            ```
1. Now run ~/androidSDKUpdate.sh to download the new SDK and update the API. 