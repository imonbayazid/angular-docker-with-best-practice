1.Install node,angular cli in your local PC

2. Get angular project 
ng new YOUR_APP_NAME
cd YOUR_APP_NAME
example: 
ng new angular-docker-demo
cd angular-docker-demo

3. Create a docker file with the following content 

FROM node:slim as build
# install dependencies first, in a different location for easier app bind mounting for local development
# due to default /home permissions we have to create the dir with root and change perms
RUN mkdir /home/app && chown node:node /home/app
WORKDIR /home/app
RUN npm install -g @angular/cli
# the official node image provides an unprivileged user as a security best practice
# but we have to manually enable it. We put it here so npm installs dependencies as the same
# user who runs the app. 
# https://github.com/nodejs/docker-node/blob/master/docs/BestPractices.md#non-root-user
USER node
COPY package.json package-lock.json* ./
RUN npm install --no-optional && npm cache clean --force
ENV PATH /home/app/node_modules/.bin:$PATH
# copy in our source code last, as it changes the most
COPY --chown=node:node . .
CMD ng serve --host 0.0.0.0




4. create a docker-compose file

version: '3'
services:
  angdockdemo:
    build: 
      # . means the relative path; in this case the root directory where the docker-compose file exists
      context: .       
      # the dockerfile that u want to use to create the image     
      dockerfile: Dockerfile
    # user defined container name
    container_name: ang_container 
    ports:
      - 4242:4200 
    volumes:
      # map host(PC) current directory files to a docker container directory so that if we change something in our host(PC) system it will refect our docker images
      - .:/home/app  
      # a named volume for node_modules so that we dont need to create
      - /home/app/node_modules  
       





5. Create a .dockerignore file with following content
dist
node_modules
.git
.gitignore
.env

6. To check whether everything is working or not
Just run docker-compose up
and goto http://localhost:4242
Change something in the source file from the local (PC) and again goto http://localhost:4242
you will see the change in the browser which is shown from the docker container.


7. We can simply build by  
docker build -t your_image_name .
or we can tag the image while building as it is necessary to tag the image when u want to push it in the dockerhub
so we will use 
docker build -t your_dockerhub_username/your_image_name :version_name .
example: docker build -t imonbayazid/angular-docker-way3:v1 .

8. check docker images in the docker 
docker images

9. Run the docker image
docker run --name your_container_name-p 4200:4200 -d your_dockerhub_username/your_image_name :version_name
example: docker run --name angular-docker-way3-container -p 4200:4200 -d imonbayazid/angular-docker-way3:v1


10. check the running container and it's port 
docker ps -a

11. check the localhost:port

12. Now push the docker image that we just built to the dockerhub.
 So, first login to your dockerhub account.
docker login -u your_dockerhub_username 
then just push the image 
docker push your_dockerhub_username/your_image_name :version_name

13. now to check wether your image works or not,pull the image from the dockerhub and run it.
First remove your the image that you run previously also remove the container.
docker rm -f YOUR_DOCKER_CONTAINER_ID
docker rmi -f YOUR_DOCKER_IMAGE_ID 

Now pull the image from the dockerhub
docker pull your_dockerhub_username/your_image_name :version_name

Check the image 
docker images

Lastly, run the image like before.
docker run --name your_container_name-p 4200:4200 -d your_dockerhub_username/your_image_name :version_name

check the running container and it's port 
docker ps -a

14.  check the localhost:4200

