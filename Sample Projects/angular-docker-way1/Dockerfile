FROM nginx:1.17.1-alpine
# copy the nginx config file to the container
COPY nginx.conf /etc/nginx/nginx.conf
# copy project files(from the dist) in the container
COPY  /dist/angular-docker-demo /usr/share/nginx/html

