---
title: xxxxxxx
date: 2025-03-31
filename: "test.md"
layout: post


---

# Introduction

If, like me, after running AI models like GPT, you want to experiment a little, I hope the following can help you.

Ollama is an open-source tool that runs large language models (LLMs) directly on a local machine.

This is my simplest set up to get the basics working.

I use Docker containers, rather than Python virtual environments, mainly because they are self contained.

The following sets up a container to run the Tinyllama model with a very basic Gradio based web interface.


# Prerequisits

```Hardware``` Obviously the bigger the better...but you can get by with not much. My sandpit system is actually an old Xiaomi Mi A1 smartphone running Alpine linux.

```Docker``` Installing docker on your specific hardware is well doumented, see [Get started with Docker]( https://www.docker.com/get-started/) ; personally, I SSH into my machine and use the command line [docker engine]( https://docs.docker.com/engine/install).

```Python``` 

```Git```


# Running

```
# Download the repository
git clone https://github.com/bryansplace/local_ollama_chatbot.git

# Move into project folder
cd local_ollama_chatbot

# Build and start the containers
docker-compose up -d --build

# Pull the llm model into the ollama container
docker exec -it ollama ollama pull tinyllama

```

Open  your web browser to, eg, 192.168.x.xxx:7860 to open the chatbot interface. Type in your message to the chatbot, submit and wait for a reply

# Explaination

## Docker

Starting with the docker-compose.yaml file, we need  two 'services'
   ```ollama``` which serves the llm model on port 11434 by default. 
    ```chatbot``` the web interface using gradio.

```
version: '3.8'

services:
  ollama:
    image: ollama/ollama:0.6.3
    container_name: ollama
    restart: unless-stopped
    ports:
      - "11434:11434"
    volumes:
      - ollama_data:/root/.ollama

  chatbot:
    build: ./chatbot
    container_name: chatbot
    restart: unless-stopped
    ports:
      - "7860:7860"
    depends_on:
      - ollama
    environment:
      - OLLAMA_BASE_URL=http://ollama:11434

volumes:
  ollama_data:
```
## Ollama

The standard dockerhub  [ollama image](https://hub.docker.com/r/ollama/ollama) is used without gpu. The specific 0.6.3 version is the latest at time of writing.
No  llm ( Large language model) is included in the image and the specific model 'tinyllama' needs to be pulled in as specified above. The specific model needs to be the same when gradio requests the response in the chatbot app.py code below.
Once simple tinyllama is working, other models can be played with, depending on your hardware and patience.


## Chatbot

In a sub directory named chatbot, there are three files
  1) app.py ; the python code for the interface
  2) requirements.txt ; the required python dependancies 
  3) dockerfile ; the instructions to build the chatbot container.

app.py
This launches the web server on local host port 7860. The interface is minimal input and response.


```
import gradio as gr
import requests
import os

OLLAMA_URL = os.getenv("OLLAMA_BASE_URL", "http://localhost:11434")

def chat_with_ollama(prompt):
    response = requests.post(
        f"{OLLAMA_URL}/api/generate",
        json={"model": "tinyllama", "prompt": prompt,"stream": False}
    )
    if response.status_code == 200:
        return response.json()["response"]
    else:
        return "Error communicating with Ollama."

iface = gr.Interface(
    fn=chat_with_ollama,
    inputs=gr.Textbox(label="Input"),
    outputs=gr.Textbox(label="Ollama's Response"),
    title="Bryan's Chatbot",
    description="Talk to my AI"
)

if __name__ == "__main__":
    iface.launch(server_name="0.0.0.0", server_port=7860)
```
requirements.txt
```
gradio
requests
```

dockerfile
```
FROM python:3.10

WORKDIR /app

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY . .

CMD ["python", "app.py"]
```

