# pythonapp-K8

Deploying Python App On Kubernetes


In this article we will begin with expanding our knowledge on Kubernetes & continue with deploying the Dockerized Flask AI Application on kubernetes.

Note: Deploying to Kubernetes requires a containerized application

Prerequisites:

Make sure you have system with ubuntu 18.04 with already installed:

-->Docker Engine 

-->Docker Compose

-->minikube

The workflow will look like this:

1- We will create Dockerfiles for each service to build images.

2 -We will create docker-compose.yml to run multiple containers together.

3- Deploy the entire stack with the docker compose command.

4- Pushing the docker images to docker hub.

5- Create a ConfigMap with Nginx configuration file.

6- Create a multicontainer Pod (webapp and nginx in separate containers)

7-Expose the Pod using nodePort service.

Step-01:

-Clone the project from GitHub.

(https://github.com/Cryptic-Gemini/pythonapp-K8.git)


Step-02:

-Dockerize the Python App with Docker Compose.

Step-03:

-Push your images to Docker Hub.

In my case the images that formed from Docker Compose were:

==> pythonk8_flask

==> pythonk8_nginx

One important thing to know is for pushing an image to docker hub,we need our images to be built as 

<hub-username>/repository-name:tag

-First you will have to re-tag your image with hub-username.

==> $docker tag pythonk8_flask gzlkhan/pythonk8_flask

==> $docker tag pythonk8_nginx gzlkhan/pythonk8_nginx

-Now you can push these images to Docker Hub by:

==> $docker image push gzlkhan/pythonk8_flask

==> $docker image push gzlkhan/pythonk8_nginx


Deploy the Application to Kubernetes

For deployment of Python App to Kubernetes,we will create two important files:

1- ConfigMap File 

2- Pod File         

In our case we have these two files as:

==> configmap1.yaml

==> pods1.yaml



Step-04:

-Create a ConfigMap with Nginx configuration file.

A ConfigMap is a dictionary of key-value pairs that store configuration settings for your applications.

$kubectl create -f configmap1.yaml

Step-05:

-Create a multi-container Pod with flask app and nginx in separate containers.

$kubectl create -f pods1.yaml

Note we cannot access port 2020 from outside the port.We define only nginx port 80.Add ConfigMap in your Pods and use its configurations.

We will populate a volume with data stored in ConfigMap.Add the ConfigMap name (python-nginx-conf) under the volumes section of Pod spec.This will add the ConfigMap data to the directory specified as volumeMounts.mountPath (/etc/nginx/nginx.conf)

Step-06:

-Expose the Pod using the NodePort service:


$kubectl expose pod pythonpod --type=NodePort --port=80 

service “pythonpod” exposed


Step-07:

Identify port on the node that is forwarded to the Pod by :

$kubectl describe service pythonpod


...

NodePort: <unset> 32404/TCP

...


Now you can rush to your browser to navigate to your node’s port to access the web application through reverse proxy.


Minikube-ip:NodePort

192.168.99.100:32404


Note: You can get your minikube-IP by:

$ minikube ip
