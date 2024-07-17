# Week 1 — App Containerization

clone the repo from [github](https://github.com/proBaxia/proBaxia-aws-bootcamp-cruddur-2023)

cd into backend-flask

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
~~~
python3 -m flask run --host=0.0.0.0 --port=4567
~~~

![alt text](images/image2.png)


- make sure to unlock the port on the port tab
- open the link for 4567 in your browser
- append to the url to `/api/activities/home`
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

![alt text](images/image0.png)


## Run Container
~~~
docker container run --rm -p 4567:4567  backend-flask
~~~

- make sure to unlock the port on the port tab
- open the link for 4567 in your browser
- on your port make sure your port 4567  state is public and click on the link on your port Address url

![alt text](images/image1.png)

- when you click on it it will give you error like this 

![alt text](images/image-1.png)



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

## Check Container Logs
~~~sh
docker logs CONTAINER_ID -f
docker logs backend-flask -f
docker logs $CONTAINER_ID -f
~~~
## Gain Access to a Container
~~~js
docker exec CONTAINER_ID -it /bin/bash
~~~
# Delete an Image
~~~sh
docker image rm backend-flask --force
~~~

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

![alt text](images/image8.png)

### Build Container for frontend-react-js

~~~
docker build -t frontend-react-js .
~~~
### Run Container
~~~sh
docker run -p 3000:3000 -d frontend-react-js

docker ps # to view runing containers 

docker stop ContainersID # to stop runing containers 
~~~

![alt text](images/image-2.png)

#### we can use `docker compose up / docker-compose up` to build and run the Container 

### Multiple Containers
Create a docker-compose file
Create `docker-compose.yml` at the root of your project to run backend and frontend together 

~~~yml
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
- Bring it up with the code below
~~~
docker-compose up
docker compose up
~~~

![alt text](images/image9.png)

- on your port make sure your port 3000 state is public and click on the link on your port Address 

![alt text](images/image10.png)

### FORNTEND APPLICATION VIEW 

![alt text](images/image11.png)
![alt text](images/image12.png)
![alt text](images/image13.png)
![alt text](images/image14.png)


# Week 1 - Create the notification feature (Backend and Front)

if i click on notification button 

![alt text](images/image15.png)

no page to display notification 

![alt text](images/image16.png)

let's add an endpoint to our notification 🔔 button 
go to our working directory cd into `backend-flask` open the `openapi-3.0.yaml` file. on vscode click on ~Api~ extension make sure you have install the extention OpenAPI (Swagger) on your vscode or gitpod click on ~Paths~ create a part and name it `/api/activities/notifications` see thr image below 

![alt text](images/image17.png)


open api for notifications sorce code 
~~~sh
 /api/activities/notifications:
    get:
      description: 'Return a field of activities for all of those that i  follow'
      tags:
        - activities
      parameters: []
      responses:
        '200':
          description: Return an array of activities 
          content:
            application/json:
             schema:
               type: array
               items:
                 $ref: '#/components/schemas/Activity"
~~~

## lets define a new endpoint 
The entries of our application is app.py 

go to your working directory  `backend-flask` on  `app.py` copy the code below to your app.py


create a `notifications_activities.py` on your `services` folder in your backend-flask working directory 

go to your working directory  `backend-flask` on  `app.py` copy the code below to your app.py

~~~sh
@app.route("/api/activities/notifications", methods=['GET'])
def data_notifications():
  data = NotificationsActivities.run()
  return data, 200
~~~

![alt text](images/image18.png)





add this to your app.py
~~~sh
from services.notifications_activities import *
~~~

![alt text](images/image19.png)

copy the code below to your  `activities-notifications.py` we created 

~~~sh
from datetime import datetime, timedelta, timezone
class NotificationsActivities:
  def run():
    now = datetime.now(timezone.utc).astimezone()
    results = [{
      'uuid': '68f126b0-1ceb-4a33-88be-d90fa7109eee',
      'handle':  'Gabriel Akintade',
      'message': 'Cloud is fun and i need a cloud role!',
      'created_at': (now - timedelta(days=2)).isoformat(),
      'expires_at': (now + timedelta(days=5)).isoformat(),
      'likes_count': 5,
      'replies_count': 1,
      'reposts_count': 0,
      'replies': [{
        'uuid': '26e12864-1c26-5c3a-9658-97a10f8fea67',
        'reply_to_activity_uuid': '68f126b0-1ceb-4a33-88be-d90fa7109eee',
        'handle':  'Worf',
        'message': 'This post has no honor!',
        'likes_count': 0,
        'replies_count': 0,
        'reposts_count': 0,
        'created_at': (now - timedelta(days=2)).isoformat()
      }],
    },
    {
      'uuid': '66e12864-8c26-4c3a-9658-95a10f8fea67',
      'handle':  'Worf',
      'message': 'I am out of prune juice',
      'created_at': (now - timedelta(days=7)).isoformat(),
      'expires_at': (now + timedelta(days=9)).isoformat(),
      'likes': 0,
      'replies': []
    },
    {
      'uuid': '248959df-3079-4947-b847-9e0892d1bab4',
      'handle':  'Garek',
      'message': 'My dear doctor, I am just simple tailor',
      'created_at': (now - timedelta(hours=1)).isoformat(),
      'expires_at': (now + timedelta(hours=12)).isoformat(),
      'likes': 0,
      'replies': []
    }
    ]
    return results
~~~

when i want to start my backend-flask `python -m flask run --host=0.0.0.0 --port=
4567`i encounter this error bellow 

![alt text](images/image20.png)

my moulde was unable to import my `/api/activities/notifications:` we created because this what i wrote `from services.notifications_activity import *` instaed of `services.notifications_activities import *` i have updated it from  app.py file
now lets run our backend-flask
~~~
python -m flask run --host=0.0.0.0 --port=4567
~~~
i got this error.
![alt text](images/image21.png)

just because i did not call my api moudle i want to call which is `/api/activities/notifications` add it to the end of your URL 

![alt text](images/image22.png)

# Create the notification feature (Frontend) 

if we go to frontend-react-jd directory in our frontend-react-jd directory we will see the start scripts in package.json and the entry point are App.js index.js This is a react application generated by  react start scripts with all front-end Frameworks.

in `App.js` add the code below to create a notifications and create a `NotificationsFeedPage.js` and `NotificationsFeedPage.css`
~~~js
import NotificationsFeedPage from './pages/NotificationsFeedPage';
~~~

~~~js
  {
    path: "/notifications",
    element: <NotificationsFeedPage />
  },
~~~

![alt text](images/image23.png)

in the `NotificationsFeedPage.js` we created copy and paste this code below we copy the code from our `HomeFeddPages.js` and did some editing in line `1 13 23 60 and 75`

~~~js
import './NotificationsFeedPage.css';
import React from "react";

import DesktopNavigation  from '../components/DesktopNavigation';
import DesktopSidebar     from '../components/DesktopSidebar';
import ActivityFeed from '../components/ActivityFeed';
import ActivityForm from '../components/ActivityForm';
import ReplyForm from '../components/ReplyForm';

// [TODO] Authenication
import Cookies from 'js-cookie'

export default function NotificationsFeedPageFeedPage() {
  const [activities, setActivities] = React.useState([]);
  const [popped, setPopped] = React.useState(false);
  const [poppedReply, setPoppedReply] = React.useState(false);
  const [replyActivity, setReplyActivity] = React.useState({});
  const [user, setUser] = React.useState(null);
  const dataFetchedRef = React.useRef(false);

  const loadData = async () => {
    try {
      const backend_url = `${process.env.REACT_APP_BACKEND_URL}/api/activities/notifications`
      const res = await fetch(backend_url, {
        method: "GET"
      });
      let resJson = await res.json();
      if (res.status === 200) {
        setActivities(resJson)
      } else {
        console.log(res)
      }
    } catch (err) {
      console.log(err);
    }
  };

  const checkAuth = async () => {
    console.log('checkAuth')
    // [TODO] Authenication
    if (Cookies.get('user.logged_in')) {
      setUser({
        display_name: Cookies.get('user.name'),
        handle: Cookies.get('user.username')
      })
    }
  };

  React.useEffect(()=>{
    //prevents double call
    if (dataFetchedRef.current) return;
    dataFetchedRef.current = true;

    loadData();
    checkAuth();
  }, [])

  return (
    <article>
      <DesktopNavigation user={user} active={'notifications'} setPopped={setPopped} />
      <div className='content'>
        <ActivityForm  
          popped={popped}
          setPopped={setPopped} 
          setActivities={setActivities} 
        />
        <ReplyForm 
          activity={replyActivity} 
          popped={poppedReply} 
          setPopped={setPoppedReply} 
          setActivities={setActivities} 
          activities={activities} 
        />
        <ActivityFeed 
          title="Notifications" 
          setReplyActivity={setReplyActivity} 
          setPopped={setPoppedReply} 
          activities={activities} 
        />
      </div>
      <DesktopSidebar user={user} />
    </article>
  );
}
~~~

now lets bring up our Frontend up with 
~~~
docker-compose up 
~~~
my port 3000 was not coming up so i updates my frontend-react-js working director

~~~
npm i

npm audit fix --force
~~~
my 3000 port is up but my user interface is giving me error 

![alt text](images/image24.png)

problem i did not update my code in `App.js` line 4 well . it was suppose to be  
~~~js
import NotificationsFeedPage from './pages/NotificationsFeedPage';
~~~

![alt text](images/image25.png)

After i update it i reload my home page 📄 and notification page 🔔 

Frontend-react-Home Page

![alt text](images/image26.png)

Frontend-react-Notifications Page 

![alt text](images/image27.png)

# Week 1 - DynamoDB and Postgres vs Docker

### Adding DynamaDB Local and Postgres 
- make sure you have install AWS cli and login 

we are going to use Postgres and DynamoDB local in funture labs We can bring them in as container and references them externally

Let's  intergtratr the following into existing docker compose file 


 copy and paste the Dynamodb code to your `docker-compose` file
~~~js
  dynamodb-local:
    # https://stackoverflow.com/questions/67533058/persist-local-dynamodb-data-in-volumes-lack-permission-unable-to-open-databa
    # We needed to add user:root to get this working.
    user: root
    command: "-jar DynamoDBLocal.jar -sharedDb -dbPath ./data"
    image: "amazon/dynamodb-local:latest"
    container_name: dynamodb-local
    ports:
      "- "8000:8000
    volumes:
      - "./docker/dynamodb:/home/dynamodblocal/data"
    working_dir: /home/dynamodblocal

  postgres-local

  db:
    image: postgres:13-alpine
    restart: always
    environment:
      - POSTGRES_USER=postgres
      - POSTGRES_PASSWORD=password
    ports
      - '5432:5432'
    volumes: 
      - db:/var/lib/postgresql/data
volumes:
  db:
    driver: local     
~~~

### Volumes `we map the volume already in our docker compose file ` 
directory volume mapping
~~~sh
volumes: 
- "./docker/dynamodb:/home/dynamodblocal/data"
~~~

directory volume mapping
~~~sh
volumes: 
  - db:/var/lib/postgresql/data

volumes:
  db:
    driver: local
~~~

![alt text](images/image28.png)
![alt text](images/image29.png)


after that do `docker compose up`
copy and past this link to your terminal 

#### Create a dynamodb create-table
~~~sh
aws dynamodb create-table \
    --endpoint-url http://localhost:8000 \
    --table-name Music \
    --attribute-definitions \
        AttributeName=Artist,AttributeType=S \
        AttributeName=SongTitle,AttributeType=S \
    --key-schema AttributeName=Artist,KeyType=HASH AttributeName=SongTitle,KeyType=RANGE \
    --provisioned-throughput ReadCapacityUnits=1,WriteCapacityUnits=1 \
    --table-class STANDARD
~~~
####  create and  item
 ~~~
 aws dynamodb put-item \
    --endpoint-url http://localhost:8000 \
    --table-name Music \
    --item \
        '{"Artist": {"S": "No One You Know"}, "SongTitle": {"S": "Call Me Today"}, "AlbumTitle": {"S": "Somewhat Famous"}}' \
    --return-consumed-capacity TOTAL  
 ~~~
#### List Tables List Tables
~~~
aws dynamodb list-tables --endpoint-url http://localhost:8000
~~~

![alt text](images/image-3.png)


(References)[https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Tools.CLI.html]
Example of using DynamoDB local (link)[https://github.com/100DaysOfCloud/challenge-dynamodb-local]

#### install Postgress client drive in your gitpod.yml or copy and paste it to your terminal 
~~~js
  - name: postgres
    init: |
      curl -fsSL https://www.postgresql.org/media/keys/ACCC4CF8.asc|sudo gpg --dearmor -o /etc/apt/trusted.gpg.d/postgresql.gpg
      echo "deb http://apt.postgresql.org/pub/repos/apt/ `lsb_release -cs`-pgdg main" |sudo tee  /etc/apt/sources.list.d/pgdg.list
      sudo apt update
      sudo apt install -y postgresql-client-13 libpq-dev
~~~      
- install SQLTools PostgreSQL/Cockroach Drive extension 
- restart your terminal and paste the code below to check it you have postgres


~~~
psql  -Upostgres --host localhost
~~~
![alt text](images/image30.png)
