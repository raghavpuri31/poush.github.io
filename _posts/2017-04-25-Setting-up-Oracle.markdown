---
layout: post
title:  "Setting up Oracle 11g in Windows"
date:   2017-04-25 13:26:44 +0530
categories: general windows
layout: post
date: 2016-02-24 22:44
tag:
- oracle
- database
- express
- windows
star: true
author: poush
---

A short simple guide to set up oracle database 11g on your windows system.

## 1. Downloading Oracle 11g express
Go to this [page](http://www.oracle.com/technetwork/database/database-technologies/express-edition/downloads/index.html) to get download links 


Choose *x64* if you have 64bit system or *x32* for 32bit system. If you are not sure just select *x32* one. 

You will be required to signup and login to download this
 ![Signin](https://www.dropbox.com/s/lo2fjnqbbd9y5so/Capture-2.PNG?dl=1)

and on completing the signup and login, your download will start.
![download_Start](https://www.dropbox.com/s/0tcuqeazmueeqyi/Capture-1.PNG?dl=1)


## 2. Installing Oracle 11g
An obvious step is to extract the downloaded zip
![Extracting files](https://www.dropbox.com/s/d64md9pi22xnkeg/Capture-4.PNG?dl=1)

What's next start with double-clicking setup.exe :D

You will be asked for a password. You can put any password you want here but for now I'm putting "root" as password

![Entering Password](https://www.dropbox.com/s/105wqecbzr6xcqs/Capture-6.PNG?dl=1)

Now complete setup process and that's all in installation.

## 3. Using Oracle 11g

Go to your start menu and search for "SQL" you will see something "Run SQL Command Line". Open it.

![Oracle 11g CLI](https://www.dropbox.com/s/9kolfs8fdcej2d0/Capture-7.PNG?dl=1)

### Now how to connect ? Simple!
You can directly use "Sysdba" user or if you want you can use it to create more users.

Here's how you can login using sysdba user

![Login](https://www.dropbox.com/s/ma6w6h9h6hjc1l8/Capture-8.PNG?dl=1)

Now after this you can directly use SQL commands or if you prefer, create new user to use.

![SQL Command Line Usage](https://www.dropbox.com/s/oj28p2wp6gw0r9r/Capture-9.PNG?dl=1)

*This post is inspired from the questions of my batchmates so major credits goes to them*
