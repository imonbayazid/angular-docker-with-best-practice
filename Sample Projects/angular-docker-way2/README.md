#Instructions

1.Install node,angular cli in your local PC

2. Get angular project 
ng new YOUR_APP_NAME
cd YOUR_APP_NAME
example: 
ng new angular-docker-demo
cd angular-docker-demo

3. Create a docker file with the following content 
FROM node:slim as build_stage
RUN mkdir /home/app && chown node:node /home/app
WORKDIR /home/app
USER node
COPY package.json package-lock.json* ./
RUN npm install --no-optional && npm cache clean --force
ENV PATH /home/app/node_modules/.bin:$PATH
COPY --chown=node:node . .
RUN npm run build

FROM nginx:1.17.1-alpine
COPY nginx.conf /etc/nginx/nginx.conf
COPY --from=build_stage /home/app/dist/angular-docker-demo /usr/share/nginx/html

4. create a nginx.conf file with 
events{}

http {

    include /etc/nginx/mime.types;

    server {
        listen 80; // you can define your own port here
        server_name localhost;
        root /usr/share/nginx/html;
        index index.html;

        location / {
            try_files $uri $uri/ /index.html;
        }
    }
}

5. Create a .dockerignore file with following content
dist
node_modules

6. We can simply build by  
docker build -t your_image_name .
or we can tag the image while building as it is necessary to tag the image when u want to push it in the dockerhub
so we will use 
docker build -t your_dockerhub_username/your_image_name :version_name .

7. check docker images in the docker 
docker images

8. Run the docker image
docker run --name your_container_name-p 80:80 -d your_dockerhub_username/your_image_name :version_name

9. check the running container and it's port 
docker ps -a

10. check the localhost:port

11. Now push the docker image that we just built to the dockerhub.
 So, first login to your dockerhub account.
docker login -u your_dockerhub_username 
then just push the image 
docker push your_dockerhub_username/your_image_name :version_name

12. now to check wether your image works or not,pull the image from the dockerhub and run it.
First remove your the image that you run previously also remove the container.
docker rm -f YOUR_DOCKER_CONTAINER_ID
docker rmi -f YOUR_DOCKER_IMAGE_ID 

Now pull the image from the dockerhub
docker pull your_dockerhub_username/your_image_name :version_name

Check the image 
docker images

Lastly, run the image like before.
docker run --name your_container_name-p 9090:80 -d your_dockerhub_username/your_image_name :version_name

check the running container and it's port 
docker ps -a

13.  check the localhost:9090 
