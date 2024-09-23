# Django Application

## Setup

Setup project :

    django-admin startproject qna_poll

Run server:

    python manage.py runserver

## App

An app is a web application that has a specific meaning in your project, like a home page, a contact form, or a members database.

create app

    python manage.py startapp members

Notify Django of new application

File: [qna_poll/settings.py](qna_poll/settings.py)
```py
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'members'
]
```

Do the migration

    python manage.py migrate

## File Components

Folder structure


```shell
.
├── db.sqlite3
├── manage.py
├── members
│   ├── __init__.py
│   ├── admin.py
│   ├── apps.py
│   ├── migrations
│   │   └── __init__.py
│   ├── models.py
│   ├── tests.py
│   └── views.py
├── qna_poll
│   ├── __init__.py
│   ├── __pycache__
│   │   ├── __init__.cpython-311.pyc
│   ├── asgi.py
│   ├── settings.py
│   ├── urls.py
│   └── wsgi.py
└── readme.md
```


### Views 
views are Python functions that takes http requests and returns http response, like HTML documents.

Folder: [members/views.py](members/views.py)
```py
from django.shortcuts import render
from django.http import HttpResponse

# Create your views here.

def members(request):
    return HttpResponse("Hello world!")
```

### URLS

- Create a file named `urls.py` in the same folder as the `views.py` file.
- set the urlpattern for getting reponse.
    ```py
    from django.urls import path
    from . import views

    urlpatterns = [
        path('members/', views.members, name='members'),
    ]
    ```
- The `urls.py` file you just created is specific for the members application. 
- We have to do some routing in the root directory `qna_poll` as well.
- There is a file called `urls.py` on the `qna_poll` folder, open that file and add the `include` module in the import statement, and also add a `path()` function in the `urlpatterns[]` list, with arguments that will route users that comes in via `127.0.0.1:8000/`.

    ```py
    from django.contrib import admin
    from django.urls import include, path

    urlpatterns = [
        path('', include('members.urls')),
        path('admin/', admin.site.urls),
    ]

    ```
### Templates

- result should be in HTML, and it should be created in a template.
- Create a templates folder inside the members folder, and create a HTML file named myfirst.html.

**Note: Do register app with django in settings.py and run migration script.**

### Models

- data is created in objects, called Models, and is actually tables in a database.
- we have described a Model in the models.py file, we must run a command to actually create the table in the database.

        python manage.py makemigrations members
    Output:

    ```sh
    ╰─ python manage.py makemigrations members
    Migrations for 'members':
    members/migrations/0001_initial.py
        + Create model Member

    ```
- Django creates a file describing the changes and stores the file in the /migrations/ folder
- For Django to create and execute an SQL statement, based of migration folder have to run migrate query.

        python manage.py migrate
- to view SQL command

        python manage.py sqlmigrate members 0001
- to add fields in the model, added `phone` & `joined_date` to Member model.
    ```py
    from django.db import models

    class Member(models.Model):
    firstname = models.CharField(max_length=255)
    lastname = models.CharField(max_length=255)
    phone = models.IntegerField()
    joined_date = models.DateField()
    ```
-  we have to make a migration to tell Django that it has to update the database

        python manage.py makemigrations members
        python manage.py migrate

**Note:** if the field is non nullable then add the default value or make it nullable, as the data exists with no value in theses fields.


## Python Shell Django

To open a Python shell, type this command:

    python manage.py shell

### Check the data in the Member table

```sh
>>> from members.models import Member
>>> Member.objects.all()
<QuerySet []>
```

### Add Record in Member Table
Create member object then save the object to table.
```sh
>>> member = Member(firstname='Suyash', lastname='Srivastava')
>>> member.save()
>>> Member.objects.all()
<QuerySet [<Member: Member object (1)>]>
>>> Member.objects.all().values()
<QuerySet [{'id': 1, 'firstname': 'Suyash', 'lastname': 'Srivastava'}]>
```
Add multiple records
```sh
>>> m1 = Member(firstname='Srijan', lastname='Srivastava')
>>> m2 = Member(firstname='Rahul', lastname='K')
>>> m3 = Member(firstname='Rajat', lastname='Srivastava')
>>> member_list=[m1,m2,m3]
>>> for i in member_list:
...     i.save()
... 
>>> 
>>> Member.objects.all().values()
<QuerySet [{'id': 1, 'firstname': 'Suyash', 'lastname': 'Srivastava'}, {'id': 2, 'firstname': 'Srijan', 'lastname': 'Srivastava'}, {'id': 3, 'firstname': 'Rahul', 'lastname': 'K'}, {'id': 4, 'firstname': 'Rajat', 'lastname': 'Srivastava'}]>
```

### Update Record in Member Table

Fetch the record
```sh
>>> x = Member.objects.all()[0]
>>> x.firstname
'Suyash'
```
Update & save the changes
```sh
>>> x = Member.objects.all()[0]
>>> x.firstname
'Suyash'
>>> x.firstname="Hero"
>>> x.save()
>>> x = Member.objects.all().values()
>>> x
<QuerySet [{'id': 1, 'firstname': 'Hero', 'lastname': 'Srivastava'}, {'id': 2, 'firstname': 'Srijan', 'lastname': 'Srivastava'}, {'id': 3, 'firstname': 'Rahul', 'lastname': 'K'}, {'id': 4, 'firstname': 'Rajat', 'lastname': 'Srivastava'}]>
>>> 
```

### Delete Records

fetch & delete
```sh
>>> x = Member.objects.all()[0]
>>> x
<Member: Member object (1)>
>>> x.firstname
'Hero'
>>> x.delete()
(1, {'members.Member': 1})
>>> Member.objects.all().values()
<QuerySet [{'id': 2, 'firstname': 'Srijan', 'lastname': 'Srivastava'}, {'id': 3, 'firstname': 'Rahul', 'lastname': 'K'}, {'id': 4, 'firstname': 'Rajat', 'lastname': 'Srivastava'}]>
```

### Added column update

```sh
>>> from members.models import Member
>>> Member.objects.all()
<QuerySet []>
>>> member = Member(firstname='Suyash', lastname='Srivastava')
>>> member.save()
>>> Member.objects.all()
>>> from members.models import Member
>>> x= Member.objects.all()[0]
>>> x.phone= 90328932932
>>> x.joined_date = '2024-09-23'
>>> x.save()
>>> Member.objects.all().values()
<QuerySet [{'id': 2, 'firstname': 'Srijan', 'lastname': 'Srivastava', 'phone': 90328932932, 'joined_date': datetime.date(2024, 9, 23)}, {'id': 3, 'firstname': 'Rahul', 'lastname': 'K', 'phone': None, 'joined_date': None}, {'id': 4, 'firstname': 'Rajat', 'lastname': 'Srivastava', 'phone': None, 'joined_date': None}]>
```

## Display Data from Database

### Prep Template & View

Have to render the data on to the templates, have to fetch data from db and put to template through view.

    template
    |
    |
    view
    |
    |
    db



file : [members/templates/all_members.html](members/templates/all_members.html)
```html
<!DOCTYPE html>
<html>
<body>

<h1>Members</h1>
  
<ul>
  {% for x in mymembers %}
    <li>{{ x.firstname }} {{ x.lastname }}</li>
  {% endfor %}
</ul>

</body>
</html>
```

`{% %}` are Django Tags, telling Django to perform some programming logic inside these brackets.

### Add links to details





