---
layout: post
title: Create Slack Bot with AWS Serverless
subtitle: Create Slack Bot with API gateway and Lambda
tags: [aws, slack, API Gateway, Lambda]
---

A bot is a nifty way to run code and automate tasks. In Slack, a bot is controlled programmatically via a bot user token that can access one or more of Slack’s APIs. 
We will create simple bot that receive and process some simple task by entering command into channel
The way the chat bot will work is the following:
- User enter message into channel, ex. "@abot say hello"
- Slack bot will send the request with this message to API Gateway 
- An AWS Lambda function processes the payload of the request and takes an appropriate action,then reply request to the Slack API 
We will need permission to create app on Slack, so on this Post we will create us self slack workspace 

### Create IAM Role to enable write log from Lambda
From AWS Console manager, go to `Services` → `IAM`   
Create Role named **AllowLambdaWriteLog** that allow Lambda can able to write log with below policies   
```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "logs:CreateLogStream",
                "logs:PutLogEvents"
            ],
            "Resource": "arn:aws:logs:*:*:*"
        },
        {
            "Effect": "Allow",
            "Action": "logs:CreateLogGroup",
            "Resource": "*"
        }
    ]
}
```

### Create AWS Lambda 
From AWS Console manager, go to `Services` → `Lambda`   
- **Name** : TestSlackBot
- **Runtime** : Python 3.6
- **Execution Role** : use existing role `AllowLambdaWriteLog`
<img src="./img/20190531/image2019-5-30_14-7-35.png" alt="Create new lambda function">
Then click `Create Function` 

### Implement Lambda function 
First, declare and import some resources need to use 
```python
"""
Slack chat-bot Lambda handler.
"""
import os
import logging
import urllib.request
import json
import re

# Use logger for debug and logging
logger = logging.getLogger()
logger.setLevel(logging.INFO)

# Grab the Bot OAuth token from the environment.
BOT_TOKEN = os.environ["BOT_TOKEN"]

# Define the URL of the targeted Slack API resource.
SLACK_URL = "https://slack.com/api/chat.postMessage"

# Main handler
def lambda_handler(event, context):
    # Everything went fine.
    return "200 OK"
```

When setup Slack API resource, Slack require to verify the API URL by the challenger request.   
We need add below code to reply this challenger request
```python 
def lambda_handler(event, context):
    # Required for valid challenge request from slack api
    if "challenge" in event:
        return event["challenge"]
```

Continue, detach command from the message from the user send to bot
```python
# Main handler
def lambda_handler(event, context):
    .... 
    
    # Grab the Slack event data.
    slack_event = event["event"]
    # Check message has been sent from bot
    if "bot_id" in slack_event:
        logging.warn("Ignore bot event")
        return "200 OK"

    # Detach command from the message that user sent to bot,
    text = slack_event["text"]
    matched = detach_command(text)
    if matched == False: 
        logging.warn("Not matched message. Ignore bot event")
        return "200 OK"
        
    # Everything went fine.
    return "200 OK"

# Function : Detach command from message    
def detach_command(message):
    # Match message format : <@bot_id> <command> [option message]
    matched = re.match("<@(\w+)>[\s](\w+)(.*)", message);
    if matched: 
        # matched data must be {'bot_id', 'command', 'optional'}
        return matched.groups()
    return False
```

Now, we will process the command that detached from message
```python
def lambda_handler(event, context):
    ......
    
    # process command
    bot_id  = matched[0].strip();
    command = matched[1].strip();
    optional_data = matched[2].strip();
    if bot_id not in event["authed_users"]:
        logging.warn("Not matched command. Ignore bot event")
        return "200 OK"
        
    result_msg = process_cmd(command, optional_data);
    if result_msg == "":
        logging.warn("Not matched command. Ignore bot event")
        return "200 OK"
        
        
    # Everything went fine.
    return "200 OK"

# Function : Detach command from message
def detach_command(message):
    ....
    
# Function : process the command 
def process_cmd(command, optional_data):
    if command == "say":
        return say(optional_data)
    elif command == "calculate":
        return calculate(optional_data)
    return "";
    
# Function : Say something
def say(message):
    if message: 
        return message
    return "Nothing to say"

# Function : Calculate from calculating string
def calculate(calculating):
    if calculating:
        try:
            result = eval(calculating)
            return '{} = {}'.format(calculating, result)
        except:
            return "I'm confused with this calculating !!"
    return "Please input calculating"
```

