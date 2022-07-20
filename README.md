# CodeWithMoshDockerCourse

This repository contains the final result of the project used in ["The Ultimate Docker Course"](https://codewithmosh.com/p/the-ultimate-docker-course) by [CodeWithMosh](https://codewithmosh.com/) to study and understand Docker.

The only change to make when we want to run on a container provider our images is to add the IP of the remote docker machine inside /frontend/Dockerfile.prod.

The application is a web app, with React used for the frontend and Node.js used for the backend. So, running `npm i` have to be done before building images.
It is also used a local mongodb to connect our application to a database.

In the Docker.md file there are the notes I've taken during the course.
