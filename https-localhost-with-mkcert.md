Title: HTTPS localhost with mkcert
Date: 2023-09-25
Tags: mkcert, progressive web app

I am currently building progressive web apps. In order for a PWA to be installed on a device, the PWA needs to be served on HTTPS. I should be able to easily run it on AWS and then install on my devices, but I found an easy way to run it on localhost with https locally.

Steps:

- Install mkcert (https://github.com/FiloSottile/mkcert)
- Install http-server (https://github.com/http-party/http-server)
- mkcert -install
- cd to project directory
- mkcert localhost
- http-server -S -C localhost.pem -K localhost-key.pem 
- Go to https://localhost:8080/

We can now download the PWA!
