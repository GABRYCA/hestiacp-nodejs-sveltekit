# Steps for SvelteKit

### Locally before upload:

Before cloning this repository, you should use the `@sveltejs/adapter-node`, to do this run:
* `npm install -D @sveltejs/adapter-node`
* Now edit your `svelte.config.js` adapter to this:
  ```
  import adapter from '@sveltejs/adapter-node';
  ```
#### Build your project:
* `npm run build`
#### Check if preview works:
* `npm run preview`

For good measure I also copied to the `build` folder before uploading it, the `package.json`, just in case you need to run an `npm install` on your server.

### On your server (with HestiaCP):

* Git Clone this repository
* Run `install.sh` to install PM2 and move automatically the template files.
* Upload your app with ssh to this path: `/home/<user>/web/<domain>/nodeapp/`.
* Upload your `package.json` to `/home/<user>/web/<domain>/nodeapp/`.
* Run `npm install` inside `/home/<user>/web/<domain>/nodeapp/`.
* Run `index.js` inside your `/home/<user>/web/<domain>/nodeapp/` with `pm2 start index.js --name sveltekit` (You can change sveltekit to whatever you want)
* Go back to your HestiaCP panel -> WEB -> Pick a domain and open settings -> Advanced Options -> Change Proxy Template to `NodeJS`
* Done, everything should be up and running

#### If you want to see if your website is running, run `pm2 list` and check if there's the running process.
#### If you want to stop your website, run `pm2 stop sveltekit` (Replace sveltekit with whatever name you choose).
#### If you want to see logs of pm2, run `pm2 logs sveltekit` (Replace sveltekit with whatever name you choose) and to close them use CTRL-Z.
#### If you want to delete the website process, run `pm2 delete sveltekit` (Replace sveltekit with whatever name you choose).

## Demo
[Demo](https://anonymousgca.eu)

# HestiaCP with Node.JS support.

With this repo you can run multiples NodeJS Apps at same time.
This template reads the `.env` file, `.nvm` for Node version, and `package.json` to get info and automatically install modules.

![HestiaCP](https://logico.com.ar/img/2019/04/21/vestacp_proxy_setup.png)

## Spanish Explication
https://logico.ar/blog/2019/04/20/soporte-para-aplicaciones-nodejs-en-vestacp

## Installation

***Auto "With Script":***
* First download this Git in your `home` directory and run `chmod +x ./install.sh`, `sudo ./install.sh`. Script auto install `NVM` and `PM2` 

***Manually:***
* Install PM2 `npm i pm2@latest -g`
* Install NVM see [https://nvm.sh]
* Upload `NodeJS.tpl`, `NodeJS.stpl`, `NodeJS.sh` to `/usr/local/hestia/data/templates/web/nginx/`

## WHERE DEPLOY MY APP?

In HestiaCP the user must be a enabled `bash` ssh, if set as `none` this is a cause to stuck.
Upload your app with ssh on this path: `/home/<user>/web/<domain>/nodeapp/`.
Here you can use files `.env` with Enviroment variables, `.nvm` or `.node-version` for specify NodeJS version.

In your `package.json` you can specify your index file with 
```js
    ...
    "main": "index.js",
    ...
```

In your `index.js` you need run the express server or http with this parameters:
```js
    ...
    //PORT IS PARSET WITH ENV VARIABLE FOR PROPER RUN IF YOU DON'T SET YOUR APP IS CRASH OR NOT RUN!!
    app.listen(process.env.PORT); 
    ...
```
When your app is launched script create UNIX socket file in `/home/<user>/web/<domain>/nodeapp/app.sock` is set with 0777 permisses for allow access to NGINX.

## DEBUG

Each launch will be copied two logs files `<domain-name>-error.log` and `<domain-name>-out.log` to the `nodeapp` folder.

## BLANK PAGES "NODE DOWN"

When your app is down create simply html file in public folder `/home/<user>/web/<domain>/public_app/index.html`.

### FAQ

* How can restart app?: Select Proxy template as `default` click save button, select NodeJs template option and click save again.
* How I can check if my app is running?: in nodeapp folder run `ls -l` and see if exist `app.sock` file, if yes, run `runuser -l <user> -c "pm2 list"` or `runuser -l <user> -c "pm2 monit"`
* My app is not running, why?: Check setting of user account and set `bash` ssh. 

### Samples

For debug propose you can run script with this arguments `NodeJS.sh <user> <domain> 127.0.0.1 /home` full command: `/usr/local/hestia/data/templates/web/nginx/NodeJS.sh admin default 127.0.0.1 /home`

* ***Remove all instances***: `runuser -l <user> -c "pm2 del all"` for admin `runuser -l admin -c "pm2 del all"`
* ***List all instances***: `runuser -l <user> -c "pm2 list"` for admin `runuser -l admin -c "pm2 list"`
* ***Show monitor of instances***: `runuser -l <user> -c "pm2 monit"` for admin `runuser -l admin -c "pm2 monit"`

#### Documentation

Pm2: [https://pm2.keymetrics.io/docs/usage/quick-start/]
Info about NGINX Proxy [https://serverfault.com/questions/316157/how-do-i-configure-nginx-proxy-pass-node-js-http-server-via-unix-socket]

