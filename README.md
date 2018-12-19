Meme Generator
---

This repository complements the Docker workshop given for REC employees.

# Prerequisites

We assume you have the following prerequisites met:

- A local Docker installation
- A working Docker Compose installation

# Setting up your services

To get this app up and running, you will need to define the following services inside a `docker-compose.yml` file.
See https://docs.docker.com/compose/gettingstarted/#step-3-define-services-in-a-compose-file for help with the syntax.

- [composer](https://hub.docker.com/_/composer/) - Makes sure all project dependencies are installed using Composer
- [nginx](https://hub.docker.com/_/nginx/) - Will handle all HTTP requests to your application
- [php](https://hub.docker.com/_/php/) - Will serve as an nginx backend for PHP files
- [redis](https://hub.docker.com/_/redis/) - Will cache all the data generated by our end users

# Setup hints

## composer

- The official Composer image uses the Composer binary as it's entrypoint.
All you got to do is pass the [command](https://docs.docker.com/compose/compose-file/#command) you want to run upon spinning up the container.
- Composer expects to find your composer.json and composer.lock files in the `/app` folder inside the container.
- To mount stuff inside your container (like your composer.json and composer.lock files), you will need to specify
one or more [volumes](https://docs.docker.com/compose/compose-file/#volumes) for your service.

## nginx

- Use the `nginx:1.15` image
- You will need to mount your vhost configuration as `/etc/nginx/conf.d/vhost.conf`
- You will need to forward any local port to port 80 in the container,
using the [ports](https://docs.docker.com/compose/compose-file/#ports) attribute.

## php

- Start with the base FPM image for now (`php:7.2-fpm`)
- In order for FPM to handle requests, it needs to have your PHP files inside the container,
mount it (using [volumes](https://docs.docker.com/compose/compose-file/#volumes) again)
under the same path as where you mounted it in the nginx container.
- We will need to customize it in our next step (await instructions)

# Extending images

You will soon notice that the PHP base image lacks some extensions that we need for our project.
The project has the following dependencies:

- The GD extension is required, for image manipulation
  - This extension also depends on the following libraries being installed:
    - libfreetype6-dev
    - libjpeg62-turbo-dev
    - libpng-dev
- The Redis extension is required, so PHP can communicate with our Redis instance

For examples, see the official Docker Hub page for the PHP images: https://hub.docker.com/_/php/

Make a Dockerfile that extends the FPM image by installing these extensions in a new custom image.
Don't forget to add a `build` attribute to your service in your `docker-compose.yml` file.