Finally, reponse the result to Slack 
```python
def lambda_handler(event, context):
    ....
    
    # Get the ID of the channel where the message was posted.
    channel_id = slack_event["channel"]
    # Send result message to slack channel 
    send_data_to_slack_api(channel_id, result_msg)

    # Everything went fine.
    return "200 OK"
    
....

# Function : send message to slack api    
def send_data_to_slack_api(channel_id, message):
    data = urllib.parse.urlencode(
        (
            ("token", BOT_TOKEN),
            ("channel", channel_id),
            ("text", message)
        )
    )
    data = data.encode("utf-8")
    # Construct the HTTP request that will be sent to the Slack API.
    request = urllib.request.Request(
        SLACK_URL,
        data=data,
        method="POST"
    )
    # Add a header mentioning that the text is URL-encoded.
    request.add_header(
        "Content-Type",
        "application/x-www-form-urlencoded"
    )

    # Fire off the request!
    urllib.request.urlopen(request).read()
```

The full code is here [Gis](https://gist.github.com/anhnt1208/6c4b281ee07978634648248c5bf529d1)

### Create API Gateway 
Go to `API Gateway` service from AWS Console manager.   
Create new API with follow info
<img src="./img/20190531/image2019-5-30_13-42-54.png" alt="Create new API Gateway">

At API Resources page, click `Action` → `Create Resource`   
We don’t need to enable `CORS` as the client will not be a browser   
<img src="./img/20190531/image2019-5-30_13-48-59.png" alt="Add new resource">
Click `Create Resource`

### Add trigger from API Gateway to Lambda
At AWS Console manager, go to `Service` → `API Gateway` 
Add `POST` method to resource `event-handler`
<img src="./img/20190531/image2019-5-30_15-48-32.png" alt="Add method POST">
Click `Save` and the pop-up will notify to add permission to Lambda Function, click `OK`   
After created the `POST` method will be
<img src="./img/20190531/image2019-5-30_15-52-30.png" alt="created method">

### Deploy API Gateway
At API Gateway screen, click `Action` → `Deploy API` , it will show pop-up as below
<img src="./img/20190531/image2019-5-30_15-54-29.png" alt="Deploy API">
Click `Deploy`   
After deployed API, we will have the API request URL for event-handler 
<img src="./img/20190531/image2019-5-30_16-0-45.png" alt="Deployed result">
We will use this URL from `event-handler`, not from root `/` path   
Copy this URL for setting up Slack Bot   
Go to AWS Lambda, check it has already triggered with API Gateway   
<img src="./img/20190531/image2019-5-30_16-48-39.png" alt="Lambda trigger">
<img src="./img/20190531/image2019-5-31_8-54-46.png" alt="API Gateway">

### Create Slack Bot
For creating bot, we need permission to create app on Slack   
Or can create new self Slack Team at [here](https://slack.com/create)   
At Main menu of Slack , click `Administration` → `Manager Apps`   
<img src="./img/20190531/image2019-5-30_16-13-3.png" alt="Manager Apps">
Click `Build` → `Your Apps`
<img src="./img/20190531/image2019-5-30_16-16-23.png" alt="Your Apps">
Click `Create New App`
<img src="./img/20190531/image2019-5-30_16-18-31.png" alt="Create new App">
App Name can be what ever you want   
Click `Create App`   

### Setup Slack Bot
Go to `Bot Users` on left menu to Add Bot User   
Click `Add Bot User` 
<img src="./img/20190531/image2019-5-30_16-29-46.png" alt="Add Bot User">





