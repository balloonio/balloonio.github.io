---
title: Host Django website on PythonAnywhere
date: 2018-12-05 21:36:04
tags:
  - front end
  - web
  - django
---

# Create account

Go to www.pythonanywhere.com and create an account. The website supports both free tier and paid plans. The free tier plan gives you one web app at your-username.pythonanywhere.com. (For me and for the context of this tutorial, it will be bolunzhang.pythonanywhere.com.) The web app will be disabled if there is no activity for 3 months. (As long as you are clicking the button to indicate an active usage every 3 month, the web app will keep running.)

# Create PythonAnywhere web app

Go to the **Web** tab on PythonAnywhere, and select **Add a new web app**. Now a popup will come up to ask you to upgrade your plan; otherwise, a free tier web app can only be host at your-username.pythonanywhere.com instead of a customized domain.

Next, it asks you to **Select a Python Web framework**. Please select **>>Manual configuration (including virtualenvs)** instead of the **Django** option. This one gives you more options and flexibility. Lastly, you need to select a Python version to work with.

# Configure web app

As soon as you finish all those popup prompts above, you come to this page which says **Configuration for your-username.pythonanywhere.com**. (For me it is **Configuration for bolunzhang.pythonanywhere.com**) Here we need to make some configuration changes to make the site linked with the code.

## Default page

Before we actually make any change to the configurations, if we go to the url your-username.pythonanywhere.com, we can already see a default page showing up like the following:

{% img [class names] /assets/pythonanywhere-default.JPG [530] [211] [PythonAnywhere welcome page] %}

We will soon discuss where this default page is coming from.

## Code and virtualenv (optional)

Under **Code** section, there is a **Source code** field which says *Enter the path to your web app source code*. Here you want to put the path to your source code. For example

```
Source code: /home/bolunzhang/hello_world/
```

Similarly, under **Virtualenv** section, you want to specify the path to the virtualenv that you are using:

```
/home/bolunzhang/hello_world/venv
```

This part is optional, because based on my testing the entire website works fine even if these two fields are left untouched. I'm not entirely sure about the effect of these two fields at this point.

## WSGI configuration file

Under **Code** section, there is another very important field **WSGI configuration file**. This WSGI file is pretty much the same as the WSGI file that Django provides when a new project is created. The only thing that need to be pay attention to is that this file is at a very different location from the rest of the sources. You will have to soon modify this file to use the WSGI file that Django generates.

```
WSGI configuration file: /var/www/bolunzhang_pythonanywhere_com_wsgi.py
```

## log files

PythonAnywhere provides three log files by default. They are very helpful in terms of debugging.

```
Access log: bolunzhang.pythonanywhere.com.access.log
 Error log: bolunzhang.pythonanywhere.com.error.log
Server log: bolunzhang.pythonanywhere.com.server.log
```

# Initialize virtualenv and install Django

Go to the **Console** tab, and here you can start a new console. You will find yourself landed in a home directory with a README file:

```bash
06:00 ~ $ ls
README.txt
06:00 ~ $ pwd
/home/bolunzhang
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
      ├───settings.py
      ├───urls.py
      ├───wsgi.py
      └───__init__.py
```

# Revisit WSGI configuration file

## Understand the template

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

More than just showing a default page, the WSGI file is the gateway for your-username.pythonanywhere.com. It is the first thing that a request reaches.

