FROM node:8

RUN mkdir -p /usr/src/app

WORKDIR /usr/src/app

COPY sv-app/package.json /usr/src/app

RUN npm install

COPY sv-app /usr/src/app

EXPOSE 4200 49153

