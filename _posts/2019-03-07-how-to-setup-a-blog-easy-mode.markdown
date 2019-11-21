---
layout: default
title: 'How-To: Setup a Blog Easy-Mode'
date: '2019-03-07 16:03:53'
tags:
- general
---

Once I decided to consolidate my learning into a blog format, I needed a platform. &nbsp;I briefly considered one of the one-stop-shop solutions (i.e. wordpress.com), but being the nerd that I am, I wanted more control. &nbsp;I settled with a Digital Ocean Droplet, Docker, and Ghost. &nbsp;Below, I detail the setup that I used to get up and running (I've also included some of the other solutions that I considered).

<!--kg-card-begin: hr-->
* * *
<!--kg-card-end: hr--><!--kg-card-begin: markdown-->

**One-Stop-Shops**

- [WordPress](https://wordpress.com/)
- [GhostPro](https://ghost.org/pricing/)
<!--kg-card-end: markdown--><!--kg-card-begin: hr-->
* * *
<!--kg-card-end: hr--><!--kg-card-begin: markdown-->
# Up and Running Easy-Peasy
<!--kg-card-end: markdown--><!--kg-card-begin: markdown-->

**Technologies Used**

- [Digital Ocean](https://www.digitalocean.com/)
- [Docker](https://www.docker.com/)
- [Nginx](https://www.nginx.com/)
  - [https://en.wikipedia.org/wiki/Nginx](https://en.wikipedia.org/wiki/Nginx)
  - [https://hub.docker.com/\_/nginx](https://hub.docker.com/_/nginx)
- [Let's Encrypt](https://letsencrypt.org/)
- [Ghost](https://ghost.org/)
  - [https://hub.docker.com/\_/ghost](https://hub.docker.com/_/ghost)
<!--kg-card-end: markdown--><!--kg-card-begin: markdown-->

**Summarized Setup**  
To start with, I created an account on digital ocean and setup a docker droplet. Prices vary, but I would stick with one of the cheaper options since you don't need a lot of horsepower for a new blog. Once the droplet finishes setup, I created a few things (we'll go over them in detail later on). Lastly, I used docker-compose to setup my infrastructure and then lets-encrypt tools for digital signatures.

_Created Files_

- Slightly Customized _DockerFile_ for nginx
- _Docker-Compose.yml_
- _Default.conf_ file for nginx settings
<!--kg-card-end: markdown--><!--kg-card-begin: markdown-->

**Digital Ocean and Droplet Setup**

1. Register an account at [https://www.digitalocean.com/](https://www.digitalocean.com/)
2. Create a new project and name it something related to your blog
3. Create a new droplet by clicking the "Create" button and selecting "Droplets"
4. Select "Docker 18.09" (Versioning will be different depending on the latest version)
5. Scroll down and select your plan. (Click "Show all plans" to view the cheap options)
6. Scroll down and choose a hostname
7. Click Create

At this point your droplet will spin up. You will get an email once it completes, but in my experience it took less than 2 minutes.

Check your email and make note of the _temporary_ root password

Once the droplet is up and running you should see it listed in your project. Console in by clicking on the droplet and clicking "Console" on the right side of the screen.

The console will pop up and you can log in as root with the root password that you noted above.

<!--kg-card-end: markdown--><!--kg-card-begin: markdown-->

**Initial Setup**  
At this point docker should already be installed, so all we need to do is prepare our files. For this setup, we will be using docker-compose to stand up a ghost container and an nginx container. For more information on docker, containers, and docker-compose visit the docker website. To learn how to do all of this, I utilized two blogs

- [Linode](https://www.linode.com/docs/websites/cms/how-to-install-ghost-cms-with-docker-compose-on-ubuntu-18-04/)
- [Alex Ellis](https://blog.alexellis.io/your-ghost-blog/)

_docker-compose.yml_  
Let's start with the docker-compose file. Open your favorite text editor and copy the lines below and save it as docker-compose.yml in your root directory.

    version: '2'
    services:
    
      blog:
        image: ghost:latest
        container_name: blog
        restart: always
        environment:
          url: https://example.com
        volumes:
          - /opt/ghost_content:/var/lib/ghost/content
      nginx:
        build:
          context: ./nginx
          dockerfile: Dockerfile
        restart: always
        depends_on:
          - blog
        ports:
          - "80:80"
          - "443:443"
        volumes:
           - /etc/letsencrypt/:/etc/letsencrypt/
           - /usr/share/nginx/html:/usr/share/nginx/html

_Directory Setup_  
Now we need to create some directories.

    mkdir -p ghost/content
    mkdir nginx

_nginx Setup_  
Jump into your nginx folder `cd nginx` and create a file called _Dockerfile_ and place the following inside

    FROM nginx:latest
    
    COPY default.conf /etc/nginx/conf.d

This creates a slightly modified nginx image by copying a custom configuration file into it (See below). Close your _Dockerfile_ and lets move on.

_default.conf_  
Create a new file called _default.conf_ in your current directory (nginx/). Copy the contents below into _default.conf_

    server {
      listen 80;
      listen [::]:80;
      server_name example.com;
      location /.well-known/acme-challenge/ { root /usr/share/nginx/html; allow all; }
      location / { return 301 https://$host$request_uri; }
    }
    
    server {
      listen 443 ssl http2;
      listen [::]:443 ssl http2;
      server_name example.com;
    
      ssl_protocols TLSv1.2;
      ssl_ciphers HIGH:!MEDIUM:!LOW:!aNULL:!NULL:!SHA;
      ssl_prefer_server_ciphers on;
      ssl_session_cache shared:SSL:10m;
    
      ssl_certificate /etc/letsencrypt/live/example.com/fullchain.pem;
      ssl_certificate_key /etc/letsencrypt/live/example.com/privkey.pem;
    
      location / {
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-Proto https;
        proxy_pass http://ghost:2368;
      }
    }

This configuration sets nginx up as a proxy which will handle SSL for us. Save the file and continue on.

<!--kg-card-end: markdown--><!--kg-card-begin: markdown-->

**Bring Up The Infrastructure**  
Go back to the dir with your docker-compose.yml `cd ..`  
We'll run docker-compose with the -d switch to detach and let it run in the background

    docker-compose up -d

--Note: If you need to make changes and re-run this then you will need to add the _--build_ switch to rebuild the custom nginx image

    docker-compose up -d --build

<!--kg-card-end: markdown--><!--kg-card-begin: markdown-->

**Setup Lets Encrypt**  
We'll be using Let's Encrypt as our Certificate Authority. To do that, we'll make use of a few tools to simplify the process. I used Digital Ocean's guide to help for this part.

- [Digital Ocean](https://www.digitalocean.com/community/tutorials/how-to-secure-nginx-with-let-s-encrypt-on-ubuntu-16-04)

First, install some dependencies.

    add-apt-repository ppa:certbot/certbot
    apt update
    install python-certbot-nginx

Next, make sure that the firewall will allow our nginx container to talk to the internet.  
Check

    ufw status
    ufw allow 80
    ufw allow 443

Now its time to setup our certs

    certbot --nginx -d example.com -d www.example.com

When presented with the option, select _1: No Redirect.._

<!--kg-card-end: markdown--><!--kg-card-begin: markdown-->

**Clean-Up**  
Restart the infrastructure.

    docker-compose down
    docker-compose up -d

Check the nginx logs to make certain that there aren't any errors

    docker logs proxy

Remember from above that we named our nginx container _proxy_

Visit your site and check for Ghost!

To complete the setup visit the admin portal by appending _/admin_ to your url.

<!--kg-card-end: markdown-->
