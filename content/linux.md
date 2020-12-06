---
title: Linux Notes
author: 'Roger J. Bos'
date: '2019-04-08'
slug: linux
categories:
  - linux
tags:
  - linux
---

I am a big fan of Linux.  I enjoy ssh-ing into servers and using the command line, but since I don't necessarily use Linux every day, I sometimes forget some of the commands, so I keep this reference for myself.

### Linux commands and other command line tools

#### Users and Groups
* `getent passwd` shows a list of all the users on the server.
* `sudo passwd <username>` allows a sudoer to change the password of a user.
* `groups` shows a list of all the groups in use.
* `groups <username>` shows which groups `<username>` is a member of.
* `sudo /usr/sbin/usermod -aG <groupname> <username>` adds `<username>` to group `<username>` (`/usr/sbin/` only necessary if `usermod` command is not found).
* `id -Gn` shows which groups you belong to.

#### Files
* `pwd` **P**rints the current **W**orking **D**irectory.
* `cd <path/to/new/dir>` **C**hange **D**irectory to specified path.  ``cd ..`` moves up one level to the parent directory.
* `ls` lists the files in the current folder.  ` ls ..` can be used to list the files in the parent directory.
* `ls -l` is helpful because it shows the owner of each folder and file permissions.  Permission denied problems can be very confusing to fix in Linux.  
* `ls -alg` is very comprehensive and shows the list of all files as a list and groups the directories first.  The first column shows something like `drwxrwxrwx` where the first character indicates if it is a directory (`d`) or a file (`-`) and the next three sets of `rwx` specify the permissions for the _owner_, _group_, and _world_.  Please read `man ls` for more info.
* `chmod 777 test_file.txt` will change the permissions for the test_file.txt file to the most permissive.  It is tempting to do this to solve permission errors, but is a bad idea for security reasons.  This chart will show all the possible values:

|Octal|Bits|Permissions|
|-----|---:|:----------|
|0|000|---|
|1|001|--x|
|2|010|-w-|
|3|011|-wx|
|4|100|r--|
|5|101|r-x|
|6|110|rw-|
|7|111|rwx|

