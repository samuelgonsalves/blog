---
title: Building Alexa Skills using C# and AWS
---

In this article, I'll walk you through building custom Alexa skills using C# and AWS Lambdas.

### Basics

The Alexa developer console allows you to setup a skill and acts as the user facing piece of your Alexa skill. 
The skill invokes an AWS lambda to respond to requests.
The Alexa skill interacts with the AWS lambda function, passing information back and forth.

### Alexa Developer Console

In the Alexa developer console, you can create a new skill.
While in the console, it's useful to understand what Intents, Utterances and Slots are as they form the basis of your skill.
**Intents:** Simply put, intents are commands that Alexa can understand. Intents that you create are custom intents, and Amazon has built-in intents as well.
**Slots:** Slots are parts of an intent that get mapped to a slot value that can be used in code.
**Utterances:** These are phrases that you can use to invoke an intent.

Let's break this up with an example.

If you were building an app that gave you the weather forecast in your city, these could be examples of intents, slots & utterances:
Intent: *GetWeatherIntent*, which gets the weather
Slot: The city *San Francisco*, which you could use to call a weather API to get the weather info for San Francisco.
Utterance: "*Get me the weather for San Francisco*"

### C# AWS Lambda

Up until now, we've created the skill but there's no backend processing or response from Alexa. 
1. Create a new Lambda function in the AWS developer console. 
2. In Visual Studio, you will need to install the AWS Toolkit Extension. 
3. Create a new AWS Lambda project in Visual Studio.
4. Install the Alexa.NET nuget package.

Now we're finally ready to dig into some code.
In order for the Alexa skill to understand the intent, you defined the intent in the console, but Alexa still doesn't know how to respond to the intent.

**Get Alexa to respond to an intent**

In your Function.cs 

- Check whether Alexa sent a IntentRequest
```
skillRequest.GetRequestType() == typeof(IntentRequest)
```
- Check whether it was the GetWeatherIntent
```
intentRequest.IsGetWeather()

// Extension method
public static bool IsGetWeather(this IntentRequest intentRequest) => intentRequest.Intent.Name.Equals("GetWeatherIntent");
```
- Once we know it's the intent we expect, we can write code to process the request.
```
var slot = intentRequest.Intent.Slots["City"].SlotValue
```
- Before calling the external API to get weather info, let's just pause here and get something working. We can return a dummy response and let the user know that the temperature is 75 degrees.
```
return ResponseBuilder.Tell($"The temperature in {slot} is 75 degrees.");
```

**Deploy**
You can publish your AWS lambda function by right clicking your Visual Studio Lambda project and clicking Publish.
Note: Make sure your lambda function is deployed to the same region as the one you created in step 1 of the C# AWS Lambda section.

### Link the AWS Lambda to the Alexa skill

In the Alexa Developer Console, you'll want to specify the endpoint to the AWS lambda you created
The Alexa Developer Console allows you to test your Alexa skill by specifying the utterance.
You'll also need to give your skill an Invocation name.

