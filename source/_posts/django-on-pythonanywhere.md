---
title: Host a Django website on PythonAnywhere for free
date: 2018-12-05 21:36:04
tags:
  - techstack
  - front-end
  - web
  - django
categories:
  - - TechStacks
---

# Account creation

Go to www.pythonanywhere.com and create an account. The website supports both free tier and paid plans. The free tier plan gives you one web app at your-username.pythonanywhere.com. The web app will be disabled if there is no activity for 3 months. (As long as you are clicking the button to indicate an active usage every 3 month, the web app will keep running.)

# Web App creation

Go to the **Web** tab on PythonAnywhere, and select **Add a new web app**. Now a popup will come up to ask you to upgrade your plan; otherwise, a free tier web app can only be host at your-username.pythonanywhere.com instead of a customized domain.

Next, it asks you to **Select a Python Web framework**. Please select **>>Manual configuration (including virtualenvs)** instead of the **Django** option. This one gives you more options and flexibility. Lastly, you need to select a Python version to work with.

# Web App Configuration

As soon as you finish all those popup prompts above, you come to this page which says **Configuration for your-username.pythonanywhere.com** Here we need to make some configuration changes to make the site linked with the code.

## Default Page

Before we actually make any change to the configurations, if we go to the url your-username.pythonanywhere.com, we can already see a default page showing up like the following:

```
Hello, World!
This is the default welcome page for a PythonAnywhere hosted web application.

Find out more about how to configure your own web application by visiting the web app setup page
```

We will soon discuss where this default page is coming from.

## Code and Virtualenv

Under **Code** section, there is a **Source code** field which says *Enter the path to your web app source code*. Here you want to put the path to your source code. For example

```
Source code: /home/your-username/hello_world/
```

Similarly, under **Virtualenv** section, you want to specify the path to the virtualenv that you are using:

```
/home/your-username/hello_world/venv
```

## WSGI configuration file

Under **Code** section, there is another very important field **WSGI configuration file**. This WSGI file is pretty much the same as the WSGI file that Django provides when a new project is created. The only thing that need to be pay attention to is that this file is at a very different location from the rest of the sources. You will have to soon modify this file to use the WSGI file that Django generates.

```
WSGI configuration file: /var/www/your-username_pythonanywhere_com_wsgi.py
```

## Log files

PythonAnywhere provides three log files by default. They are very helpful in terms of debugging.

```
Access log: your-username.pythonanywhere.com.access.log
 Error log: your-username.pythonanywhere.com.error.log
Server log: your-username.pythonanywhere.com.server.log
```
