---
title: Jupyter Notebook in a Docker Container
date: 2024-09-13
---

# Setting Up and Running Jupyter Notebook in a Docker Container


I want to run a Jupyter Notebook environment in a Docker container. I want the classic notebook, not the jupyterlabs. As I run on my trusted lan, I do not want to be bothered with a password or access token.

## Step 1: Install Docker

Ensure that Docker is installed on your machine. If you haven’t installed Docker yet, you can download it from the [official Docker website](<https://www.docker.com/get-started>). Run ``` docker ps``` to check whata running.

## **Step 2: Project Setup**

Create a dedicated directory for the Jupyter Notebook project. 

```
mkdir ~\projects\jupyter
```
Inside this directory, set up the following files.

## Step 2.1: Dockerfile

Create a file named `Dockerfile` and add the following content:

```
# FROM python:3.8

# Set the working directory to /app
WORKDIR /app

# Install Jupyter Notebook
RUN pip install jupyter

# If required
# Install any needed packages 
# specified in requirements.txt
# RUN pip install --no-cache-dir -r requirements.txt

# Make port 8888 available to the world outside this container
EXPOSE 8888

# Define environment variable
ENV NAME World

# Run Jupyter Notebook when the container launches
CMD ["jupyter", "notebook", "--ip=0.0.0.0", "--port=8888", "--no-browser", "--allow-root", "--NotebookApp.token=''",  "--NotebookApp.password=''"]
```

## Step 2.2 : requirements.txt 

If required, create a `requirements.txt` file listing project dependencies.


## Step 3: Build Docker Image

Open a terminal and navigate to the project directory, run the following command to build the Docker image:
```
docker build -t my-jupyter .
```
( don't miss out the trailing period point)

## Step 4: Docker Compose

Create the docker-compose.yml file:

```
# version: '3'
services:
  jupyter:
    build: .
    container_name: my-jupyter
    user: root
    image: my-jupyter
    restart: always
    volumes:
      - ./notebooks:/app/notebooks
    environment:
      - NB_USER=bryan
      - NB_UID=10000
      - NB_GID=10000
    ports:
      - '8888:8888'
```

This command maps port 8888 on the host machine to port 8888 on the Docker container

Start the container
```
docker compose up -d
```

## Step 5: Access Jupyter Notebook

Open a web browser and go to [[http://localhost:8888]](http://localhost:8888](http://localhost:8888).)

on the place of “localhost” you need to paste the the  ip to access the jupyter notebook

## Step 6: Stop Docker Container

To stop the running Docker container  use `Ctrl+C` in the terminal.


