Some channels provide features that cannot be implemented by using only message text and attachments. To implement channel-specific functionality, you can pass native metadata to a channel in the activity object's _channel data_ property. For example, your bot can use the channel data property to instruct Telegram to send a sticker or to instruct Office365 to send an email.

This article describes how to use a message activity's channel data property to implement this channel-specific functionality:

| Channel  | Functionality                                                                  |
| -------- | ------------------------------------------------------------------------------ |
| Email    | Send and receive an email that contains body, subject, and importance metadata |
| Slack    | Send full fidelity Slack messages                                              |
| Facebook | Send Facebook notifications natively                                           |
| Telegram | Perform Telegram-specific actions, such as sharing a voice memo or a sticker   |
| Kik      | Send and receive native Kik messages                                           |

> [!NOTE]
> The value of an activity object's channel data property is a JSON object.
> Therefore, the examples in this article show the expected format of the
> `channelData` JSON property in various scenarios.
> To create a JSON object using .NET, use the `JObject` (.NET) class.

## Create a custom Email message

To create an email message, set the activity object's channel data property
to a JSON object that contains these properties:

| Property      | Description                                                                                                                                                  |
| ------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| bccRecipients | A semicolon (;) delimited string of email addresses to add to the message's Bcc (blind carbon copy) field.                                                   |
| ccRecipients  | A semicolon (;) delimited string of email addresses to add to the message's Cc (carbon copy) field.                                                          |
| htmlBody      | An HTML document that specifies the body of the email message. See the channel's documentation for information about supported HTML elements and attributes. |
| importance    | The email's importance level. Valid values are **high**, **normal**, and **low**. The default value is **normal**.                                           |
| subject       | The email's subject. See the channel's documentation for information about field requirements.                                                               |
| toRecipients  | A semicolon (;) delimited string of email addresses to add to the message's To field.                                                                        |

> [!NOTE]
> Messages that your bot receives from users via the Email channel may
> contain a channel data property that is populated with a JSON object like the one described above.

This snippet shows an example of the `channelData` property for a custom email message.

```json
"channelData": {
    "type": "message",
    "locale": "en-Us",
    "channelID": "email",
    "from": { "id": "mybot@mydomain.com", "name": "My bot"},
    "recipient": { "id": "joe@otherdomain.com", "name": "Joe Doe"},
    "conversation": { "id": "123123123123", "topic": "awesome chat" },
    "channelData":
    {
        "htmlBody": "<html><body style = /"font-family: Calibri; font-size: 11pt;/" >This is more than awesome.</body></html>",
        "subject": "Super awesome message subject",
        "importance": "high",
        "ccRecipients": "Yasemin@adatum.com;Temel@adventure-works.com"
    }
}
```

## Create a full-fidelity Slack message

To create a full-fidelity Slack message,
set the activity object's channel data property to a JSON object that specifies
<a href="https://api.slack.com/docs/messages" target="_blank">Slack messages</a>,
<a href="https://api.slack.com/docs/message-attachments" target="_blank">Slack attachments</a>, and/or
<a href="https://api.slack.com/docs/message-buttons" target="_blank">Slack buttons</a>.

> [!NOTE]
> To support buttons in Slack messages, you must enable **Interactive Messages** when you
> [connect your bot](../bot-service-manage-channels.md) to the Slack channel.

This snippet shows an example of the `channelData` property for a custom Slack message.

```json
"channelData": {
   "text": "Now back in stock! :tada:",
   "attachments": [
        {
            "title": "The Further Adventures of Slackbot",
            "author_name": "Stanford S. Strickland",
            "author_icon": "https://api.slack.com/img/api/homepage_custom_integrations-2x.png",
            "image_url": "http://i.imgur.com/OJkaVOI.jpg?1"
        },
        {
            "fields": [
                {
                    "title": "Volume",
                    "value": "1",
                    "short": true
                },
                {
                    "title": "Issue",
                    "value": "3",
                    "short": true
                }
            ]
        },
        {
            "title": "Synopsis",
            "text": "After @episod pushed exciting changes to a devious new branch back in Issue 1, Slackbot notifies @don about an unexpected deploy..."
        },
        {
            "fallback": "Would you recommend it to customers?",
            "title": "Would you recommend it to customers?",
            "callback_id": "comic_1234_xyz",
            "color": "#3AA3E3",
            "attachment_type": "default",
            "actions": [
                {
                    "name": "recommend",
                    "text": "Recommend",
                    "type": "button",
                    "value": "recommend"
                },
                {
                    "name": "no",
                    "text": "No",
                    "type": "button",
                    "value": "bad"
                }
            ]
        }
    ]
}
```

