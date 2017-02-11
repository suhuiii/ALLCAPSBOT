# ALLCAPSBOT
A zulip bot that listens to messages and saves any string with three or more uppercase words. 
When tagged in a Zulip stream, it will randomly select a saved string and post in the stream.

Made at the Recurse Center in collaboration with [Steve McCarthy](https://github.com/ifo)

## Background
I wanted to build a zulip bot one day and decided to pair with Steve who had just finished making a zulip bot in Golang. 
We chose to use Python and reference the existing [RSVPBot](https://github.com/kokeshii/RSVPBot) which was based off [Bot-builder](https://github.com/TansyArron/Bot-Builder).
Getting a working bot was pretty easy with those two resources and the Zulip API, and the rest of it was mostly a discussion around the expected behavior (e.g. bot should only save strings that it hasn't seen, therefore we should use a dictionary).

# To build your own bot
1. Create a bot in zulip settings as shown [here](https://recurse.zulipchat.com/api/)
2. Set up your environment variables based on what you have created above

```
export ZULIP_USERNAME="bot-username"
export ZULIP_API_KEY="bot-apikey"
export ZULIP_KEY_WORD="some keyword" # this should be what you want your bot to react to. can be a word (rsvp) or a tag (@**bot-username**)
export MEGAPHONE_FILE='megaphone.txt' # file I/O - application dependent
```
3. Modify the method `respond` and `send_message` to make your bot do what you want.
3.a For ALLCAPSBOT - we have it listening to all events of type `message` (filtering of events is done in the method `process`).
