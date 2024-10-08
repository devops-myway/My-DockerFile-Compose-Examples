##### Build a Django Application
First, let's install Django and Create a Django application.
``````sh
$ sudo pip install django==1.9
$ django-admin startproject djangoapp

# Requirements File
$ cd djangoapp
$ vi requirements.txt

# add the following dependencies to requirements.txt
Django==1.9
gunicorn==19.6.0
``````
##### Create Dockerfile

``````sh

FROM python:2.7
MAINTAINER Esther
ADD . /usr/src/app
WORKDIR /usr/src/app
COPY requirements.txt ./
RUN pip install --no-cache-dir -r requirements.txt
EXPOSE 8000  
CMD exec gunicorn djangoapp.wsgi:application --bind 0.0.0.0:8000 --workers 3 

``````
##### Build the Docker Image

``````sh
sudo docker build -t django_application_image .

``````
##### Run the App
You should see a message that gunicorn is serving your app at http://0.0.0.0:8000. Navigate to your servers IP (ip_address:8000) and you should see the Django welcome page.
``````sh
sudo docker run -p 8000:8000 -i -t django_application_image

``````
##### To see running containers:

``````sh
sudo docker ps -a
``````
