---
toc: true
layout: post
description: A Django based REST API for managing songs and playlists, along with a graphical management interface
categories: [markdown]
title: A REST API for managing playlists
tags: [python, django, rest-api, bootstrap, javascript, html, css]
---

So the last 24 hours were spent on a code-sprint and this is the end product.
I built a Django REST API for retrieving and updating data to a PostgreSQL database of the backend of a music app that wishes to manage playlists and songs.  
You can find the codebase on my [github](https://github.com/kprohith)

> <https://github.com/kprohith/django-playlist-manager>

## **Playlist Manager**

A Django based REST API for managing songs and playlists, along with a graphical management interface

Built using:

- [Python3.8](https://www.python.org/)
- [Django Framework](https://www.djangoproject.com/)
- [Django REST Framework](https://www.django-rest-framework.org/)
- [Bootstrap](https://getbootstrap.com/)
- [SQLite](https://www.sqlite.org/index.html)(for development)
- [PostgreSQL](https://www.postgresql.org/)(for production)

## Setup Instructions

1. Create virtual environment using [vne/virtualenv/etc](https://docs.python.org/3/tutorial/venv.html) using Python3.8
2. Activate virtual environment in terminal
3. Install dependencies by running 'pip install -r requirements.txt'
4. Run 'python manage.py makemigrations'
5. Run 'python manage.py migrate'
6. Run 'python manage.py runserver'. The app should now be running locally at 127.0.0.1:8000 or localhost:8000

## Using the app

1. Go to the Admin Page (<http://127.0.0.1:8000/admin/>) and login as admin with the details below to to create new users/groups/manage database of app.
2. The admin username is **admin** and password is **testing321**
3. You can either use the Management Interface(after logging in as admin) or teh REST API(after creating a account and logging in) in order to view and update the songs/artists/albums/playlists information.
4. The Management Interface at <http://127.0.0.1:8000/admin/backend/> can be used to graphically add new users or update the music database of the app. You can use it to add new songs/artists/albums/playlists.
5. The REST API at <http://127.0.0.1:8000/api/api-home/> can be used to access the app and query its data using GET/POST requests and retrieving/updating data in API/JSON format.

## Links

1. Home Page: <http://127.0.0.1:8000/>
2. Admin Page: <http://127.0.0.1:8000/admin/>
3. Register: <http://127.0.0.1:8000/register/>
4. Login: <http://127.0.0.1:8000/login/>
5. Access Management Interface: <http://127.0.0.1:8000/admin/backend/>
6. Add/retrieve songs using Management Interface: <http://127.0.0.1:8000/admin/backend/song/>
7. Add/retrieve artists using Management Interface: <http://127.0.0.1:8000/admin/backend/artist/>
8. Add/retrieve albums using Management Interface: <http://127.0.0.1:8000/admin/backend/album/>
9. Add/retrieve playlists using Management Interface: <http://127.0.0.1:8000/admin/backend/playlist/>
10. Accessing REST API: <http://127.0.0.1:8000/api/api-home/>
11. Add/retrieve songs using REST API: <http://127.0.0.1:8000/api/songs/songs/>
12. Add/retrieve artists using REST API: <http://127.0.0.1:8000/api/artists/artists/>
13. Add/retrieve albums using REST API: <http://127.0.0.1:8000/api/albums/albums/>
14. Add/retrieve playlists using REST API: <http://127.0.0.1:8000:api/playlists/playlists/>

Notes:

- You can send GET requests with or without creating an account and logging in.
Although, you **have** to create an account and be logged in in order to send POST requests.

- I have included some test data for demonstration's sake. I hope you appreciate my fine taste for music.

- I have exported all of the SQL code from all the major migrations that I have made and stored it in /SQL directory in order to maintain database integrity.

- Set DEBUG = True in setting.py of the backend app to view detailed logs and errors messages

If you find any bugs or if you think you can improve this implementation in any manner, please fork and create a pull request!  

This is still a work in progress. I plan on dedicating more time for this in the future.