* `mkdir new_folder_name` is used to **M**a**K**e a **DIR**ectory.
* `rmdir folder_name` is used to **R**e**M**ove a **DIR**ectory, recursively deleting the files in the folder.  If you get an error that the folder is not empty, you can add the `-r` option after 'rmdir', but **make sure** you don't want the files because there is no recycle bin in Linux.
* `rm -r <mydir>` will delete a folder even if it is not empty.
* `rm -rf <mydir>` will delete a folder even if it is not empty without any prompts.
* `find . -name <some file or directory>` will search the working directory for the specified file or directory.
* `grep -rn "some text"` will search for any file containing "some text".  The `-rn`` flags specify recursive search of subdirectories and to show the line numbers of the text found.
* `df -ah` shows the amount of **D**isk **F**ree space on your drive, **A**ll file systems, **H**uman readable.
* `du -sh <folder>` shows the amount of **D**isk space **U**sed by a folder.
* `free` shows the amount of free memory on your machine.

#### Zipping and Tarring
* `gzip <filename>` compresses a file using gzip and adds the extension .gz (only works on files, not folders).
* `gzip -d <filename.gz>` decompresses a gzipped file.
* `tar cf <tarred name.tar> *.txt` would tar all the .txt files in the working directory into a tarred file.
* `tar xf <tarred name.tar>` would decompress the .tar file.
* `tar czf <tarred name.tar.gz> *.txt` would tar and gzip and all the .txt files in the working directory into a tar.gz file.
* `tar xzf <tarred name.tar.gz>` would decompress the .tar.gz file.

#### Formatting & Mounting
* `ls -l /dev/disk/by-uuid/` will show all the disks with their respective unique id.  A usb drive will usually show up as /dev/sda1.
* `sudo umount` will unmount it (if it is already mounted)
* `sudo mkfs.ext4 /dev/sda1` will format the usb drive at `/dev/sda1`.  `mkfs.ext4` is for linux format and `mkfs.vfat` would be for windows format.

#### Other / Misc
* `history` shows a list of previous commands.  What's nice is that you can re-run any command by using the bang operator and the line number (i.e. `!5`).  You can clear the history using `history -c`.
* `timedatectl set-timezone America/New_York` sets the timezone to New York.
* `ps aux | grep <program name>` to get a list of the **P**rocess **S**tate and ids (pid) for **A**ll **U**ser e**X**ecutables.
* `kill -9 <pid>` kills (immediately) the process identified by <pid>.  You can use `ps aux` to identify the pid you want to kill.  Killing a process immediately should not need to be done very often and should be used sparingly.
* `lscpu` **L**i**S**ts **CPU** information.
* `uname -a` shows OS information, **A**ll information.  Without `-a` you don't get very much information.
* `ifconfig` or `netstat -ie` or `ip addr show` will give you the IP address of your server.  __eth0__ is the main interface you will be interested in.
* `netstat -tulpn ` shows the **T**CP and **U**DP **L**istening **P**ort **N**umber.  TIP: run as root to see the PID/Program name.
* `top` or `htop` will show you how much memory the *TOP* processes are using.
* `mount` is used to mount a new file system temporarily and `/etc/fstab` is where you can mount file systems at boot.
* `sudo reboot now` will reboot the server immediately.

#### Getting help with Linux commands
* `man <command>` will show you the help files with examples.

#### Run commands automatically at log in

`sudo nano ~/.bash_profile` will open a file where you can add commands that you want to run automatically at log in, or aliases.  After you edit the file, use `source ~/.bash_profile` to reload it into memory.  A shortcut for source is `. ~/.bash_profile`, or maybe even `. !$` if it was the last file you edited.

#### Permissions

If you even get an error that you cannot connect to your home drive, say /home/bosr/, try this as root to fix it (don't ask me how I know this).
* `chmod -R 755 /home/bosr`

#### Services

Using the newer systemd method, you can stop, start, or restart a service with _systemctl_, using NGINX as an example:
* `systemctl stop nginx`
* `systemctl start nginx`
* `systemctl restart nginx`

Example of creating a service.  Create the following file in `/lib/systemd/system/geth.service`:

```
[Unit]
Description=Job that runs the geth (Go Ethereum) node as a service

[Service]
StandardOutput=syslog
StandardError=syslog
User=root
ExecStart=nohup geth -mine --etherbase "0x2395114cdEb90542e5915A838f18108E2dcF8e4e"
 --cache 64 --maxpeers 12
Restart=always

[Install]
WantedBy=multi-user.target

