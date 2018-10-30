# image
FROM python:3

ENV PYTHONUNBUFFERED 1

# update & install module
RUN apt-get update
RUN apt-get -y install mysql-server 
RUN apt-get -y install mysql-client

# install requirements and copy your code
RUN mkdir /code
WORKDIR /code
ADD requirements.txt /code/
RUN pip install -r requirements.txt
ADD . /code/
