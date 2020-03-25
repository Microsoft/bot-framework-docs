---
title: Channels reference
description: Bot Framework Channels reference
keywords: channels reference, bot builder channels, bot framework channels
author: ivorb
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 12/03/2019
---

# Categorized activities by channel

The following tables show what events (Activities on the wire) can come from which channels.

This is the key for the tables:

Symbol              | Meaning
:------------------:|:------------------------------------------------
:white_check_mark:  |The Bot should expect to receive this Activity
:x:                 |The Bot should **never** expect to receive this Activity
:white_large_square:|Currently undetermined whether the Bot can receive this

Activities can meaningfully be split into separate categories. For each category we have a table of possible Activities.

Conversational
--------------

 \                      | Cortana            | Direct Line        | Direct Line (Web Chat) | Email              | Facebook           | GroupMe            | Kik                | Teams              | Slack              | Skype   | Skype Business | Telegram | Twilio  
:---------------------- | :-----:            | :----------------: | :--------------------: |:----:              | :------:           | :-----:            | :-----:            | :---:              | :---:              | :---:   | :------------: | :------: | :----:  
Message                 | :white_check_mark: | :white_check_mark: | :white_check_mark:     | :white_check_mark: | :white_check_mark: | :white_check_mark: | :white_check_mark: | :white_check_mark: | :white_check_mark: | :white_check_mark: | :white_check_mark:        | :white_check_mark:  | :white_check_mark: 
MessageReaction         | :x:                | :x:                | :x:                    | :x:                | :x:                | :x:                | :x:                | :white_check_mark: | :x:                | :x:      | :x:             | :x:       | :x:      

- All Channels send Message Activities.
- When using a Dialog, Message Activities should generally always be passed onto the Dialog.
- This is probably not true of the MessageReaction although they are very much part of the conversation.
- There are logically two types of MessageReaction: Added and Removed


> [!TIP]
> "Message Reactions" are things like a "thumbs up" on a previous comment. They can happen out of order, so they can be thought of as similar to buttons. This Activity is currently sent by the Teams Channel.  


Welcome
-------

 \                         | Cortana            | Direct Line        | Direct Line (Web Chat) | Email   | Facebook             | GroupMe | Kik     | Teams   | Slack   | Skype   | Skype Business | Telegram | Twilio  
:----------------------    | :-----:            | :---------:        | :--------------------: |:----:   | :------:             | :-----: | :-----: | :---:   | :---:   | :---:   | :------------: | :------: | :----:  
ConversationUpdate         | :white_check_mark: | :white_check_mark: | :white_check_mark:     | :x:     | :white_large_square: | :white_check_mark: | :white_check_mark: | :white_check_mark: | :white_check_mark: | :x:      | :x:             | :white_check_mark:  | :x:     
ContactRelationUpdate      | :x:                | :x:                | :x:                    | :x:     | :x:                  | :x:      | :x:      | :x:      | :x:      | :white_check_mark: | :white_check_mark:        | :x:       | :x:     

- It is common for Channels to send ConversationUpdate Activities.
- There are logically two types of MessageReaction: Added and Removed
- It is very tempting to assume bot "Welcome" behavior can be simply implemented by wiring up ConversationUpdate.Added and this sometimes works.
- However, this is a simplification, in order to produce a reliable "Welcome" behavior the bot implementation may also need to use state.


Application Extensibility
-------------------------

 \                      | Cortana            | Direct Line        | Direct Line (Web Chat) | Email   | Facebook | GroupMe | Kik     | Teams   | Slack   | Skype   | Skype Business | Telegram | Twilio  
:---------------------- | :-----:            | :---------:        | :--------------------: |:----:   | :------: | :-----: | :-----: | :---:   | :---:   | :---:   | :------------: | :------: | :----:  
Event.*                    | :white_large_square: | :white_check_mark: | :white_check_mark:    | :white_large_square: | :white_large_square:  | :white_large_square: | :white_large_square: | :white_large_square:  | :white_large_square:  | :white_large_square:  | :white_large_square:           | :white_large_square:     | :white_large_square:  
Event.CreateConversation   | :white_large_square: | :white_large_square: | :white_large_square:   | :white_large_square: | :white_large_square:  | :white_large_square: | :white_large_square: | :white_large_square:  | :white_large_square:  | :white_large_square:  | :white_large_square:           | :white_large_square:     | :white_large_square:  
Event.ContinueConversation | :white_large_square: | :white_large_square: | :white_large_square:   | :white_large_square: | :white_large_square:  | :white_large_square: | :white_large_square: | :white_large_square:  | :white_large_square:  | :white_large_square:  | :white_large_square:           | :white_large_square:     | :white_large_square:  