When a user clicks a button within a Slack message, your bot will receive a response message
in which the channel data property is populated with a `payload` JSON object.
The `payload` object specifies contents of the original message,
identifies the button that was clicked, and identifies the user who clicked the button.

This snippet shows an example of the `channelData` property in the message that a bot receives
when a user clicks a button in the Slack message.

```json
"channelData": {
    "payload": {
        "actions": [
            {
                "name": "recommend",
                "value": "yes"
            }
        ],
        . . .
        "original_message": "{…}",
        "response_url": "https://hooks.slack.com/actions/..."
    }
}
```

Your bot can reply to this message in the normal manner,
or it can post its response directly to the endpoint that is specified by
the `payload` object's `response_url` property.
For information about when and how to post a response to the `response_url`, see
<a href="https://api.slack.com/docs/message-buttons" target="_blank">Slack Buttons</a>.

You can create dynamic buttons using the following JSON.

```json
{
    "text": "Would you like to play a game ? ",
    "attachments": [
        {
            "text": "Choose a game to play!",
            "fallback": "You are unable to choose a game",
            "callback_id": "wopr_game",
            "color": "#3AA3E3",
            "attachment_type": "default",
            "actions": [
                {
                    "name": "game",
                    "text": "Chess",
                    "type": "button",
                    "value": "chess"
                },
                {
                    "name": "game",
                    "text": "Falken's Maze",
                    "type": "button",
                    "value": "maze"
                },
                {
                    "name": "game",
                    "text": "Thermonuclear War",
                    "style": "danger",
                    "type": "button",
                    "value": "war",
                    "confirm": {
                        "title": "Are you sure?",
                        "text": "Wouldn't you prefer a good game of chess?",
                        "ok_text": "Yes",
                        "dismiss_text": "No"
                    }
                }
            ]
        }
    ]
}
```

To create interactive menus, use the following JSON:

```json
{
    "text": "Would you like to play a game ? ",
    "response_type": "in_channel",
    "attachments": [
        {
            "text": "Choose a game to play",
            "fallback": "If you could read this message, you'd be choosing something fun to do right now.",
            "color": "#3AA3E3",
            "attachment_type": "default",
            "callback_id": "game_selection",
            "actions": [
                {
                    "name": "games_list",
                    "text": "Pick a game...",
                    "type": "select",
                    "options": [
                        {
                            "text": "Hearts",
                            "value": "menu_id_hearts"
                        },
                        {
                            "text": "Bridge",
                            "value": "menu_id_bridge"
                        },
                        {
                            "text": "Checkers",
                            "value": "menu_id_checkers"
                        },
                        {
                            "text": "Chess",
                            "value": "menu_id_chess"
                        },
                        {
                            "text": "Poker",
                            "value": "menu_id_poker"
                        },
                        {
                            "text": "Falken's Maze",
                            "value": "menu_id_maze"
                        },
                        {
                            "text": "Global Thermonuclear War",
                            "value": "menu_id_war"
                        }
                    ]
                }
            ]
        }
    ]
}
```

## Create a Facebook notification

To create a Facebook notification, 
set the activity object's channel data property to a JSON object that specifies these properties:

| Property          | Description                                                                                                          |
| ----------------- | -------------------------------------------------------------------------------------------------------------------- |
| notification_type | The type of notification (e.g., **REGULAR**, **SILENT_PUSH**, **NO_PUSH**).                                          |
| attachment        | An attachment that specifies an image, video, or other multimedia type, or a templated attachment such as a receipt. |

> [!NOTE]
> For details about format and contents of the `notification_type` property and `attachment` property, see the 
> <a href="https://developers.facebook.com/docs/messenger-platform/send-api-reference#guidelines" target="_blank">Facebook API documentation</a>. 

This snippet shows an example of the `channelData` property for a Facebook receipt attachment.

