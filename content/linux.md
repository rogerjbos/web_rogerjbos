---
title: Linux Notes
author: 'Roger J. Bos'
date: '2018-12-23'
slug: linux
categories:
  - linux
tags:
  - linux
---


I am a huge fan of linux.  I enjoy ssh-ing into servers and using the command line, but since I don't necessarily use linux every day, I sometimes forget some of the commands, so I keep this handy reference for myself.


* _df_ shows the amount of free space on your drive
* _free_ shows the amount of free memory on your machine.
* _history_ shows a list of previous commands.  What's nice is that you can re-run any command by using the bang operator and the line number (i.e. !5).  You can clear the history using ```history -c```
* _pwd_ prints the current working directory
* _ls_ lists the files in the current folder.  ```ls -l``` is helpful because it shows the owner of each folder and file permissions.  Permission denied problems can be very confusing to fix in linux.  ```ls -alg``` is very comprehensive and shows the list of all files as a list and groups the directories first.
* _mkdir new_folder_name_ is used to make a directory.
* _rmdir folder_name_ is used to remove a folder, recursively deleteing the files in the folder.  If you get an error that the folder is not empty (which I always do), add the '-r' option after 'rmdir', but make sure you don't want the files because there is not recycle bin in linux.

