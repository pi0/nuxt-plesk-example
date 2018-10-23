# Nuxt on Plesk  Onyx

You have to first download and set up a Plesk server. It is downloadable from [this](https://page.plesk.com/plesk-onyx-free-download) link. This guide is using the Docker-based method. If you prefer to or already have a Plesk server, you may have to take a look at [here](https://www.plesk.com/blog/product-technology/node-js-plesk-onyx) for Node extension installing instructions.

## Setup plesk

Run Plesk server:

```
docker run -d -it -p 80:80 -p 443:443 -p 8880:8880 -p 8443:8443 -p 8447:8447 plesk/plesk
```

New head to http://localhost:8880 for admin panel.

Wait until server is setup.

![image](https://user-images.githubusercontent.com/5158436/47363690-3ed78780-d6e4-11e8-8ae9-76dc6b3c29da.png)

Now you can log in with default conditionals `admin`/`changeme`.

![image](https://user-images.githubusercontent.com/5158436/47363759-71818000-d6e4-11e8-839b-24ad37a11cab.png)

## Add a new domain

After logging in, switch to "Power User View" (Bottom left)


Click on "Add a Domain" to define a new domain:

In this tutorial, I use `nuxt.127.0.0.1.xip.io` as Plesk is running on my laptop.

If you have a version control, you may prefer to enable "Git support". For this demo I use a simple hello-world repository:

> https://github.com/pi0/nuxt-plesk-demo.git


Now from "Websites and Domains" tab, open "Node.js":

## Enable and set Node params

Set this params:

Node.js Version: `8.9.3` (Or later)
Document Root: `/httpdocs/static`
Application Root: `/httpdocs`

Click on "Enable Node.js"

Click on "NPM Install"

![image](https://user-images.githubusercontent.com/5158436/47364375-020c9000-d6e6-11e8-861a-15388e3738bd.png)

 ## Development

![image](https://user-images.githubusercontent.com/5158436/47366947-8f9eae80-d6eb-11e8-9294-fc08c97f669b.png)

Click on restart app and head to http://nuxt.127.0.0.1.xip.io! Voila.

![image](https://user-images.githubusercontent.com/5158436/47367007-a8a75f80-d6eb-11e8-9d9c-d0a1610d0345.png)

## Production

Set params like below:

![image](https://user-images.githubusercontent.com/5158436/47367570-cc1eda00-d6ec-11e8-84e5-a1bcab4c3812.png)

You should have a file called `server.js` in the root of your project like this: 

```js
const express = require('express');
const { Nuxt, Builder } = require('nuxt');

const config = require('./nuxt.config.js');

// Create new express app
const app = express();

// Listen to port 3000 or PORT env if provided
app.listen(process.env.PORT || 3000);

// Create instance of nuxt
const nuxt = new Nuxt(config);

// Add nuxt middleware
app.use(nuxt.render);
```

If you prefer to automatically build upen server start (Not recommanded), then appennd line(s) below:

```js
new Builder(nuxt).build()
```

If you don't prefer to build upon start, use "Run Script" tool to manually trigger a build:

> build --scripts-prepend-node-path

![image](https://user-images.githubusercontent.com/5158436/47367448-8bbf5c00-d6ec-11e8-8e3a-2a81c8ee01ec.png)


If you prefer to automatically build upon server start (Not recommended), then append line(s) below:

```js
new Builder(nuxt).build().catch(err => {
  console.error(err);
  process.exit(1);
});
```

## Windows / IISNode

Windows deployment is a little bit different by design with Plesk.

Go into "Websites & Domains" > "Hosting Settings" and ensure that "Document Root" is set to `httpdocs` (Or where you clone your project. Not the ~~`httpdocs/dist`~~ folder)

![image](https://user-images.githubusercontent.com/5158436/47394041-4b80cd80-d72e-11e8-93b9-27e744b7d82c.png)

Go into Node.js settings and ensure that **BOTH** "Document Root" and "Application Root" are pointing to `/httpdocs`. They should be the same.

![image](https://user-images.githubusercontent.com/5158436/47394114-86830100-d72e-11e8-8fa7-d9ae30df06e6.png)

Using file manager's "Change Permissions" ensure that "Application pool group" has all permissions on `/httpdocs` directory. This is required for when we run build inside `server.js`.

![image](https://user-images.githubusercontent.com/5158436/47394201-f4c7c380-d72e-11e8-8aed-378ae7cae375.png)

Create a file called `web.config` inside `/httpdocs`:

```xml
<configuration>
  <system.webServer>
    <rewrite>
      <rules>
		    <rule name="server">
			    <match url="/*" />
			    <action type="Rewrite" url="server.js" />
		    </rule>
      </rules>
    </rewrite>
  </system.webServer>
</configuration>
```

Plesk node windows support is not so great. Please check `/logs/iisnode` for logs. 

## Nuxt.js

For detailed explanation on how Nuxt.js things work, checkout [Nuxt.js docs](https://nuxtjs.org).