```json
"channelData": {
    "notification_type": "NO_PUSH",
    "attachment": {
        "type": "template"
        "payload": {
            "template_type": "receipt",
            . . .
        }
    }
}
```

## Create a Telegram message

To create a message that implements Telegram-specific actions, 
such as sharing a voice memo or a sticker, 
set the activity object's channel data property to a JSON object that specifies these properties: 

| Property   | Description                             |
| ---------- | --------------------------------------- |
| method     | The Telegram Bot API method to call.    |
| parameters | The parameters of the specified method. |

These Telegram methods are supported: 

- answerInlineQuery
- editMessageCaption
- editMessageReplyMarkup
- editMessageText
- forwardMessage
- kickChatMember
- sendAudio
- sendChatAction
- sendContact
- sendDocument
- sendLocation
- sendMessage
- sendPhoto
- sendSticker
- sendVenue
- sendVideo
- sendVoice
- unbanChateMember

For details about these Telegram methods and their parameters, see the 
<a href="https://core.telegram.org/bots/api#available-methods" target="_blank">Telegram Bot API documentation</a>.

> [!NOTE]
> <ul><li>The <code>chat_id</code> parameter is common to all Telegram methods. If you do not specify <code>chat_id</code> as a parameter, the framework will provide the ID for you.</li>
> <li>Instead of passing file contents inline, specify the file using a URL and media type as shown in the example below.</li>
> <li>Within each message that your bot receives from the Telegram channel, the <code>ChannelData</code> property will include the message that your bot sent previously.</li></ul>

This snippet shows an example of a `channelData` property that specifies a single Telegram method.

```json
"channelData": {
    "method": "sendSticker",
    "parameters": {
        "sticker": {
            "url": "https://domain.com/path/gif",
            "mediaType": "image/gif",
        }
    }
}
```

This snippet shows an example of a `channelData` property that specifies an array of Telegram methods.

```json
"channelData": [
    {
        "method": "sendSticker",
        "parameters": {
            "sticker": {
                "url": "https://domain.com/path/gif",
                "mediaType": "image/gif",
            }
        }
    },
    {
        "method": "sendMessage",
        "parameters": {
            "text": "<b>This message is HTML formatted.</b>",
            "parse_mode": "HTML"
        }
    }
]
```

When a Telegram method is implemented, your bot will receive a response message
in which the channel data property is populated with a JSON object. This response object specifies
the contents of the original message, including an `update_id` and, at most, one optional
parameter. For information about receiving incoming responses, see
<a href="https://core.telegram.org/bots/api#getting-updates" target="_blank">Getting updates</a>.

This snippet shows an example of the `channelData` property in the message that a bot receives
when a poll is created.

```json
"channelData": {
    "update_id": 43517575,
    "message": {
        "message_id": 618,
        "from": {
            "id": 803613355,
            "is_bot": false,
            "first_name": "Joe",
            "last_name": "Doe",
            "username": "jdoe",
            "language_code": "en"
        },
        "chat": {
            "id": 803613355,
            "first_name": "Joe",
            "last_name": "Doe",
            "username": "jdoe",
            "type": "private"
        },
        "date": 1582577834,
        "poll": {
        "id": "5089525250643722242",
        "question": "How to win?",
        "options": [
            {
                "text": "Be the best",
                "voter_count": 0
            },
            {
                "text": "Help those in need",
                "voter_count": 0
            },
            {
                "text": "All of the above",
                "voter_count": 0
            }
        ],
        "total_voter_count": 0,
        "is_closed": false,
        "is_anonymous": true,
        "type": "regular",
        "allows_multiple_answers": false
        }
    }
}
```

## Create a native Kik message

To create a native Kik message, set the activity object's channel data property to a JSON object that specifies this property:

| Property | Description                                                                                                                                                                 |
| -------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| messages | An array of Kik messages. For details about Kik message format, see <a href="https://dev.kik.com/#/docs/messaging#message-formats" target="_blank">Kik Message Formats</a>. |

This snippet shows an example of the `channelData` property for a native Kik message.

