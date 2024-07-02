# docker-challenge-template

# Docker Challenges

## Overview

This repository contains solutions for two Docker-related challenges. The first challenge involves serving static pages using Docker and Nginx, while the second challenge involves creating a Dockerized Node.js application with Docker Compose.

## Challenge 1 - Simple Static Page Server

### Goals
1. Learn the basics of Docker.
2. Build a Docker image.
3. Run the container and see the results in a browser.
4. Publish the Dockerfile and related files on GitHub.

### Steps
1. Create a folder named `challenge1`.
2. Inside `challenge1`, create a folder named `public`.
3. Create an `index.html` file in the `public` folder with the following content:
    ```html
    <!DOCTYPE html>
    <html>
    <head>
        <title>SAIT Student</title>
    </head>
    <body>
        <h1>Your Name</h1>
        <p>SAIT ID: YourID</p>
    </body>
    </html>
    ```
4. Create a `Dockerfile` in the `challenge1` folder with the following content:
    ```Dockerfile
    FROM nginx:alpine
    COPY public /usr/share/nginx/html
    ```
5. Build the Docker image and run the container:
    ```sh
    docker build -t static-page-server .
    docker run -d -p 8080:80 --name static-page-container static-page-server
    ```
6. Access the page at `http://localhost:8080/`.

## Challenge 2 - NodeJS Application

### Goals
1. Introduce the concept of Docker Compose.
2. Create a Dockerfile for a NodeJS application.
3. Create a Docker Compose file to orchestrate the web server and the application.

### Steps
1. Create a folder named `challenge2`.
2. Extract the NodeJS application files into the `challenge2` folder.
3. Ensure the main file is named `server.js`.
4. Create a `Dockerfile` in the `challenge2` folder with the following content:
    ```Dockerfile
    FROM node:14
    WORKDIR /usr/src/app
    COPY . .
    RUN npm install
    EXPOSE 3000
    CMD ["node", "server.js"]
    ```
5. Create a `docker-compose.yml` file in the `challenge2` folder:
    ```yaml
    version: '3.9'

    services:
      nginx:
        image: nginx:alpine
        ports:
          - "8080:80"
        volumes:
          - ./nginx.conf:/etc/nginx/nginx.conf:ro
        depends_on:
          - app

      app:
        build: .
        ports:
          - "3000:3000"
    ```
6. Create an `nginx.conf` file in the `challenge2` folder:
    ```nginx
    events { }

    http {
        server {
            listen 80;

            location / {
                proxy_pass http://app:3000;
                proxy_set_header Host $host;
                proxy_set_header X-Real-IP $remote_addr;
                proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
                proxy_set_header X-Forwarded-Proto $scheme;
            }

            location /api/books {
                proxy_pass http://app:3000/api/books;
            }

            location /api/stats {
                proxy_pass http://app:3000/api/stats;
            }
        }
    }
    ```
7. Build the Docker image and run the containers:
    ```sh
    docker-compose down
    docker-compose up --build -d
    ```
8. Access the application at `http://localhost:8080/api/books` and `http://localhost:8080/api/stats`.

## Errors and Solutions

### Port 8080 Already in Use
- **Error:** `Bind for 0.0.0.0:8080 failed: port is already allocated`
- **Solution:** Stopped the process using port 8080 and freed up the port.

### Cannot Find Module 'app.js'
- **Error:** `Error: Cannot find module '/usr/src/app/app.js'`
- **Solution:** Ensured the main file `server.js` existed and updated the Dockerfile to use `server.js`.

### 502 Bad Gateway
- **Error:** `502 Bad Gateway nginx/1.27.0`
- **Solution:** Verified that the Node.js application was running correctly and accessible from Nginx. Checked logs and ensured correct proxy configuration in `nginx.conf`.
