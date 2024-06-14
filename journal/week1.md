# Week 1 — App Containerization

clone the repo from [github](https://github.com/proBaxia/proBaxia-aws-bootcamp-cruddur-2023)

cd into backendend 

create a Dockerfile 

~~~
FROM python:3.10-slim-buster

WORKDIR /backend-flask

COPY requirements.txt requirements.txt

RUN pip install -r requirements.txt

COPY . .

ENV FLASK_ENV=development

EXPOSE ${port}

CMD [ "python","-m","flask","run","--host=0.0.0.0","--port=4567"  ]

~~~

# Install python version
```
pyenv install 3.10.9
```

# Set your python version
```
pyenv global 3.10.9
```

# Create virual environment
```
python -m venv venv
```

# Activate environment
```
source venv/bin/activate
```

# Install Flask
```
pip install flask
```

![ ](images/image.png)

## Export your variables into your terminal
export FRONTEND_URL="*"
export BACKEND_URL="*"
## Bring on the app by runing the code below 
python3 -m flask run --host=0.0.0.0 --port=4567

![alt text](images/image2.png)


- make sure to unlock the port on the port tab
- open the link for 4567 in your browser
- append to the url to /api/activities/home
you should get back json
- on your port make sure your port state is public and click on the link on your port Address 

![alt text](images/image1.png)

- you will get a link like this 

 https://4567-probaxia-probaxiaawsboo-sue6j7331dj.ws-eu114.gitpod.io/

- when you click on it it will give you error like this 
![alt text](images/image4.png)

so add the `/api/activities/home` to your url 

https://4567-probaxia-probaxiaawsboo-sue6j7331dj.ws-eu114.gitpod.io/api/activities/home

![alt text](images/image3.png)

## Build container
- FIRST delete the environment variables  BACKEND and FRONEND
```
unset BACKEND
unset FRONEND
```
- Lets work on the Dockerfile we created to containerization our application 

- build your app with docker build  
move back ousside your director backend-flask and run this code to build your docker images  

~~~
docker build -t  backend-flask ./backend-flask
~~~
check if your docker has build your images 
~~~
docker images
~~~

![alt text](images/image5.png)

## Run Container
~~~
docker container run --rm -p 4567:4567  backend-flask
~~~

- make sure to unlock the port on the port tab
- open the link for 4567 in your browser
- on your port make sure your port 4567  state is public and click on the link on your port Address url

![alt text](images/image1.png)

- when you click on it it will give you error like this 

![alt text](images/image4.png)

- reason we are getting this error is because we haven't set the environment variables 
so let run this command 
~~~
FRONTEND_URL="*"BACKEND_URL="*" docker container run --rm -p 4567:4567 backend-flask
~~~
stil not working lets check if the environment variables is save by going to tha icon of docker in your vscode/gitpod click on containers and right click on backend-flask attach shell . on the attach shell  terminal type env to view your environment variables
~~~
env 
~~~

![alt text](images/image6.png)

- run this code to in put your environment variables 
~~~
docker container run --rm -p 4567:4567  -e FRONTEND_URL='*' -e BACKEND_URL='*' backend-flask
~~~
- check docker attach shell you will find environment variables `FRONTEND_URL=* BACKEND_URL=*`

![alt text](images/image7.png)

- on your terminal port make sure your port 4567  state is public and click on the link on your port Address url

![alt text](images/image1.png)

- Add `/api/activities/home` to the back of your url , something like this

https://4567-probaxia-probaxiaawsboo-sue6j7331dj.ws-eu114.gitpod.io/api/activities/home

## Get Container Images or Running Container Ids
docker ps
docker images

## Containerize Frontend
### RUN NPM Install
We have to run NPM Install before building the container since it needs to copy the contents of node_modules
~~~
cd frontend-react-js
npm i
~~~

### Create Docker File
Create a file here: frontend-react-js/Dockerfile

~~~
FROM node:16.18

ENV PORT=3000

COPY . /frontend-react-js
WORKDIR /frontend-react-js
RUN npm install
EXPOSE ${PORT}
CMD ["npm", "start"]
~~~

### Multiple Containers
Create a docker-compose file
Create `docker-compose.yml` at the root of your project to run backend and frontend together 

~~~
version: "3.8"
services:
  backend-flask:
    environment:
      FRONTEND_URL: "https://3000-${GITPOD_WORKSPACE_ID}.${GITPOD_WORKSPACE_CLUSTER_HOST}"
      BACKEND_URL: "https://4567-${GITPOD_WORKSPACE_ID}.${GITPOD_WORKSPACE_CLUSTER_HOST}"
    build: ./backend-flask
    ports:
      - "4567:4567"
    volumes:
      - ./backend-flask:/backend-flask
  frontend-react-js:
    environment:
      REACT_APP_BACKEND_URL: "https://4567-${GITPOD_WORKSPACE_ID}.${GITPOD_WORKSPACE_CLUSTER_HOST}"
    build: ./frontend-react-js
    ports:
      - "3000:3000"
    volumes:
      - ./frontend-react-js:/frontend-react-js

# the name flag is a hack to change the default prepend folder
# name when outputting the image names
networks: 
  internal-network:
    driver: bridge
    name: cruddur
~~~

![alt text](images/image8.png)

### Build Container
~~~
docker build -t frontend-react-js ./frontend-react-js
~~~
### Run Container
~~~
docker run -p 3000:3000 -d frontend-react-js
~~~
#### we can use `docker compose up / docker-compose up` to build and run the Container 
~~~
docker-compose up
docker compose up
~~~

![alt text](images/image9.png)

- on your port make sure your port 3000 state is public and click on the link on your port Address 

![alt text](images/image10.png)

### FORNTEND APPLICATION VIEW 

![alt text](images/image11.png)