```json
"channelData": {
    "messages": [
        {
            "chatId": "c6dd8165…",
            "type": "link",
            "to": "kikhandle",
            "title": "My Webpage",
            "text": "Some text to display",
            "url": "http://botframework.com",
            "picUrl": "http://lorempixel.com/400/200/",
            "attribution": {
                "name": "My App",
                "iconUrl": "http://lorempixel.com/50/50/"
            },
            "noForward": true,
            "kikJsData": {
                    "key": "value"
                }
        }
    ]
}
```

## Create a LINE message

To create a message that implements LINE-specific message types (such as sticker, templates, or LINE specific action types like opening the phone camera), set the activity object's channel data property to a JSON object that specifies LINE message types and action types. 

| Property | Description                       |
| -------- | --------------------------------- |
| type     | The LINE action/message type name |

These LINE message types are supported:
* Sticker
* Imagemap 
* Template (Button, confirm, carousel) 
* Flex 

These LINE actions can be specified in the action field of the message type JSON object: 
* Postback 
* Message 
* URI 
* Datetimerpicker 
* Camera 
* Camera roll 
* Location 

For details about these LINE methods and their parameters, see the [LINE Bot API documentation](https://developers.line.biz/en/docs/messaging-api/). 

This snippet shows an example of a `channelData` property that specifies a channel message type `ButtonTemplate` and 3 action types: camera, cameraRoll, Datetimepicker. 

```json
"channelData": { 
    "type": "ButtonsTemplate", 
    "altText": "This is a buttons template", 
    "template": { 
        "type": "buttons", 
        "thumbnailImageUrl": "https://example.com/bot/images/image.jpg", 
        "imageAspectRatio": "rectangle", 
        "imageSize": "cover", 
        "imageBackgroundColor": "#FFFFFF", 
        "title": "Menu", 
        "text": "Please select", 
        "defaultAction": { 
            "type": "uri", 
            "label": "View detail", 
            "uri": "http://example.com/page/123" 
        }, 
        "actions": [{ 
                "type": "cameraRoll", 
                "label": "Camera roll" 
            }, 
            { 
                "type": "camera", 
                "label": "Camera" 
            }, 
            { 
                "type": "datetimepicker", 
                "label": "Select date", 
                "data": "storeId=12345", 
                "mode": "datetime", 
                "initial": "2017-12-25t00:00", 
                "max": "2018-01-24t23:59", 
                "min": "2017-12-25t00:00" 
            } 
        ] 
    } 
}
```

## Adding a bot to Teams

Bots added to a team become another team member, who can be `@mentioned` as part of the conversation. In fact, bots only receive messages when they are `@mentioned`, so other conversations on the channel are not sent to the bot.
For more information, see [Channel and Group chat conversations with a Microsoft Teams bot](https://aka.ms/bots-con-channel).

Because bots in a group or channel respond only when they are mentioned (`@botname`) in a message, every message received by a bot in a group channel contains its own name, and you must ensure your message parsing handles that. In addition, bots can parse out other users mentioned and mention users as part of their messages.

### Check for and strip @bot mention

```csharp

Mention[] m = sourceMessage.GetMentions();
var messageText = sourceMessage.Text;

for (int i = 0;i < m.Length;i++)
{
    if (m[i].Mentioned.Id == sourceMessage.Recipient.Id)
    {
        //Bot is in the @mention list.
        //The below example will strip the bot name out of the message, so you can parse it as if it wasn't included. Note that the Text object will contain the full bot name, if applicable.
        if (m[i].Text != null)
            messageText = messageText.Replace(m[i].Text, "");
    }
}
```

```javascript
var text = message.text;
if (message.entities) {
    message.entities
        .filter(entity => ((entity.type === "mention") && (entity.mentioned.id.toLowerCase() === botId)))
        .forEach(entity => {
            text = text.replace(entity.text, "");
        });
    text = text.trim();
}

```

> [!IMPORTANT] 
> Adding a bot by GUID, for anything other than testing purposes, is not recommended. Doing so severely limits the functionality of a bot. Bots in production should be added to Teams as part of an app. See [Create a bot](https://docs.microsoft.com/microsoftteams/platform/concepts/bots/bots-create) and [Test and debug your Microsoft Teams bot](https://docs.microsoft.com/microsoftteams/platform/concepts/bots/bots-test).


## Additional resources

- [Entities and activity types](../bot-service-activities-entities.md)
- [Bot Framework Activity schema](https://aka.ms/botSpecs-activitySchema)
