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

