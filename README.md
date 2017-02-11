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
export MEGAPHONE_FILE='megaphone.txt' # file I/O - application dependent. Remove from constructor and call to constructor if not using..
```
3. Modify the method `respond` and `send_message` to make your bot do what you want. In the case of ALLCAPSBOT -
  1. In `respond`:
    1. The bot listens to all events of type `message` (filtering of events is done in the method `process`)  
    2. If the content of `message` is checked to see if it matches the regex pattern for three Uppercase words which may end with punctuation  
    3. Matches are passed as an argument to the method `add_CAPS_string`, which checks if the string exists as a key in the dictionary. If the new string is unique, then it is added to the dictionary, appended to a list for random access and also written to a file  
    4. If content of message also contains keyword (in this case `@**bot-username**), call `send_message`  
  2. In `send_message`: In `self.client.send_message({..})`, change the value for the key `content` to send whatever you want!    
The code for the above portion (and really all you need to change) looks like this - 
  ```
      def process(self, event):
        if event['type'] == 'message':
            self.respond(event['message'])

    def respond(self, message):
        """Now we have an event dict, we should analyze it completely."""
        content = message['content']
        
        [self.add_CAPS_string(in_caps.strip()) for in_caps in re.findall(r'(?:(?:\b[A-Z\']+\b)[ \.,!?]*){3,}', content)]

        if self.key_word in content:
            self.send_message(message)

    def add_CAPS_string(self, string):
        if not string in self.megaphone_dict:
            self.megaphone_file.write(string + "\n")
            self.megaphone.append(string)
            self.megaphone_dict[string] = True
            self.megaphone_file.flush()

    def send_message(self, msg):
        """Sends a message to zulip stream or user."""
        msg_to = msg['display_recipient']
        if msg['type'] == 'private':
            msg_to = msg.get('sender_email') or msg_to

        self.client.send_message({
            "type": msg['type'],
            "subject": msg["subject"],
            "to": msg_to,
            "content": self.megaphone[randint(0, len(self.megaphone)-1)],
        })
  ```
  