- Event Activities are an extensibility mechanism in Direct Line (_aka Web Chat_).
- An application that owns both the client and server may chose to tunnel their own events through the service using this Event Activity.


Microsoft Teams
------------------

 \                      | Cortana            | Direct Line        | Direct Line (Web Chat) | Email   | Facebook | GroupMe | Kik     | Teams   | Slack   | Skype   | Skype Business | Telegram | Twilio  
:---------------------- | :-----:            | :---------:        | :--------------------: |:----:   | :------: | :-----: | :-----: | :---:   | :---:   | :---:   | :------------: | :------: | :----:  
Invoke.TeamsVerification   | :x:      | :x:          | :x: | :x:   | :x:       | :x:      | :x:      | :white_check_mark: | :x:    | :x:    | :x:             | :x:       | :x:     
Invoke.ComposeResponse     | :x:      | :x:          | :x: | :x:   | :x:       | :x:      | :x:      | :white_check_mark: | :x:    | :x:    | :x:             | :x:       | :x:     

- Along with a number of the other typed Activities, Microsoft Teams defines a few Teams specific Invoke Activities.
- Invoke Activities are specific to an application and not something a client would define.
- There is no general notion of Invoke specific subtypes of the activity.
- Invoke is currently the only Activity that triggers a request-reply behavior on the bot.

This is very important: if using Dialogs for the OAuth Prompt to work the Invoke.TeamsVerification Activity must be forwarded to the Dialog.


Message Update
--------------

 \                      | Cortana            | Direct Line        | Direct Line (Web Chat) | Email   | Facebook | GroupMe | Kik     | Teams   | Slack   | Skype   | Skype Business | Telegram | Twilio  
:---------------------- | :-----:            | :---------:        | :--------------------: |:----:   | :------: | :-----: | :-----: | :---:   | :---:   | :---:   | :------------: | :------: | :----:  
MessageUpdate | :x:      | :x:          | :x:    | :x: | :x:      | :x:      | :x:  | :white_check_mark: | :white_large_square:  | :x:    | :x:             | :x:       | :x:     
MessageDelete | :x:      | :x:          | :x:    | :x: | :x:      | :x:      | :x:  | :white_check_mark: | :white_large_square:  | :x:    | :x:             | :x:       | :x:     

- Message Update is currently supported by Teams.


OAuth
-------

 \                      | Cortana            | Direct Line        | Direct Line (Web Chat) | Email   | Facebook | GroupMe | Kik     | Teams   | Slack   | Skype   | Skype Business | Telegram | Twilio  
:---------------------- | :-----:            | :---------:        | :--------------------: |:----:   | :------: | :-----: | :-----: | :---:   | :---:   | :---:   | :------------: | :------: | :----:  
Event.TokenResponse| :white_large_square:  | :white_check_mark:   | :white_check_mark:    | :x:    | :white_large_square: | :white_large_square: | :white_large_square: | :x:    | :white_large_square: | :white_large_square: | :white_large_square:       | :white_large_square: | :white_large_square: 

This is very important: if using Dialogs for the OAuth Prompt to work the Event.TokenResponse Activity must be forwarded to the Dialog.


Uncategorized 
-------------

 \                      | Cortana  | Direct Line        | Direct Line (Web Chat) | Email | Facebook | GroupMe | Kik     | Teams | Slack | Skype | Skype Business | Telegram | Twilio  
:---------------------- | :-----:  | :---------:        | :--------------------: |:----: | :------: | :-----: | :-----: | :---: | :---: | :---: | :------------: | :------: | :----:  
EndOfConversation       | :x:      | :white_check_mark: | :white_check_mark:     | :x:   | :x:      | :x:     | :x:     | :x:   | :x:   | :x:   | :x:            | :x:      | :x:     
InstallationUpdate      | :x:      | :white_check_mark: | :white_check_mark:     | :x:   | :x:      | :x:     | :x:     | :x:   | :x:   | :x:   | :x:            | :x:      | :x:     
Typing                  | :x:      | :white_check_mark: | :white_check_mark:     | :x:   | :x:      | :x:     | :x:     | :x:   | :x:   | :x:   | :x:            | :x:      | :x:     
Handoff                 | :x:      | :x:                | :x:                    | :x:   | :x:      | :x:     | :x:     | :x:   | :x:   | :x:   | :x:            | :x:      | :x:     


Out of Use (includes Payment specific Invoke)
---------------------------------------------
- DeleteUserData 
- Invoke.PaymentRequest  
- Invoke.Address
- Ping

---

## Summary of Activities supported per Channel

Cortana
-------
- Message
- ConversationUpdate
- _Event.TokenResponse_
- _EndOfConversation (when the window closes?)_

Direct Line
--------
- Message
- ConversationUpdate
- Event.TokenResponse
- Event.*
- _Event.CreateConversation_
- _Event.ContinueConversation_

