# How to set up a Django project with PostgreSQL on Docker?

-   **Step 1**

Create a Django project with a name of your choice or you may already have a project. In the latter case, you don’t need to do step1.

-   **Step 2**

Create a new file called  `Dockerfile`  in your project directory i.e. where the manage.py file is.

Now you may be wondering what the heck is Dockerfile?  
Let me help you with that in short: The Dockerfile defines an application’s image content via one or more build commands that configure that image.

-   **Step 3**

Add the following content to the  `Dockerfile`.
```
FROM  python:3

ENV  PYTHONUNBUFFERED=1

WORKDIR  /code

COPY  requirements.txt  .

RUN  pip  install  -r  requirements.txt

COPY  .  .
```

-   **Step 4**

Now Save and close the  `Dockerfile`.

-   **Step 5**

Create a file called  `docker-compose.yml`  in your project directory.

-   **Step 6**

Paste the content below in the  `docker-compose.yml`

```     
services:  
  db:  
    image: postgres   
    environment:  
      - POSTGRES_DB=postgres  
      - POSTGRES_USER=postgres  
      - POSTGRES_PASSWORD=postgres  
  web:  
    build: .  
    command: python manage.py runserver 0.0.0.0:8000  
    volumes:  
      - .:/code  
    ports:  
      - "8000:8000"  
    environment:  
      - POSTGRES_NAME=postgres  
      - POSTGRES_USER=postgres  
      - POSTGRES_PASSWORD=postgres  
    depends_on:  
      - db
```

There are two services mentioned in  `docker-compose.yml`  one for the database and one for your django project.

The credentials mentioned in the environment will be used by Django in the  `settings.py`  file. which leads to the next step after saving.

-   **Step 7**

Now for accessing the database from Django you need to set the database in  `settings.py`  file

```
import os

DATABASES = {
	'default': {
		'ENGINE': 'django.db.backends.postgresql',
		'NAME': os.environ.get('POSTGRES_NAME'),
		'USER': os.environ.get('POSTGRES_USER'),
		'PASSWORD': os.environ.get('POSTGRES_PASSWORD'),
		'HOST': 'db',
		'PORT': 5432, #default port you don't need to mention in docker-compose
	}
}
```
-   **Step 8**

Now you just need to have a working docker on your system and run the following command in your terminal.

#For starting the container  
`docker-compose up` #For stopping the container  
`docker-compose down`

And now you can access the application via the port you mentioned in  `docker-compose.yml`

Generally for Django projects  [http://localhost:8000/](http://localhost:8000/)

Important Notes:

1.  Make sure you have the latest requirements.txt file.
2.  Don't forget to change the  `settings.py`  file
3.  Make sure Docker is running.
