# docker_django_angular_mysql

### backend: django
### frontend: angular
### db: mysql

### Directory structure

```
─docker_django_angular_mysql
  ├─backend
  │  └─api
  │      └─__pycache__
  └─frontend
      └─sv-app
          ├─e2e
          │  └─src
          └─src
              ├─app
              ├─assets
              └─environments
```

### Backend
#### Dockerfile
```
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
```

#### requirements.txt
```
Django>=2.0
mysqlclient>=1.3
```

### django settings.py 
```
DATABASES = {
    'default': {
	'ENGINE': 'django.db.backends.mysql',
	'NAME': 'svdb',
	'USER': 'svdbadmin',
	'PASSWORD': 'qwe123',
	'HOST': 'db',
	'PORT': '3306',
    }
}
```

### Frontend
#### Dockerfile
```
# image
FROM node:8

# install dependency
RUN mkdir -p /usr/src/app
WORKDIR /usr/src/app
COPY sv-app/package.json /usr/src/app
RUN npm install

# copy your code
COPY sv-app /usr/src/app

# open ports
EXPOSE 4200 49153
```

#### package.json
```{
  "name": "sv-app",
  "version": "0.0.0",
  "scripts": {
    "ng": "ng",
    "start": "ng serve --host 0.0.0.0",
    "build": "ng build",
    "test": "ng test",
    "lint": "ng lint",
    "e2e": "ng e2e"
  },
  "private": true,
  "dependencies": {
    "@angular/animations": "~7.0.0",
    "@angular/common": "~7.0.0",
    "@angular/compiler": "~7.0.0",
    "@angular/core": "~7.0.0",
    "@angular/forms": "~7.0.0",
    "@angular/http": "~7.0.0",
    "@angular/platform-browser": "~7.0.0",
    "@angular/platform-browser-dynamic": "~7.0.0",
    "@angular/router": "~7.0.0",
    "core-js": "^2.5.4",
    "rxjs": "~6.3.3",
    "zone.js": "~0.8.26"
  },
  "devDependencies": {
    "@angular-devkit/build-angular": "~0.10.0",
    "@angular/cli": "~7.0.3",
    "@angular/compiler-cli": "~7.0.0",
    "@angular/language-service": "~7.0.0",
    "@types/node": "~8.9.4",
    "@types/jasmine": "~2.8.8",
    "@types/jasminewd2": "~2.0.3",
    "codelyzer": "~4.5.0",
    "jasmine-core": "~2.99.1",
    "jasmine-spec-reporter": "~4.2.1",
    "karma": "~3.0.0",
    "karma-chrome-launcher": "~2.2.0",
    "karma-coverage-istanbul-reporter": "~2.0.1",
    "karma-jasmine": "~1.1.2",
    "karma-jasmine-html-reporter": "^0.2.2",
    "protractor": "~5.4.0",
    "ts-node": "~7.0.0",
    "tslint": "~5.11.0",
    "typescript": "~3.1.1"
  }
}
```
*note: "start": "ng serve --host 0.0.0.0"*

### docker-compose.yml
```
version: '3'

services:
  db:
    image: mysql
    command: --default-authentication-plugin=mysql_native_password
    restart: always
    ports:
      - "3306:3306"
    environment:
      MYSQL_ROOT_PASSWORD: qwe123
      MYSQL_DATABASE: svdb
      MYSQL_USER: svdbadmin
      MYSQL_PASSWORD: qwe123

  web:
    build: backend 
    command: bash -c "sleep 3 && python3 backend/manage.py runserver 0.0.0.0:8000"
    links:
      - db
    volumes:
      - .:/code
    ports:
      - "8000:8000"
    depends_on:
      - db
      
  angular:
    build: frontend
    command: "npm start"
    ports:
      - "4200:4200"
      - "49153:49153"
    expose:
      - "4200"
      - "49153"
    depends_on:
     - web
```