Email
-----
- Message

Facebook
--------
- Message
- _Event.TokenResponse_

GroupMe
-------
- Message
- ConversationUpdate
- _Event.TokenResponse_

Kik
---
- Message
- ConversationUpdate
- _Event.TokenResponse_

Teams
-----
- Message
- ConversationUpdate
- MessageReaction
- MessageUpdate
- MessageDelete
- Invoke.TeamsVerification
- Invoke.ComposeResponse


Slack
-----
- Message
- ConversationUpdate
- _Event.TokenResponse_


Skype
-----
- Message
- ContactRelationUpdate
- _Event.TokenResponse_


Skype Business
--------------
- Message
- ContactRelationUpdate 
- _Event.TokenResponse_


Telegram
--------
- Message
- ConversationUpdate
- _Event.TokenResponse_


Twilio
------
- Message

## Summary Table All Activities to All Channels

 \                         | Cortana              | Direct Line          | Direct Line (Web Chat) | Email                | Facebook             | GroupMe | Kik     | Teams   | Slack   | Skype   | Skype Business | Telegram | Twilio  
:----------------------    | :-----:              | :---------:          | :--------------------: |:----:                | :------:             | :-----: | :-----: | :---:   | :---:   | :---:   | :------------: | :------: | :----:  
Message                    | :white_check_mark:   | :white_check_mark:   | :white_check_mark:     | :white_check_mark:   | :white_check_mark:   | :white_check_mark: | :white_check_mark: | :white_check_mark: | :white_check_mark: | :white_check_mark: | :white_check_mark:        | :white_check_mark:  | :white_check_mark: 
MessageReaction            | :x:                  | :x:                  | :x:                    | :x:                  | :x:                  | :x:      | :x:      | :white_check_mark: | :x:      | :x:      | :x:             | :x:       | :x:      
ConversationUpdate         | :white_check_mark:   | :white_check_mark:   | :white_check_mark:     | :x:                  | :white_large_square: | :white_check_mark: | :white_check_mark: | :white_check_mark: | :white_check_mark: | :x:      | :x:             | :white_check_mark:  | :x:     
ContactRelationUpdate      | :x:                  | :x:                  | :x:                    | :x:                  | :x:                  | :x:      | :x:      | :x:      | :x:      | :white_check_mark: | :white_check_mark:        | :x:       | :x:     
Event.*                    | :white_large_square: | :white_check_mark:   | :white_check_mark:     | :white_large_square: | :white_large_square: | :white_large_square: | :white_large_square: | :white_large_square:  | :white_large_square:  | :white_large_square:  | :white_large_square:           | :white_large_square:     | :white_large_square:  
Event.CreateConversation   | :white_large_square: | :white_large_square: | :white_large_square:   | :white_large_square: | :white_large_square: | :white_large_square: | :white_large_square: | :white_large_square:  | :white_large_square:  | :white_large_square:  | :white_large_square:           | :white_large_square:     | :white_large_square:  
Event.ContinueConversation | :white_large_square: | :white_large_square: | :white_large_square:   | :white_large_square: | :white_large_square: | :white_large_square: | :white_large_square: | :white_large_square:  | :white_large_square:  | :white_large_square:  | :white_large_square:           | :white_large_square:     | :white_large_square:  
Invoke.TeamsVerification   | :x:                  | :x:                  | :x:                    | :x:                  | :x:                  | :x:      | :x:      | :white_check_mark: | :x:    | :x:    | :x:             | :x:       | :x:     
Invoke.ComposeResponse     | :x:                  | :x:                  | :x:                    | :x:                  | :x:                  | :x:      | :x:      | :white_check_mark: | :x:    | :x:    | :x:             | :x:       | :x:     
MessageUpdate              | :x:                  | :x:                  | :x:                    | :x:                  | :x:                  | :x:      | :x:  | :white_check_mark: | :white_large_square:  | :x:    | :x:             | :x:       | :x:     
MessageDelete              | :x:                  | :x:                  | :x:                    | :x:                  | :x:                  | :x:      | :x:  | :white_check_mark: | :white_large_square:  | :x:    | :x:             | :x:       | :x:     
Event.TokenResponse        | :white_large_square: | :white_check_mark:   | :white_check_mark:     | :x:                  | :white_large_square: | :white_large_square: | :white_large_square: | :x:    | :white_large_square: | :white_large_square: | :white_large_square:       | :white_large_square: | :white_large_square: 
EndOfConversation          | :x:                  | :white_check_mark:   | :white_check_mark:     | :x:                  | :x:      | :x:     | :x:     | :x:   | :x:   | :x:   | :x:            | :x:      | :x:     
InstallationUpdate         | :x:                  | :white_check_mark:   | :white_check_mark:     | :x:                  | :x:      | :x:     | :x:     | :x:   | :x:   | :x:   | :x:            | :x:      | :x:     
Typing                     | :x:                  | :white_check_mark:   | :white_check_mark:     | :x:                  | :x:      | :x:     | :x:     | :x:   | :x:   | :x:   | :x:            | :x:      | :x:     
Handoff                    | :x:                  | :x:                  | :x:                    | :x:                  | :x:      | :x:     | :x:     | :x:   | :x:   | :x:   | :x:            | :x:      | :x:     

