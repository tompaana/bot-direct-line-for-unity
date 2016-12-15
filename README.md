# Direct Line implementation for Unity #

This is a simple [Microsoft Bot Framework](https://dev.botframework.com/)
[Direct Line](https://docs.botframework.com/en-us/restapi/directline/)
implementation for Unity. The current implementation enables:

* Starting a new conversation with the bot
* Sending and receiving simple messages

## Usage ##

1. First simply copy the content to the assets of your Unity app.
2. Initialize [the BotDirectLineManager class](/BotDirectLine/BotDirectLineManager.cs)
   with your secret key (this will bind the manager to your bot) and start
   listening to bot response events: 

    ```csharp
    BotDirectLineManager.Initialize("INSERT YOUR BOT'S SECRET KEY HERE");
    BotDirectLineManager.Instance.BotResponse += OnBotResponse;
    ```

3. Start a new conversation (`OnBotResponse` will be called when the new
   conversation is started):

    ```csharp
    StartCoroutine(BotDirectLineManager.Instance.StartConversationCoroutine());
    ```

4. To send a message:

    ```csharp
    StartCoroutine(BotDirectLineManager.Instance.SendMessageCoroutine(
        _conversationState.ConversationId, "UnityUserId", "Hello bot!", "Unity User 1"));
    ```

Note that there are no return values, but your `BotResponse` event handler will
be called for all results. Here's an example for the event handler
implementation:

```csharp
private void OnBotResponse(object sender, Assets.BotDirectLine.BotResponseEventArgs e)
{
    Debug.Log("OnBotResponse: " + e.ToString());

    switch (e.EventType)
    {
        case EventTypes.ConversationStarted:
            // Store the ID
            _conversationState.ConversationId = e.ConversationId;
            break;
        case EventTypes.MessageSent:
            if (!string.IsNullOrEmpty(_conversationState.ConversationId))
            {
                // Get the bot's response(s)
                StartCoroutine(BotDirectLineManager.Instance.GetMessagesCoroutine(_conversationState.ConversationId));
            }

            break;
        case EventTypes.MessageReceived:
            // Handle the received message(s)
            break;
        case EventTypes.Error:
            // Handle the error
            break;
    }
}
```


## Acknowledgements ##

The implementation uses [SimpleJSON](http://wiki.unity3d.com/index.php/SimpleJSON)
for parsing JSON. The original version of
[the SimpleJSON project](https://github.com/Bunny83/SimpleJSON) is licensed
under MIT
(see [the license file](https://github.com/Bunny83/SimpleJSON/blob/master/LICENSE))
and was created by [Markus Göbel](https://github.com/Bunny83).
