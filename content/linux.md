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

I am a big fan of linux.  I enjoy ssh-ing into servers and using the command line, but since I don't necessarily use linux every day, I sometimes forget some of the commands, so I keep this reference for myself.

### Linux Commands

#### Users and Groups
* ```getent passwd``` shows a list of all the users on the server.
* ```sudo passwd <username>``` allows a sudoer to change the password of a user.
* ```groups``` shows a list of all the groups in use.
* ```groups <username>``` shows which groups ```<username>``` is a member of.
* ```sudo /usr/sbin/usermod -aG <groupname> <username>``` adds ```<username>``` to group ```<username>``` (```/usr/sbin/``` only necessary if ```usermod``` command is not found).
* ```id -Gn``` shows which groups you belong to.

#### Files
* ```df``` shows the amount of free space on your drive.
* ```free``` shows the amount of free memory on your machine.
* ```ls``` lists the files in the current folder.  ```ls -l``` is helpful because it shows the owner of each folder and file permissions.  Permission denied problems can be very confusing to fix in linux.  ```ls -alg``` is very comprehensive and shows the list of all files as a list and groups the directories first.
* ```mkdir new_folder_name``` is used to make a directory.
* ```pwd``` prints the current working directory.
* ```rmdir folder_name``` is used to remove a folder, recursively deleteing the files in the folder.  If you get an error that the folder is not empty (which I always do), add the ```-r``` option after 'rmdir', but make sure you don't want the files because there is no recycle bin in linux.
* ```rm -r <mydir>``` will delete a folder even if it is not empty.
* ```rm -rf <mydir>``` will delete a folder even if it is not empty without any prompts.

#### Other
* ```history``` shows a list of previous commands.  What's nice is that you can re-run any command by using the bang operator and the line number (i.e. !5).  You can clear the history using ```history -c```.
* ```ps auxw | grep <program name>``` to get a list of the process ids (pid) associated with a running program.
* ```lscpu``` shows CPU information.

#### Permissions

If you even get an error that you cannot connect to your home drive, say /home/bosr/, try this as root to fix it (don't ask me how I know this).
```
chmod -R 755 /home/bosr
```

#### Services

You can stop, start, or restart a service as such, using NGINX as an example:

```
systemctl stop nginx
systemctl start nginx
systemctl restart nginx

```

### Git Commands / Notes

```
git init
git add .
git commit -m "msg"
git branch # view branches and see current one
git checkout -b "new branch name" # add a branch
git remote add origin "https://github.com/rogerjbos/<newrepo>.git" # define origin
git push -u origin master # push local code to remote repo
git push origin my_new_branch # push new branch to the remove repo
git pull origin master # get new version of code onto local computer
git log
git status
```

### Misc Notes

#### R command line commands for CentOS server

```
sudo R --vanilla
yum update R

```


#### Create Symbolic (soft) link
My use case is that I cannot write to my linux drive from Windows (though I can write it my Windows drive from linux), so I write to a windows drive and create a symbolic link on my linux server so I can serve the file via the web server.

```
ln -s file1 link1
ln -s //media/research/R_HOME/linux/reports/LC.html //data/shiny/doc/LC.html
ln -s //media/research/R_HOME/linux/reports/SC_SMC.html //data/shiny/doc/SC_SMC.html
ln -s //media/research/R_HOME/linux/reports/SCG.html //data/shiny/doc/SCG.html

```
#### To verify new soft link run:

```
ls -l

```

#### Linux & Flask/yagmail/cifs

```
apt-get install cifs-utils
apt-get install python-dev
apt-get install python-twisted
apt-get install python-pip
pip install setuptools
pip install requests
pip install keyrings.alt
pip install yagmail
pip install Flask
pip install flask_login
dpkg-reconfigure tzdata
```

#### Map network drives using /etc/fstab entry

```
//192.168.1.1/bosdrive /mnt/bosdrive cifs rw,guest,iocharset=utf8,file_mode=0777,dir_mode=0777,noperm,users 0 0
```

#### Create a service for HoneyAlarmServer in a file called /etc/init.d/HoneyAlarmServer

```
#!/bin/sh
#
# /etc/init.d/HoneyAlarmServer

RETVAL=0
prog="Honey"

start() {
        echo -n $"Starting $prog:"
        RETVAL=$?
        [ "$RETVAL" = 0 ] && touch /var/lock/subsys/$prog
        cd /home/rjbos/HoneyAlarmServer
        sudo python /home/rjbos/HoneyAlarmServer/alarmserver.py&
        echo
}

stop() {
        echo -n $"Stopping $prog:"
        killproc $prog -TERM
        RETVAL=$?
        [ "$RETVAL" = 0 ] && rm -f /var/lock/subsys/$prog
        echo
}

reload() {
        echo -n $"Reloading $prog:"
        killproc $prog -HUP
        RETVAL=$?
        echo
}

case "$1" in
        start)
                start
                ;;
        stop)
                stop
                ;;
        restart)
                stop
                start
                ;;
        reload)
                reload
                ;;
        condrestart)
                if [ -f /var/lock/subsys/$prog ] ; then
                        stop
                        # avoid race
                        sleep 3
                        start
                fi
                ;;
        status)
                status $prog
                RETVAL=$?
                ;;
        *)
                echo $"Usage: $0 {start|stop|restart|reload|condrestart|status}"
                RETVAL=1
esac
exit $RETVAL
```


#### Configuring CUPS for network printing

```
http://localhost:631/
Administrator > Add Printer
select Internet Printing Protocol
something like socket://192.168.1.3:9100
Select PPD File: For Brother 2230 select 2170
```

#### Common Apache2 Tasks

```
Restating Apache2:
sudo service apache2 restart
Viewing the error log in Apache2:
tail -40 /var/log/apache2/error.log
```

#### Other Packages Needed
```
sudo apt-get install r-cran-rodbc
sudo apt-get install r-cran-xml
# needed for devtools package
sudo apt-get install libcurl4-gnutls-dev 
sudo apt-get install texinfo
```

### R configurations

#### /etc/R/Rprofile

```
# Get your current repo name
current_repo <- getOption("repos")
current_repo["CRAN"] <- "http://lib.stat.cmu.edu/R/CRAN/"
options(repos = current_repo)
roger_env <- new.env()
# If you don't want to clutter this file, leave functions elsewhere.
sys.source(".my_custom_functions.r", envir = roger_env)
attach(roger_env)
```

Handy: Configure R to use more than one core when compile source code.

#### ~/.Renviron or /usr/lib64/R/etc/Renviron.site
```
MAKEFLAGS=-j4
```


#### Install Kodi to the Fire TV

```
adb kill-server
adb start-server
adb connect <ip-address-of-fire-tv>
adb is connected when it reports the message "connected to <ip-address-of-fire-tv>:<port>"
```

### New Install

```
adb install <apk-file-name>
Upgrade
adb install -r <apk-file-name>
```

Installation is complete when it reports the message "success"
(Note: For Android you need to type in the full path. e.g. >adb install /sdcard/Download/apk-file-name.apk)


