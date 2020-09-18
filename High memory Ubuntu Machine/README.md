## Author(s): 
Poornapragna Vadiraj, Varun Bhaseen, Mirsaeid Abolghasemi

Deep Learning CMPE 258 - Term Project - Professor Vijay Eranti - Spring 2020

## Introduction:

Attention mechanisms are broadly used in present image captioning encoder / decoder frameworks, where at each step a weighted average is generated on encoded vectors to direct the process of caption decoding. 
However, the decoder has no knowledge of whether or how well the vector being attended and the attention question being given are related, which may result in the decoder providing erroneous results. 
Image captioning, that is to say generating natural automatic descriptions of language images are useful for visually impaired images and for the quest of natural language related pictures. 
It is significantly more demanding than traditional vision tasks recognition of objects and classification of images for two guidelines. 
First, well formed structured output space natural language sentences are considerably more challenging than just a set of class labels to predict. 
Secondly, this dynamic output space enables a more thin understanding of the visual scenario, and therefore also a more informative one visual scene analysis to do well on this task.

## Setup:

Using Docker to deploy the service is much easier. You can save most work described above.
Check out tiangolo/uwsgi-nginx-flask-docker to find and download a suitable version of Dockerfile.

Create a file kf_upload.conf, in which add:

```
client_body_buffer_size 5m;
```

Then, write the Dockerfile as below:

```
FROM tiangolo/uwsgi-nginx-flask:python3.6

COPY requirements.txt /
COPY kf_upload.conf /etc/nginx/conf.d/
RUN pip install -i https://pypi.tuna.tsinghua.edu.cn/simple -r /requirements.txt
```

Finally, in the directory including Dockerfile build a customized version of Docker image by:
```
$ docker build -t kf-customized-image .
```

## Create and run a Docker container with the customized image
Create a new folder for deploying you api, in which there’s a Dockerfile and a subfolder named app.

Copy the myproject.py file mentioned in the previous section into the app folder, rename it as main.py. Also remember to copy the files used in the main.py (e.g. disease.model, labels.npz) in to app folder too.

In the base folder (which inlcudes th app folder and the Dockerfile), create a uwsgi.ini file, add the following code:

```
[uwsgi]
socket = /tmp/uwsgi.sock
chown-socket = nginx:nginx
chmod-socket = 664
cheaper = 0
processes = 1
master = false
```

Then, add the following code into the Dockerfile:
```
FROM kf-customized-image
COPY uwsgi.ini /etc/uwsgi/

COPY ./app /app
```

Build the final version of image that is ready to use, in the base folder:
```
$ docker build -t kf-ready-to-deploy-image .
```

Now, everything is ready for deployment. You can check if your image is ready in your Docker by:
```
$ docker image ls
```

Finally, one last step you need to do is to run the image as a container.

In any working directory, just run:
```
$ docker run -p 80:80 kf-ready-to-deploy-image
```

If unable to understand on how to run then follow the link below:
(https://zkf85.github.io/2018/12/03/flask-uwsgi-nginx-deploy#21-flask)