{% blockquote Wikipedia https://en.wikipedia.org/wiki/Web_Server_Gateway_Interface Web Server Gateway Interface (WSGI) %}
The Web Server Gateway Interface (WSGI) is a simple calling convention for web servers to forward requests to web applications or frameworks written in the Python programming language.
{% endblockquote %}

&nbsp;
An easy way to think about WSGI is that it acts like a dispatcher that dispatches web requests to the specific web apps. Since it can be a dispatcher to multiple web apps, it is agnostic to the web framework being used by each web app. As a matter of fact, there is also a Flask part in ths WSGI file provided, which is initially be commented out also:

```python
# +++++++++++ FLASK +++++++++++
# Flask works like any other WSGI-compatible framework, we just need
# to import the application.  Often Flask apps are called "app" so we
# may need to rename it during the import:
#
#
#import sys
#
## The "/home/bolunzhang" below specifies your home
## directory -- the rest should be the directory you uploaded your Flask
## code to underneath the home directory.  So if you just ran
## "git clone git@github.com/myusername/myproject.git"
## ...or uploaded files to the directory "myproject", then you should
## specify "/home/bolunzhang/myproject"
#path = '/home/bolunzhang/path/to/flask_app_directory'
#if path not in sys.path:
#    sys.path.append(path)
#
#from main_flask_app_file import app as application  # noqa
#
# NB -- many Flask guides suggest you use a file called run.py; that's
# not necessary on PythonAnywhere.  And you should make sure your code
# does *not* invoke the flask development server with app.run(), as it
# will prevent your wsgi file from working.
```

## Modify the template

Now that we have some knowledge on WSGI, let modify it so that your-username.pythonanywhere.com shows our Django website instead of the default page.

First of all, remove all the unncessary templates. This includes the `HELLO WORLD` page part and the `FLASK` part. Any other part that is not related to Django can be removed also.

Now, the template should look similar to this:

```python
# +++++++++++ CUSTOM WSGI +++++++++++
# If you have a WSGI file that you want to serve using PythonAnywhere, perhaps
# in your home directory under version control, then use something like this:
#
#import sys
#path = '/home/bolunzhang/path/to/my/app
#if path not in sys.path:
#    sys.path.append(path)
#from my_wsgi_file import application  # noqa

# +++++++++++ DJANGO +++++++++++
# To use your own django app use code like this:
#import os
#import sys
#
## assuming your django settings file is at '/home/bolunzhang/mysite/mysite/settings.py'
## and your manage.py is is at '/home/bolunzhang/mysite/manage.py'
#path = '/home/bolunzhang/mysite'
#if path not in sys.path:
#    sys.path.append(path)
#
#os.environ['DJANGO_SETTINGS_MODULE'] = 'mysite.settings'
#
## then:
#from django.core.wsgi import get_wsgi_application
#application = get_wsgi_application()
```

Let's uncomment most of the code here, keeping in mind that `path` and `my_wsgi_file` be replaced with the real path and the real wsgi filename. Here is how it looks after modification:

```python
import os, sys

# +++++++++++ CUSTOM WSGI +++++++++++
path_project_dir = '/home/bolunzhang/hello_world'  # for 'mysite.settings' below
path_project_mysite = path_project_dir + '/mysite' # for wsgi file

if path_project_dir not in sys.path:
    sys.path.append(path_project_dir)
if path_project_mysite not in sys.path:
    sys.path.append(path_project_mysite)

from wsgi import application # wsgi file that Django generated under hello_world/mysite/

# +++++++++++ DJANGO +++++++++++
# no longer needed - this part of code is imported from hello_world/mysite/wsgi.py
```

# Add allowed host

If we just go to the link **your-username.pythonanywhere.com** now, we will see the following error. It means we need to add the url to the Django site settings' allowed hosts. This is due to some security reasons.

```
DisallowedHost at /
Invalid HTTP_HOST header: 'bolunzhang.pythonanywhere.com'. You may need to add 'bolunzhang.pythonanywhere.com' to ALLOWED_HOSTS.
```

Go to `/home/bolunzhang/hello_world/mysite/settings.py` and change the line `ALLOWED_HOSTS = ['']` to `ALLOWED_HOSTS = ['your-username.pythonanywhere.com']` (bolunzhang.pythonwhere.com for me)

Now go back to **Configuration** page, and click on the **Reload your-username.pythonanywhere.com** button. Go to `your-username.pythonanywhere.com` after reloading. Now you should see the Django welcome page:

{% img [class names] /assets/django-default.png [1366] [768] [Django welcome page] %}

At this point, our barebone Django project is already up and running on a real web server provided by PythonAnywhere.

# Hello world

Now that we already have the Django project (website) ready, let's create a Django web app within this project to display a hello world message. Keep in mind, a Django project is a website, whereas a Django web app is a specific module within the entire website. One website can have, and very commonly will have multiple web apps. *(This is different from the 'PythonAnywhere web app' term. When we say we are creating a 'PythonAnywhere web app', it simply refers to a website.)

## Create helloworld Django web app

It is amazing that we finally come to this last step - create a Django web app within the project. 

```bash
(myvenv) 02:10 ~/hello_world $ python3 manage.py startapp helloworld
(myvenv) 02:11 ~/hello_world $ ls
db.sqlite3  helloworld  manage.py  mysite  myvenv
```

Now, the project directory structure should look like this:

```
hello_world
├───(myvenv)
├───db.sqlite3
├───manage.py
├───mysite
│   ├───__init__.py
│   ├───settings.py
│   ├───urls.py
│   ├───wsgi.py
└───helloworld
    ├───__init__.py
    ├───admin.py
    ├───apps.py
    ├───migrations
    │   └───__init__.py
    ├───models.py
    ├───tests.py
    └───views.py
```

## Add app to site settings

Tell Django that this `helloworld` app is being used by this website by specifying in `mysite/settings.py`:

```python
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'helloworld' # add this line so the Django knows 'helloworld' web app is being used by the website
]
```

## Create URL routing

Let's examine the `hello_world/mysite/urls.py` file first:

```python
"""mysite URL Configuration

The `urlpatterns` list routes URLs to views. For more information please see:
    https://docs.djangoproject.com/en/2.1/topics/http/urls/
Examples:
Function views
    1. Add an import:  from my_app import views
    2. Add a URL to urlpatterns:  path('', views.home, name='home')
Class-based views
    1. Add an import:  from other_app.views import Home
    2. Add a URL to urlpatterns:  path('', Home.as_view(), name='home')
Including another URLconf
    1. Import the include() function: from django.urls import include, path
    2. Add a URL to urlpatterns:  path('blog/', include('blog.urls'))
"""
from django.contrib import admin
from django.urls import path

urlpatterns = [
    path('admin/', admin.site.urls),
]
```

This file basically defines the url routing for our entire website. For example, there is an `admin/` entry. That means any url path that starts with `admin/` will be mapped to `admin.site.urls`, which is another URLconf file, to take care of. Of course, there are more documentations about how urls are directed.

{% blockquote Django https://docs.djangoproject.com/en/2.1/topics/http/urls/ URL dispatcher %}
To design URLs for an app, you create a Python module informally called a URLconf (URL configuration). This module is pure Python code and is a mapping between URL path expressions to Python functions (your views).
{% endblockquote %}

&nbsp;
Let's map the url `your-username.pythonanywhere.com/helloworld` to a view called `hwindex` (hellow world index page). You might be wondering what is and where is this `hwindex`. Don't worry - this is essentially a Python function that we define and we are going to implement it soon.

```python
from django.contrib import admin
from django.urls import path
from helloworld import views # added line to use hwindex

urlpatterns = [
    path('admin/', admin.site.urls),
    path('helloworld/', views.hwindex, name='hwindex') # added line to route to hwindex
]
```

## Create hwindex view

{% blockquote Django https://docs.djangoproject.com/en/2.1/topics/http/urls/ Writing views %}
A view function, or view for short, is simply a Python function that takes a Web request and returns a Web response. This response can be the HTML contents of a Web page, or a redirect, or a 404 error, or an XML document, or an image . . . or anything, really. The view itself contains whatever arbitrary logic is necessary to return that response. This code can live anywhere you want, as long as it’s on your Python path. There’s no other requirement–no “magic,” so to speak. For the sake of putting the code somewhere, the convention is to put views in a file called views.py, placed in your project or application directory.
{% endblockquote %}

&nbsp;
The official Django documentation already makes it very clearly. A view is basically a Python function that handles http request. Let's make change to `helloworld/views.py`:

```python
from django.shortcuts import render
from django.http import HttpResponse

# Create your views here.
def hwindex(request):
    return HttpResponse("Hello world from Django on PythonAnywhere!")
```

Save the file, click the reload button, and go to your-username.pythonanywhere.com/helloworld. It shows on the page the response generated by `hwindex` view!

```
Hello world from Django on PythonAnywhere!
```