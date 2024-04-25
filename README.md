# Docker Challenge Template
This template provide a clear and concise set of instructions for successfully completing two Docker Challenges: serving static pages with NGINX and containerizing a NodeJS application.

## Challenge 1
Serve static page using Docker and NGINX.

### Steps
1. I created a `public` folder in the `challenge1` directory.
2. I created a `index.html` file inside the `public` folder. This file contains my student information, including my name and student ID.
3. I created a `Dockerfile`

    ```Dockerfile
    FROM nginx
    COPY public /usr/share/nginx/html
    EXPOSE 8080
    CMD ["nginx", "-g", "daemon off;"]
    ```

4. Then I opened the terminal and built the Docker Image by using the following command:

    ```bash
    docker build -t dockerchallenge1
    ```

5. After that, I ran the Docker container using the following command:

    ```bash
    docker run -d --name dockerchallenge1 -p 8080:80 dockerchallenge1
    ```

6. I tested the application by accessing http://localhost:8080 and ensuring that the `index.html` was displayed correctly.
7. I then took screenshots of my result, terminal, and my docker container.

## Challenge 2
Containerize and serve a NodeJS application using Docker and NGINX.

### Steps
1. I extracted `package-lock.json`, `package.json`, and `server.js` files from `challenge2.zip` provided on D2L to `challenge2` folder.
2. I created a `Dockerfile`

    ```Dockerfile
    FROM node:14
    WORKDIR /usr/src/app
    COPY package*.json ./
    RUN npm install
    COPY . .
    EXPOSE 3000
    CMD ["node", "server.js"]
    ```

3. Next, I created a `docker-compose` in the same directory.

    ```docker-compose
    version: '3.8'
    services:
    app:
        build: .
        ports:
        - "3000:3000"

    nginx:
        image: nginx
        volumes:
        - ./nginx.conf:/etc/nginx/nginx.conf
        ports:
        - "8080:80"
    ```

4. I also created a `nginx.conf` in still the same directory.

    ```nginx.conf
    events {}

    http {
        server {
            listen 80;

            location /api/ {
                proxy_pass http://app:3000;
                proxy_http_version 1.1;
                proxy_set_header Upgrade $http_upgrade;
                proxy_set_header Connection 'upgrade';
                proxy_set_header Host $host;
                proxy_cache_bypass $http_upgrade;
            }
        }
    }
    ```

5. After finished all of that, I navigated to the terminal and executed the following command to build and run the Docker container:

    ```bash
    docker-compose up --build
    ```

6. To check for the result, I visited http://localhost:8080/api/books/ and then also visited http://localhost:8080/api/books/1 to ensure if both pages were displayed correctly.
7. To finish, I took screenshots of my results, the terminal, and my docker containers. 

## Challenge 3
Full-stack application consists of three components: a web server (NGINX), a Node.js application, and database (MariaDB) using Docker Compose.

### Steps
1. I created `challenge3` folder in the root directory.
2. I extracted `docker` folder in the `challenge3` zip to `challenge3` folder that I just created in step 1.
3. After that, I created 2 more files, which are `.env` and `docker-compose.yml`.
4. For `.env`, I inserted these lines of code:
    ```.env
    # Application config
    DB_ROOT_PASSWORD=password
    DB_DATABASE=myDB
    DB_USERNAME=myUser
    DB_PASSWORD=password
    DB_HOST=db

    # Database config
    MYSQL_ROOT_PASSWORD=password
    MYSQL_DATABASE=myDB
    MYSQL_USER=myUser
    MYSQL_PASSWORD=password
    MYSQL_HOST=db
    ```

5. For `docker-compose.yml`, I put in these lines of code:
    ```docker-compose
    version: '3.8'

    services:
    nginx:
        image: nginx:alpine
        volumes:
        - ./docker/nginx/nginx.conf:/etc/nginx/conf.d/default.conf
        ports:
        - "8080:80"
        depends_on:
        - node-service
    node-service:
        build: ./docker/api
        environment:
        - DB_HOST=${DB_HOST}
        - DB_USERNAME=${DB_USERNAME}
        - DB_PASSWORD=${DB_PASSWORD}
        - DB_DATABASE=${DB_DATABASE}
        depends_on:
        - db
    db:
        image: mariadb
        environment:
        - MYSQL_ROOT_PASSWORD=${MYSQL_ROOT_PASSWORD}
        - MYSQL_DATABASE=${MYSQL_DATABASE}
        - MYSQL_USER=${MYSQL_USER}
        - MYSQL_PASSWORD=${MYSQL_PASSWORD}
        volumes:
        - ./docker/db/init/init.sql:/docker-entrypoint-initdb.d/init.sql
    volumes:
    db-data:
    ```

6. Then, I opened the terminal for `challenge3` folder, I executed the command below. However, I ran into some errors.
    ```bash
    docker-compose up --build
    ```

7. After some considerations, I decided to make some adjustments for `Dockerfile` in `api` and `db` folder.
    Modified version of `Dockerfile` in `api` folder:
    ```Dockerfile
    # Use the official Node.js image as base
    FROM node:alpine

    # Set the working directory in the container
    WORKDIR /app

    # Copy package.json and package-lock.json
    COPY package*.json ./

    # # Install dependencies
    RUN npm install

    # Copy the rest of the application
    COPY . .

    # Expose port 3000 to the outside world
    EXPOSE 3000

    # Command to run the application
    CMD ["node", "server.js"]
    # CMD ["sleep", "infinity"]
    ```

    Modified version of `Dockerfile` in `db` folder:
    ```Dockerfile
    FROM mariadb

    COPY init/init.sql /docker-entrypoint-initdb.d/
    ```

8. Once I finished my adjustments, I repeated step 6 and I successfully build and run the Docker container.
9. To check the results, I visited http://localhost:8080/api/books/ to see if I got a JSON message with all books.
10. I also visited http://localhost:8080/api/books/1 to see if I got a JSON message with only 1 book.
11. After that, I took screenshots of my terminal, my Docker container, and my results.

## Challenge 4
Focus on scaling up the Node.js service from 1 instance to three instances.

### Steps
1. Since the goal is to scale the Node.js service from 1 to 3 instances, no extra implementation is required. I used the same code from previous `challenge3` and decided not to copy `challenge3` code to `challenge4` folder.
2. To scale the Node.js service, I made sure that I correctly navigated to `challenge3` terminal, and executed this command
    ```bash
    docker-compose up -d --scale node-service=3
    ```
3. I then made a GET request to my application http://localhost:8080/api/stats three times. The field `hostname` should be different every instance.
4. To further verify the scaling, I also executed another command in the terminal and made sure all 3 `node-service` was created and running successfully.
    ```bash
    docker-compose ps
    ```
5. Then, I screenshot the results of my 3 instances from step 3 and my terminal from step 2 and 4.