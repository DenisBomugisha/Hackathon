# 🤖 Creating your own Facebook Messenger Bot - Tutorial

This tutorial is based on the Facebook Quickstart https://developers.facebook.com/docs/messenger-platform/guides/quick-start and one from  Jerry Wang (https://jw84.co) (c) 2106: https://github.com/jw84/messenger-bot-tutorial.

If you have not already done so, please finish the setup outlined in the [PreParty.md](https://github.com/amuhebwa/messenger-bot-tutorial/blob/master/PreParty.md) file.

## 🙌  Let's Bot Party 
### We will start our Bot Party by going through the rest of these steps together.

### *Setup the Validation in your Server*

1. Open two command-line prompts and in each, cd to the web server diretory that you created during Setup.

2. In one window, run this command so you can see your web server app logs:

    ```
    heroku logs --source app  -t
    ```

3. Use your other window to edit your index.js file and commit/push your changes. Try changing the "Hello World" text. Remember these steps to deploy your changes:

    ```
    git add .
    git commit --message "Your comment"
    git push heroku master
    ```

4. Refresh your Heroku app and you will see the messages that are logged to the console.

5. Copy the index.js.v2 code into index.js and re-deploy.

6. Refresh your Heroku app to verify that it still works correctly.

7. NOTE: In real-life, you should change the Verify Code to use your own token instead of YOUR_TOKEN, and re-deploy, but for this tutorial it's ok to leave it.


### *Setup the Facebook App*

1. Create a new Facebook Page:
    On your Home page, along the top is a drop down. Select it and you will see "Create Page".

    ![Alt text](/images/CreatePage1.png)

    ![Alt text](/images/CreatePage2.png)

2. Create a new Facebook App: https://developers.facebook.com/apps/. Make it a Messenger App.

    Note: If you are not already a Facebook Developer, you will need to become one.

3. In the App, go to Messenger tab, and under Settings, scroll down and click Setup Webhook.  First, put in the URI of your Heroku server.  Be sure to add /webhook to the end of your Server URI. Go ahead and check all the subscription fields. 

    ![Alt text](/images/WebHook.png)

5. Start with "xyz" for your Verify Token, see that it fails, and watch your Heroku log.  Then put in the YOUR_TOKEN (it needs to match your the code in index.js). Verify and Save.

6. Scroll UP to Token Generation to get a Page Access Token. Select the page that you created earlier and a great-big number will be generated.

    ![Alt text](/images/PageAccessToken.png)

    The Page Access Token is not persisted on the page, so copy/paste and save it somewhere (perhaps in a Draft email).

    We will use it in 2 places: once now to trigger the Facebook App to send messages to the Bot and once later for setting up access to the Facebook API for your Bot.

7. Go back to Terminal and type in this command to trigger the Facebook app to send messages. Instead of \<PAGE_ACCESS_TOKEN\>, use the Page Access Token you requested and saved in the previous step.

    ```bash
    curl -X POST "https://graph.facebook.com/v2.6/me/subscribed_apps?access_token=<PAGE_ACCESS_TOKEN>"
    ```

### *Setup the Bot*

Now that Facebook and Heroku can talk to each other we can start coding the Bot.

1. So far our Server has been using GET, both for the Hello World server and the Facebook App Verification. Facebook Messenger will use POST to send requests to your Bot, so we will write code in our Server to handle the POST requests that come from Facebook Messenger to your Bot.

2. Next, replace index.js with index.js.v3 which has the start of code for our Bot to handle these requests.

3. This code adds the POST request handler to simply log the incoming events and messages. 

    ```javascript
    // Process Facebook Messenger Requests

app.post('/webhook', function (req, res)
{
    var data = req.body;

    // Make sure this is a page subscription
    if (data.object === 'page')
    {
        // Iterate over each entry - there may be multiple if batched
        data.entry.forEach(function(entry)
        {
        var pageID = entry.id;
        var timestamp = entry.time;

        // Iterate over each messaging event
        entry.messaging.forEach(function(event)
        {
            if (event.message)
           {
                if (event.message.is_echo)
                      console.log("Bot received message written event");
                else
                      console.log("Bot received message " + event.message.text);
            }
            else  if (event.delivery)
              console.log("Bot received delivery event");
            else  if (event.read)
              console.log("Bot received message-was-read event");
            else  if (event.postback)
                doPostback(event);
            else
              console.log("Bot received unknown EVENT: ");
          });
        });
    }
    else
    {
      console.log("Bot received unknown OBJECT (not page): ", data.object);
    }

    // All good, sent response status 200

    res.sendStatus(200)
});
    ```

4. Copy the functs.js file to your *myBot1* directory.

5. Commit the code again and push to Heroku

    ```
    git add .
    git commit -m 'updated the bot to speak'
    git push heroku master
    ```
    
6. Go to your Facebook Page and Select Menu, then View as Page Visitor.

    ![Alt text](/images/VisitPage.png)

7. Select Send Message to start a Messenger conversation with your page.

8. Return to your Facebook page and send your Page a message from your user Messenger window.

9. Note: you as a *User* are using Facebook Messenger to have a conversation with your *Page*, which you own.

10. Watch your Heroku logs!

### *Let's get the Bot Talking*

1. Edit your copy of functs.js. This file has the Page Access Token in it  - you will need to change it to yours.

    Find this line:

    ```
    const PAGE_ACCESS_TOKEN = "EAAKTalAQ2eoBEAXiFuM..."
    ```

    Copy and paste your Page Access Token between the quotes.
    
    **Optional, but recommended**: keep your app secrets out of version control!
    - On Heroku, its easy to create dynamic runtime variables (known as [config vars](https://devcenter.heroku.com/articles/config-vars)). This can be done in the Heroku dashboard UI for your app **or** from the command line:
    ```bash
    heroku config:set FB_PAGE_ACCESS_TOKEN=fake-access-token-dhsa09uji4mlkasdfsd
    
    # view
    heroku config
    ```

    - For local development: create an [environmental variable](https://en.wikipedia.org/wiki/Environment_variable) in your current session or add to your shell config file.
    ```bash
    # create env variable for current shell session
    export FB_PAGE_ACCESS_TOKEN=fake-access-token-dhsa09uji4mlkasdfsd
    
    # alternatively, you can add this line to your shell config
    # export FB_PAGE_ACCESS_TOKEN=fake-access-token-dhsa09uji4mlkasdfsd
    
    echo $FB_PAGE_ACCESS_TOKEN
    ```
    
    - `config var` access at runtime
    ``` javascript
    const token = process.env.FB_PAGE_ACCESS_TOKEN
    ```

2. Update your index.js so that instead of simply logging the incoming message, it calls the function to process it.

    You should change this line (it should be line 78):
    ```javascript
    console.log("Bot received message " + event.message.text);
    ```
    to this:
    ```javascript
    functs.receivedMessage(event);
    ```

3. Look at the receivedMessage() code to see what it is doing.

4. Talk to your Page some more and watch your Heroku log.

5. Remember, you, as a *User* are using Facebook Messenger to have a conversation with your *Page*. And your *Bot* (the Webserver) is receiving those messages through your *App* and your *Bot* is now also able to respond.

6. What happens when you (the *User*) send the message **Knock Knock**?

##  :sweat_smile: You Rock!!

## ⚙ Customize what the bot says

### *Start with Custom Responses*
1. This example shows coding a simple rule - if the User send **Knock Knock**, the Bot will respond with **Who's there?**

2. Hopefully you can easily see where you might hook in more complicated rule-based processing to make your Bot pretty smart at responding in an autonomous way.

3. The Send API reference https://developers.facebook.com/docs/messenger-platform/send-api-reference#message is a good place to start for more details.

### *Send a Structured Message*

Facebook Messenger can send messages structured as cards or buttons. 

1. In the functs.js file, you will see a stub called *sendGenericMessage()*. This is called when the User types the word structured (see line 33 in functs.js).

2. Try typing it in your Messenger window and you will see this:
    ```
    Stub: send generic (templated) message
    ```

3. Next, we will write the code to send a response back as two cards.

4. Copy the code below to replace genericMessage() in index.js so it will send a structured message back as two cards.

    Replace this:
    ```javascript
    sendGenericMessage: function(toId)
    {
      console.log("Stub: send generic (templated) message");
    }
    ```
    with this code:
    ```javascript
    sendGenericMessage: function(toId)
    {
      console.log("Sending message with 2 Cards to id: " + toId)
      var messageData = {
        recipient: {
          id: toId
        },
        message: {
          attachment: {
            type: "template",
            payload: {
              template_type: "generic",
              elements: [
                {
                title: "CMU-Africa",
                subtitle: "In Kigali",
                item_url: "http://www.cmu.edu/africa/about-cmur/index.html",
                image_url: "http://www.cmu.edu/africa/files/images/bios/About%20CMU%20Rwanda.jpg",
                buttons:
                [{
                  type: "web_url",
                  url: "http://www.cmu.edu/africa/",
                  title: "CMU Africa"
                },{
                  type: "postback",
                  title: "Help Me Apply",
                  payload: "help me apply",
                }]
                },{
                title: "Bot Party",
                subtitle: "Bots for Messenger Challenge",
                item_url: "https://messengerchallenge.splashthat.com/",
                image_url: "https://d24wuq6o951i2g.cloudfront.net/img/events/id/272/2724336/assets/d0c.BotforMess_Splash.png",
                buttons:
                [{
                  type: "web_url",
                  url: "https://messengerchallenge.splashthat.com/",
                  title: "Facebook Messenger Challenge"
                },{
                  type: "postback",
                  title: "Please do something for me",
                  payload: "Payload for Please do something for me bubble",
                }],
                }]
            }
          }
        }
      };
      callSendAPI(messageData);
},


    ```

5. Re-deploy your Bot (Git add, commit, and push to Heroku again).

6. Look through the code to see what it is doing.

7. The Templates Send API reference https://developers.facebook.com/docs/messenger-platform/send-api-reference/templates is a good place to start for more details.

### *Act on what the user messages*

1. What happens when the user clicks on a message button or card? It sends the *payload* to the Bot. Give it a try.

2. Click on *Help Me Apply* and watch your log. You will see this:
    ```
    Stub: process the postback
    ```

3. Next, let's update the Bot code to process the postback. Your bot will receive the payload associated with the button, and can decide how to process it. For now we will just echo it to the User.

    Replace this:
    ```javascript
    doPostback: function(event)
    {
      console.log("Stub: process the postback");
    },
    ```
    with this code:
    ```javascript
    doPostback: function(event)
    {
      var senderID = event.sender.id;
      var recipientID = event.recipient.id;
      var timeOfPostback = event.timestamp;

      // The 'payload' param is a developer-defined field which is set in a postback
      // button for Structured Messages.
      var payload = event.postback.payload;
      var myResponse = payload.replace("me", "you");

      console.log("Received postback for user %d and page %d with payload '%s' " +
        "at %d", senderID, recipientID, payload, timeOfPostback);

      // When a postback is called, we'll send a message back to the sender to
      // let them know it was successful

      this.sendTextMessage(senderID, "I will gladly " + myResponse);
    },
    ```

5. Re-deploy your Bot (Git add, commit, and push to Heroku again).

6. Look through the code to see what it is doing.

##  :grinning: There you have it - a basic Messenger Bot!
