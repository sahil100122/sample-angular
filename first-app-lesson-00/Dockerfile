FROM node:18-alpine as builder

COPY package.json ./

#COPY .npmrc .npmrc 
# RUN python --version 
RUN apk add --update python3 make g++\
   && rm -rf /var/cache/apk/*
RUN npm build

## Storing node modules on a separate layer will prevent unnecessary npm installs at each build

RUN npm i && mkdir /kcommunications-ui-client && mv ./node_modules ./kcommunications-ui-client

RUN rm -f .npmrc

WORKDIR /kcommunications-ui-client

COPY . .

## Build the angular app in production mode and store the artifacts in dist folder
ARG ENVIRONMENT=prod
ARG GVM_HOSTNAME=gvm_host_domain_name
ARG STAGE=stable
RUN echo "env=$ENVIRONMENT hostname=$GVM_HOSTNAME stage=$STAGE"
RUN npm run build:$ENVIRONMENT
