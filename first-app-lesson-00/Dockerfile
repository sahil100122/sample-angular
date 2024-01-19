FROM node:14-alpine as builder

COPY package.json ./

COPY .npmrc .npmrc 

#RUN npm install  

## Storing node modules on a separate layer will prevent unnecessary npm installs at each build

RUN npm ci && mkdir /kcommunications-ui-client && mv ./node_modules ./kcommunications-ui-client

RUN rm -f .npmrc

WORKDIR /kcommunications-ui-client

COPY . .

## Build the angular app in production mode and store the artifacts in dist folder
ARG ENVIRONMENT=prod
ARG GVM_HOSTNAME=gvm_host_domain_name
ARG STAGE=stable
RUN echo "env=$ENVIRONMENT hostname=$GVM_HOSTNAME stage=$STAGE"
RUN npm run build:$ENVIRONMENT



FROM nginx:1.14.1-alpine

## Copy our default nginx config
COPY nginx/default.conf /etc/nginx/conf.d/

## Remove default nginx website
RUN rm -rf /usr/share/nginx/html/*

RUN apk add --update curl && rm -rf /var/cache/apk/*

## From ‘builder’ stage copy over the artifacts in dist folder to default nginx public folder
COPY --from=builder /kcommunications-ui-client/dist /usr/share/nginx/html/kcommunications-ui-client/

RUN chown nginx:nginx -R /usr/share/nginx/html/

RUN chmod 744 -R /usr/share/nginx/html

CMD ["nginx", "-g", "daemon off;"]
