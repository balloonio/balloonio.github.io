---
title: Host Django website on PythonAnywhere
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

# Web app creation

Go to the **Web** tab on PythonAnywhere, and select **Add a new web app**. Now a popup will come up to ask you to upgrade your plan; otherwise, a free tier web app can only be host at your-username.pythonanywhere.com instead of a customized domain.

Next, it asks you to **Select a Python Web framework**. Please select **>>Manual configuration (including virtualenvs)** instead of the **Django** option. This one gives you more options and flexibility. Lastly, you need to select a Python version to work with.

# Web app configuration

As soon as you finish all those popup prompts above, you come to this page which says **Configuration for your-username.pythonanywhere.com** Here we need to make some configuration changes to make the site linked with the code.

## Default page

Before we actually make any change to the configurations, if we go to the url your-username.pythonanywhere.com, we can already see a default page showing up like the following:

```
Hello, World!
This is the default welcome page for a PythonAnywhere hosted web application.

Find out more about how to configure your own web application by visiting the web app setup page
```

We will soon discuss where this default page is coming from.

## Code and virtualenv

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

## log files

PythonAnywhere provides three log files by default. They are very helpful in terms of debugging.

```
Access log: your-username.pythonanywhere.com.access.log
 Error log: your-username.pythonanywhere.com.error.log
Server log: your-username.pythonanywhere.com.server.log
```

# Initialize virtualenv and install Django

Go to the **Console** tab, and here you can start a new console. You will find yourself landed in a home directory with a README file:

```bash
06:00 ~ $ ls
README.txt
06:00 ~ $ pwd
/home/your-username
```

Since there is only one web app for free tier user, let's try to reuse this same cloud space for different apps. Therefore, let's create a subdirectory to work on each application:

```bash
06:02 ~ $ mkdir hello_world
06:03 ~ $ cd hello_world/
```

Create a virtual environment:

```bash
06:04 ~/hello_world $ python3 -m venv myvenv
06:04 ~/hello_world $ ls
myvenv
06:05 ~/hello_world $ source myvenv/bin/activate
(myvenv) 06:05 ~/hello_world $  
```

Install Django inside this virtual environment. This process may take a little while. After a successful installation, you may see something like this: (Verify the Django version with `django-admin --version`.)

```bash
(myvenv) 06:05 ~/hello_world $ pip3 install django
Looking in links: /usr/share/pip-wheels
Collecting django
  Using cached https://files.pythonhosted.org/packages/fd/9a/0c028ea0fe4f5803dda1a7afabeed958d0c8b79b0fe762ffbf728db3b90d/Django-2.1.4-py3-none-any.whl
Collecting pytz (from django)
  Using cached https://files.pythonhosted.org/packages/f8/0e/2365ddc010afb3d79147f1dd544e5ee24bf4ece58ab99b16fbb465ce6dc0/pytz-2018.7-py2.py3-none-any.whl
Installing collected packages: pytz, django
Successfully installed django-2.1.4 pytz-2018.7 
(myvenv) 06:14 ~/hello_world $ django-admin --version
2.1.4
```

# Initialize Django site

Now that we have Django successfully installed, let's initialize the project:

```bash
(myvenv) 06:15 ~/hello_world $ django-admin startproject mysite .
(myvenv) 06:17 ~/hello_world $ ls
manage.py  mysite  myvenv
```

Now, the project directory structure should look like this:

```
hello_world
├───(myvenv)
├───manage.py
└───mysite
        settings.py
        urls.py
        wsgi.py
        __init__.py
```

# Revisit WSGI configuration file

Before we start any work, let's go to your-username.pythonanywhere.com and see how our barebone website looks like. Surprisingly, it still shows the default page that we saw before. Now as an experienced software engineer, your guts tells you something is not right - the default page doesn't seem to come from our project source at all. 

You are totally correct. The default page comes from the WSGI file, not the WSGI file that Django generates under `mysite/` but the one specifically provided by PythonAnywhere in the **WSGI configuration file** field. Here is the part that renders the default page:

```python
# +++++++++++ HELLO WORLD +++++++++++
# A little pure-wsgi hello world we've cooked up, just
# to prove everything works.  You should delete this
# code to get your own working.


HELLO_WORLD = """<html>
<head>
    <title>Python Anywhere hosted web application</title>
</head>
<body>
<h1>Hello, World!</h1>
<p>
    This is the default welcome page for a
    <a href="https://www.pythonanywhere.com/">PythonAnywhere</a>
    hosted web application.
</p>
<p>
    Find out more about how to configure your own web application
    by visiting the <a href="https://www.pythonanywhere.com/web_app_setup/">web app setup</a> page
</p>
</body>
</html>"""

def application(environ, start_response):
    if environ.get('PATH_INFO') == '/':
        status = '200 OK'
        content = HELLO_WORLD
    else:
        status = '404 NOT FOUND'
        content = 'Page not found.'
    response_headers = [('Content-Type', 'text/html'), ('Content-Length', str(len(content)))]
    start_response(status, response_headers)
    yield content.encode('utf8')
```