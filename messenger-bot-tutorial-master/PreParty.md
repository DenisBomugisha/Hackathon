# 🤖 Create your own Facebook Messenger Bot - Setup

This tutorial is based on the following tutorials:

1. Facebook Quickstart https://developers.facebook.com/docs/messenger-platform/guides/quick-start
2. https://github.com/jw84/messenger-bot-tutorial Copyright (c) 2016 Jerry Wang (https://jw84.co)

## 🙌 Pre-Party, Get Set

Messenger Bots use a web server to process the messages they receive via Facebook Messenger. Do these steps to setup a basic Node.js server on Heroku, get ready to become a Facebook Developer, and then prepare to Bot Party!

### 1. If you are not already in GitHub, signup here: https://github.com/.

### 2. Build the Server.

1. Make sure you have access to a Linux-flavor command line.

2. Install the Heroku toolbelt from here https://toolbelt.heroku.com - this lets you launch, stop and monitor instances. Sign up for free at https://www.heroku.com if you don't have an account yet.

3. Install Node from here https://nodejs.org, this will be the server environment. Then open up Terminal or Command Line Prompt and make sure you've got the very most recent version of npm by installing it again:

    ```
    sudo npm install npm -g
    ```

4. Make a new folder called *myBot1* and cd to this directory. You will create a Node project for your server in this directory.

    Once you have cd'd there, run the following command. You may press Enter to accept the defaults.

    ```
    npm init
    ```

5. Install the additional Node dependencies: express is for the server, request is for sending out messages and body-parser is to process messages.

    ```
    npm install express request body-parser --save
    ```

6. Make a file called Procfile and copy this into it. This is so Heroku can know what file to run.

    ```
    web: node index.js
    ```

7. Create an index.js file in the folder and copy the following code into it. For this step, you will start by creating a basic Node.js server app. Later we will add the code to authenticate the Facebook Messenger Bot.

    ```javascript
    'use strict'

    const express = require('express')
    const bodyParser = require('body-parser')
    const request = require('request')
    const app = express()

    app.set('port', (process.env.PORT || 5000))

    // Process application/x-www-form-urlencoded
    app.use(bodyParser.urlencoded({extended: false}))

    // Process application/json
    app.use(bodyParser.json())

    app.get('/', function (req, res)
    {
        console.log("plain GET request");
        res.send('Hello World, I would like to be a chat bot')
    })

    // Spin up the server
    app.listen(app.get('port'), function()
    {   
        console.log('running on port', app.get('port'))
    })
    ```

8. Commit all the code with Git then create a new Heroku instance and push the code to the cloud.

    ```
    git init
    git add .
    git commit --message "hello world"
    heroku create
    git push heroku master
    ```
9. You should be able to "open your app" in Heroku to run it and see the Hello World message.

10. If you want to change your server code, re-deploy it with the following commands (put your own comments in for the commit):

    ```
    git add . 
    git commit --message "Your comment"
    git push heroku master
    ```

### 3. Become A Facebook Developer.

If you are not already a facebook Developer, become one: https://developers.facebook.com/

### 4. Get setup to Learn about Natural Language for Developers.

1. Create your login at https://wit.ai/

2. At the cool stuff, add something this: 

    ```
    #CMUHackathon2017 We will create an amazing Messenger Bot for the #messengerchallenge
    ```

3. Make a new folder called *myWit.aiBot1* and cd to this directory. 

4. In this new directory (cd there first), clone the demo code from the Wit.ai quickstart: https://wit.ai/docs/quickstart

    ```
    git clone https://github.com/wit-ai/node-wit
    cd node-wit
    npm install
    ```
 
    Note: we will go through it together, just clone the code for now.
