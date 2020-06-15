1.Install node,angular cli in your local PC

2. Get angular project 
ng new YOUR_APP_NAME
cd YOUR_APP_NAME

3. npm install

4. ng build --prod

5. Create a docker file with the following content 
FROM nginx:1.17.1-alpine
COPY nginx.conf /etc/nginx/nginx.conf
COPY /dist/YOUR_APP_NAME /usr/share/nginx/html

6. create a nginx.conf file with 
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

7. We can simply build by  
docker build -t your_image_name .
or we can tag the image while building as it is necessary to tag the image when u want to push it in the dockerhub
so we will use 
docker build -t your_dockerhub_username/your_image_name :version_name .

8. check docker images in the docker 
docker images

9. Run the docker image
docker run --name your_container_name-p 80:80 -d your_dockerhub_username/your_image_name :version_name

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
docker run --name your_container_name-p 80:80 -d your_dockerhub_username/your_image_name :version_name

check the running container and it's port 
docker ps -a

14.  check the localhost:80 
