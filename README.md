# Docker Challenge Template
This template provide a clear and concise set of instructions for successfully completing two Docker Challenges: serving static pages with NGINX and containerizing a NodeJS application.

## Challenge 1
Serve static page using Docker and NGINX.

### Steps ###
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
    docker build -t dockerchallenge1 .
    ```

5. After that, I ran the Docker container using the following command:

    ```bash
    docker run -d --name dockerchallenge1 -p 8080:80 dockerchallenge1
    ```

6. I tested the application by accessing http://localhost:8080 and ensuring that the `index.html` was displayed correctly.
7. I then took screenshots of my result, terminal, and my docker container.

## Challenge 2
Containerize and serve a NodeJS application using Docker and NGINX.

### Steps ###
1. I extracted `package-lock.json`, `package.json`, and `server.js` files from `challenge2.zip` provided on D2L to `challenge2` folder.
2. I created a `Dockerfile`

    ```Dockerfile
    FROM node:14
    WORKDIR /app
    COPY package.json package-lock.json /app/
    RUN npm install
    COPY . /app
    EXPOSE 3000
    CMD ["node", "server.js"]
    ```

3. Next, I created a `docker-compose` in the same directory.

    ```docker-compse
    version: '3'
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
        - "8000:80"
    ```
4. I also created a `nginx.conf` in still the same directory.

    ```nginx.conf
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
    ```