## Web Chat 
Web Chat will send:
- "message": with "text" and/or "attachments"
- "event": with "name" and "value" (as JSON/string)
- "typing": if the user set an option, namely "sendTypingIndicator"
Web Chat will not send "contactRelationUpdate". And Web Chat do not support "messageReaction", no one explicitly ask us to support this feature.

By default, Web Chat will render:
- "message": will render as either carousel or stacked, depends on the option in the activity
- "typing": will render for 5s and hide it, or until next activity come in
- "conversationUpdate": will hide
- "event": will hide
- Others: will show a warning box (we never see it in production)
You can modify this render pipeline to add, remove, or replace any custom render.

You can use Web Chat to send any activity type and payload, we neither document nor recommend this feature. 
You should use "event" activity instead.

## Action support by channel

The following table shows the maximum number of Suggested Actions and Card Actions that are supported in each channel.  The :x: indicates that the action is not supported at all in the specified channel.

| \                 | Cortana | Direct Line | Direct Line (Web Chat) | Email | Facebook | GroupMe |   Kik   | Line  | Teams | Slack | Skype | Skype Business | Telegram | Twilio | 
| :---------------- | :-----: | :---------: | :--------------------: |:----: | :------: | :-----: | :-----: | :---: | :---: | :---: | :---: | :------------: | :------: | :----: |
| Suggested Actions |   :x:   |     100     |          100           |  :x:  |    10    |   :x:   |   20    |  13   |  :x:  |  100  |  10   |      :x:       |    100   |   :x:  |  
| Card Actions      |   100   |     100     |          100           |  :x:  |     3    |   :x:   |   20    |  99   |   3   |  100  |   3   |      :x:       |    :x:   |   :x:  |  

For more information about the numbers shown in the above table, refer to channel support code listed [here](https://aka.ms/channelactions). 

For more information on _Suggested Actions_, refer to the [Use button for input](https://aka.ms/howto-add-buttons) article.

For more information on _Card Actions_, refer to the [Send a hero card](https://aka.ms/howto-add-media#send-a-hero-card) section of the _Add media to messages_ article.

## Card Support by Channel

| Channel | Adaptive Card | Animation Card | Audio Card | Hero Card | Receipt Card | Signin Card | Thumbnail Card | Video Card |
|:-------:|:-------------:|:--------------:|:----------:|:---------:|:------------:|:-----------:|:--------------:|:----------:|
|Cortana|✔|❌|❌|❌|✔|✔|✔|❌|
|Email|🔶|🌐|🌐|✔|✔|✔|✔|🌐|
|Facebook|⚠🔶|✔|❌|✔|✔|✔|✔|❌|
|GroupMe|🔶|🌐|🌐|🌐|🌐|🌐|🌐|🌐|
|Kik|🔶|✔|✔|❌|🌐|❌|✔|🌐|
|Line|⚠🔶|✔|🌐|✔|✔|✔|✔|🌐|
|Microsoft Teams|✔|❌|❌|✔|✔|✔|✔|❌|
|Skype|❌|✔|✔|✔|✔|✔|✔|✔|
|Slack|🔶|✔|🌐|🌐|✔|✔|🌐|🌐|
|Telegram|⚠🔶|✔|🌐|✔|✔|✔|✔|✔|
|Twilio|🔶|🌐|❌|🌐|🌐|🌐|🌐|❌|
|Web Chat|✔|✔|✔|✔|✔|✔|✔|✔|

*Note: The Direct Line channel technically supports all cards, but it's up to the client to implement them*

* ✔: Supported - Card is supported fully with the exception that some channels only support a subset of CardActions and/or may limit the number of actions allowed on each card.  Varies by channel.
* ⚠: Partial Support - Card may not be displayed at all if it contains inputs and/or buttons. Varies by channel.
* ❌: No Support
* 🔶: Card is Converted to Image
* 🌐: Card is Converted to Unformatted Text - Links may not be clickable, images may not display, and/or media may not be playable. Varies by channel.

These categories are intentionally broad and don't fully explain how every card feature is supported in each channel due to the many possible combinations of cards, features, and channels. Please use this table as a base reference, but test each of your cards in the desired channel(s).
