# django-tutorial

I created this project as a log of following the [Django tutorial](https://docs.djangoproject.com/en/1.8/) at the Django project website. I worked with version 1.8. 

Contents:

* [deployment](deployment): This folder contains an [ansible](http://docs.ansible.com) playbook which deploys the tutorial application in an nginx-gunicorn-postgresql environment. It also auto-deploys the local virtualenv.
* `virtualenv`: For sanity, we use virtualenv. (.gitignored, it is automatically installed by ansible on ubuntu 15.04)
* `mysite`: This is the application which gets created during the tutorial (.gitignored)

## Production environment

The [deployment](deployment) directory contains the installation of the tutorial application into a server environment with the following components:

* webserver running nginx
    - listens on port 80
    - serves static content from local directory, `/webapps/mysite/static`
    - proxies everything else to appserver, port 8002
* dbserver running postgresql
    - version 9.3 hardcoded
    - listens on port 5432 to the local intranet (10.15.20.0/24)
    - database `mydatabase`, accessible to `mydatabaseuser:mypassword`
* appserver
    - runs supervisord to supervise gunicorn
    - runs gunicorn
    - contains django app within gunicorn

## Tutorial

### Development environment

Set up the local environment and verify django version:

    tutorial/deployment$ ansible-playbook -i hosts site.yml --tags local
    tutorial$ . virtualenv/bin/activate
    tutorial$ python -c "import django; print(django.get_version())"
    1.8

Create the project, migrate the database and run the server:

    tutorial$ django-admin startproject mysite
    tutorial$ cd mysite
    tutorial/mysite$ python manage.py migrate
    tutorial/mysite$ python manage.py runserver
    Performing system checks...

    System check identified no issues (0 silenced).
    May 18, 2015 - 11:17:04
    Django version 1.8, using settings 'mysite.settings'
    Starting development server at http://127.0.0.1:8000/
    Quit the server with CONTROL-C.

### Production environment

Deploy the project in the production environment (use `-e "dropdb=True"` to force-drop the database):

    tutorial/deployment$ ansible-playbook -i hosts site.yml