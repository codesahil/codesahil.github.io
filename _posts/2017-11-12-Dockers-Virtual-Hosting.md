---
layout: post
category: Technical
tags: Dockers
title: Isolated Virtual Hosting Using Dockers!
---

## A Little Overview of What i Did.

I earlier worked on a project to virtually host multiple website or virtual host environment containers where every different website or environment is having a different container.Normal Apache or nginx single container can also be used for virtual hosting but using a single different container for a website makes it more secure and efficent as a single container coming down does not bring the other website down as all website have thier own different container for even more enhanced security and isolation to achieve that I used a reverse proxy with Docker.

## So Why used a Reverse Proxy 
Docker containers are assigned random IPs and ports which makes addressing them much more complicated from a client perspsective. By default, the IPs and ports are private to the host and cannot be accessed externally unless they are bound to the host.
Binding the container to the hosts port can prevent multiple containers from running on the same host. For example, only one container can bind to port 80 at a time. This also complicates rolling out new versions of the container without downtime since the old container must be stopped before the new one is started.
A reverse proxy can help with these issues as well as improve availabilty by facilitating zero-downtime deployments.

### Some more advantages of Reverse Proxy
* Multiple Ports 
* Multiple Hosts
* Wildcard Hosts
* SSL Backends
* SSL Support
* Diffie Hellman Groups
* Wildcard Certificates

*Traefik* is widely used as reverse proxy on container. Here is official link to Traefik https://traefik.io/ .

I used _jwilder/nginx-proxy_ docker container. https://github.com/jwilder/nginx-proxy

## Understand the Scenario

Imagine you have a bunch of apps or website in a variety of programming languages, each application should respond to a different domain and you want to bundle everything in the same machine.

## So What I Did

### First App/Website
You can either build a container for your own website or web application using dockerfile or just for an example lets run a nginx server using offical docker image.

```
docker pull nginx
```

```
docker run -it -p 80:80 -d nginx

```

### Second App/Website
Similar to First Application or website you can build or run your website . Remember You cannot assign the same port 80 once is alloted and as it would need to run on port 80 


```
docker pull tomcat
```

```
docker run -p 8888:90 -d tomcat

```
Now access your webpages through its public IP and you should see the PHP app on port 80 and Tomcat on port 8888.In most of the cases its running on IP 0.0.0.0 but you can check using **docker ps** command .

We are advancing, but our problem has not been solved yet, we still need to point different domains to those containers and access all of them on port 80.

## Solution to our problem 
So far we are binding our Webpages  ports to container ports, but we need to bind domains to containers.

We’ll use the jwilder/nginx-proxy project, which is already configured and good to go:

```
docker pull jwilder/nginx-proxy

```

Run this image on port 80:

```
docker run -d -p 80:80 -v /var/run/docker.sock:/tmp/docker.sock jwilder/nginx-proxy

```
Now if you access your Webpage from its public IP you’ll get a 503 Service Temporarily Unavailable response, which is good!

Nginx is just saying it doesn’t know how to serve your request as it is not configured to point to anywhere.
Given that your domains are pointing to the Web Application, we can run our containers using the VIRTUAL_HOST param, which will do all the magic and bind the domains to the running containers.

```
docker run -d -e VIRTUAL_HOST=website1.com nginx

```

```
docker run -d -e VIRTUAL_HOST=website2.com tomcat

```

Kudos We Did it.,..!!

Hopefully if you access website1.com and website2.com you’ll see the Nginx and Java apps respectively.

Any Issues faced in it can be commented below.