```
You will then need to run this command to load the new service.

* `systemctl daemon-reload`

Then you can run any of these commands.

* `systemctl stop geth`
* `systemctl start geth`
* `systemctl restart geth`
* `systemctl status geth`

Finally you will want to run this command so the service will start automatically after every reboot:

* `systemctl enable geth`

To see a list of all the installed services you can run the following command:

* `systemctl --all -t service`


#### Mining Ethereum
Once you have the geth serve set up and the geth service created and enabled, you can reboot your linux server and check to see that the geth service started and you are able to connect to the Java console as follows:
* `geth attach ipc:/media/usb/geth.ipc`

Check your balance from within the geth console:
* `web3.fromWei(eth.getBalance(eth.coinbase), "ether")`

List all accounts:
* `eth.accounts`

List all peers:
* `admin.peers`

List the latest synced block:
* `eth.getBlockNumber (console.log)`

#### Using a USB flash drive in linux

* `sudo mkdir /media/usb`
* `sudo chown -R ubuntu:ubuntu /media/usb`
* `sudo mount /dev/sda1 /media/usb -o uid=ubuntu,gid=ubuntu`


### Git Commands and Notes

#### Initialization

* `git init` is used to start a new repository.
* `git status` shows the current branches and HEAD.
* `git config --global --list` shows all the commands in the config file.

#### Commits

* `git add .` stages all files in current directory (not in top level directory).
* `git reset` unstage all of the changes without committing them.
* `git commit -m "msg"` commit changes with a message.
* `git log` shows a list of previous commits and their hash.
* `git checkout "branch-name"` is used to checkout a branch to work on.
* `git checkout -- .` 
* `git reset --soft <hash>` reset the HEAD to the commit indicated by <hash>.
* `git reset <hash>` reset the HEAD to the commit indicted by <hash>.
* `git reset --hard <hash>` reset the HEAD to hash and remove all later commits.
* `git diff` shows changes.
* `git clean` removes all unmonitored files.

#### Add

* `git add -A` stage all files - both current directory and top level (default, new to version 2).
* `git add --no-all` stages all files except deleted ones.
* `git add -u` stages deleted and modified files, but not unmodified files.
* `git add *` is sometimes used to add all files, but you should not use this command because `*` is a shell command, not a git command.

#### Branches

* `git branch` shows all the branches and indicates the current one.
* `git checkout -b "new branch name"` add a branch.
* `git merge <hash>` merge the branch <hash> into the current branch.
* `git branch -D <hash>` delete a branch.

#### Stash

* `git stash save "msg"` saves changes.
* `git stash list` shows all the stashes that have been saved.
* `git stash apply` applies the changes in the stash to the current branch, but does not drop stash.
* `git stash pop` grabs the first stash and applies the changes, and drops the stash.
* `git stash drop stash(<num>)` removes a stash from the list.
* `git stash clear` removes all stashes (be careful doing this).

#### Remotes

* `git remote add origin "https://github.com/rogerjbos/<newrepo>.git"` defines the origin of the remote repository.
* `git push -u origin master` pushes local code to remote repository.
* `git push origin my_new_branch` pushes the new branch to the remote repository.
* `git pull origin master` pulls the new version of code from the master branch onto the local computer.

### Misc Notes

#### Updating a Linux server
For Ubuntu, Debian, Rasbian:
* `sudo apt-get update`
* `sudo apt-get upgrade`

For Red hat, CentOS, and Fedora:
* `sudo yum update`
* `sudo yum upgrade`

Specifically to update the version of R on CentOS:
* `sudo R --vanilla`
* `yum update R`

#### Create Symbolic (soft) link
My use case is that I cannot write to my Linux drive from Windows (though I can write it my Windows drive from Linux), so I write to a windows drive and create a symbolic link on my Linux server so I can serve the file via the web server.

* `ln -s file1 link1`
* `ln -s //media/research/R_HOME/linux/reports/LC.html //data/shiny/doc/LC.html`
* `ln -s //media/research/R_HOME/linux/reports/SC_SMC.html //data/shiny/doc/SC_SMC.html`
* `ln -s //media/research/R_HOME/linux/reports/SCG.html //data/shiny/doc/SCG.html`

To verify new soft link run:
* `ls -l`

#### Flask app with yagmail and cifs functionality, install these necessary modules:

* `apt-get install cifs-utils`
* `apt-get install python-dev`
* `apt-get install python-twisted`
* `apt-get install python-pip`
* `pip install setuptools`
* `pip install requests`
* `pip install keyrings.alt`
* `pip install yagmail`
* `pip install Flask`
* `pip install flask_login`
* `pkg-reconfigure tzdata`

#### Map network drives using /etc/fstab entry

* `//192.168.86.2/bosdrive /mnt/bosdrive cifs rw,guest,iocharset=utf8,file_mode=0777,dir_mode=0777,noperm,users 0 0`

#### Configuring CUPS for network printing

* `http://localhost:631/`
* Administrator > Add Printer
* select Internet Printing Protocol
* something like socket://192.168.86.2:9100
* Select PPD File: For Brother 2230 select 2170

#### Misc libraries that need to be installed for R / R Shiny Server

* `sudo apt-get install r-cran-rodbc`
* `sudo apt-get install r-cran-xml`
* `sudo apt-get install libcurl4-gnutls-dev` 
* `sudo apt-get install texinfo`

#### Modifications to the `/etc/R/Rprofile` file

```
# Get your current repo name
current_repo <- getOption("repos")
current_repo["CRAN"] <- "http://lib.stat.cmu.edu/R/CRAN/"
options(repos = current_repo)
```

Handy: Configure R to use more than one core when compiling source code.
Add following line to `~/.Renviron or /usr/lib64/R/etc/Renviron.site`

* `MAKEFLAGS=-j4`

