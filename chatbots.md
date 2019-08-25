class: center, middle

Llenguatges de Programació

# Python 3: telegram bots

Gerard Escudero i Jordi Petit

<br>

![Python 3](python-logo.svg)

<br>

Universitat Politècnica de Catalunya, 2019

---

# Hello world!

.col5050[
.col1[
L'exemple mostra el *Hello world!* <br> a telegram.

La comanda típica per iniciar un <br> bot és `/start`.

Requeriments:
* Telegram (ordinador i *smartphone*)

* Python3

* *python-telegram-bot* (amb `pip`)

* Visitar @BotFather ([enllaç](https://core.telegram.org/bots#6-botfather))

Guardeu l'*access token* en un arxiu <br> `token.txt`.
]
.col2[
<img src="figures/bot1b.png" height=400>
]
]

---

# Exemple de codi

```python3
# importing Telegram API
from telegram.ext import Updater
from telegram.ext import CommandHandler

# defining callback function for the /start command
def start(bot, update):
    bot.send_message(chat_id=update.message.chat_id, text="Hello world!")

# loading the access token from token.txt
TOKEN = open('token.txt').read().strip()

# call main Telegram objects
updater = Updater(token=TOKEN)
dispatcher = updater.dispatcher

# handling callbacks functions to the commands
dispatcher.add_handler(CommandHandler('start', start))

# starting the bot
updater.start_polling()
```

---

# Accés al missatge

Bot traductor amb `/tr`:

```python3
...
# requirements: pip install googletrans
from googletrans import Translator
...
# defining callback function for the /tr command
def tr(bot, update):
    translator = Translator()
    msg = update.message.text[3:] # delete "/tr "
    msg_tr = translator.translate(msg).text
    bot.send_message(chat_id=update.message.chat_id, text=msg_tr)
...
# handling callbacks functions to the commands
dispatcher.add_handler(CommandHandler('tr', tr))
...
```

![traductor](figures/bot2a.png)

---

# Accés als arguments

Tracta cada paraula del missatge com un argument:

```python3
...
# defining callback function for the /tr command
def tr(bot, update, args):
    translator = Translator()
    msg = " ".join(args)
    msg_tr = translator.translate(msg).text
    bot.send_message(chat_id=update.message.chat_id, text=msg_tr)
...

# handling callbacks functions to the commands
dispatcher.add_handler(CommandHandler('tr', tr, pass_args=True))
...
```

![traductor](figures/bot2a.png)

---

# Guardant informació I

Utilitza el diccionari `user_data` per guardar informació de la conversa:

```python3
...
def tr(bot, update, args, user_data):
    if 'counter' not in user_data:
        user_data['counter'] = 0
    user_data['counter'] += 1

    translator = Translator()
    msg = " ".join(args)
    msg_tr = translator.translate(msg).text
    bot.send_message(chat_id=update.message.chat_id, text=msg_tr)
    report = str(user_data['counter']) + translations\nfor user ' + \
             update.message.chat.first_name
    bot.send_message(chat_id=update.message.chat_id, text=report)
...

dispatcher.add_handler(CommandHandler('tr', tr, pass_args=True, \
                       pass_user_data=True))
...
```

---

# Guardant informació II

![traductor](figures/bot2c.png)

---

# Tractament sense comanda I

Canvia la forma del *callback*!

```python3
...
from telegram.ext import MessageHandler, Filters
...
def tr(bot, update, user_data):
    if 'counter' not in user_data:
        user_data['counter'] = 0
    user_data['counter'] += 1
    translator = Translator()
    msg = update.message.text
    msg_tr = translator.translate(msg).text
    bot.send_message(chat_id=update.message.chat_id, text=msg_tr)
    report = str(user_data['counter']) + ' translations\nfor user ' + \
             update.message.chat.first_name
    bot.send_message(chat_id=update.message.chat_id, text=report)
...

# handling callbacks functions to the commands
dispatcher.add_handler(MessageHandler(Filters.text, tr, \
                       pass_user_data=True))
...
```
---

# Tractament sense comanda II

![traductor](figures/bot2d.png)

---

# Multimedia I

A l'exemple s'envia una foto desde *url*, una des d'arxiu i un text en *markdown*:

```python3
...
from telegram import ParseMode

def start(bot, update):
    info = '''
Written in MarkDown:
- *bold*
- _italic_
'''
    bot.send_photo(chat_id=update.message.chat_id, \
         photo='https://www.upc.edu/++theme++homeupc/assets/images/' + \
         'logomark.png')
    bot.send_photo(chat_id=update.message.chat_id, \
         photo=open('t_logo.png', 'rb'))
    bot.send_message(chat_id=update.message.chat_id, text=info, \
         parse_mode=ParseMode.MARKDOWN)
...
```

De forma de similar:
* `ParseMode.HTML`
* `send_video` i `send_audio`

---

# Multimedia II

<img src="figures/bot3.png" height=400>

---

# Mapes i localització I

```python3
import random
import os
from staticmap import StaticMap, CircleMarker
from telegram.ext import Updater, CommandHandler, MessageHandler, Filters

def start(bot, update):
    bot.send_message(chat_id=update.message.chat_id, \
                     text="Send me your location!")

def where(bot, update, user_data):
    ...

TOKEN = open('token.txt').read().strip()

updater = Updater(token=TOKEN)
dispatcher = updater.dispatcher

dispatcher.add_handler(CommandHandler('start', start))
dispatcher.add_handler(MessageHandler(Filters.location, where, \
                       pass_user_data=True))

updater.start_polling()
```

---

# Mapes i localització II

```python3
def where(bot, update, user_data):
    try:
        name = "%d.png" % random.randint(1000000, 9999999)
        lat, lon = update.message.location.latitude,
        update.message.location.longitude
        mapa = StaticMap(500, 500)
        mapa.add_marker(CircleMarker((lon, lat), 'blue', 10))
        imatge = mapa.render()
        imatge.save(name)
        bot.send_photo(chat_id=update.message.chat_id, \
                       photo=open(name, 'rb'))
        os.remove(name)
    except Exception as e:
        print(e)
        bot.send_message(chat_id=update.message.chat_id, \
                         text='Something goes wrong!')
```

⚠️ La localització s'ha d'enviar amb l'*smartphone* !

---

# Mapes i localització III

<img src="figures/bot4.png" height=450>

---

# nltk I

```python3
from telegram.ext import Updater
from telegram.ext import MessageHandler, Filters
from nltk import word_tokenize, pos_tag, ne_chunk
from nltk.tree import Tree

def Tree2Str(t):
    ...

def psr(bot, update):
    sentence = update.message.text
    res = ne_chunk(pos_tag(word_tokenize(sentence))) # nltk
    s = '\n'.join(Tree2Str(res))
    bot.send_message(chat_id=update.message.chat_id, text=s)

TOKEN = open('token.txt').read().strip()

updater = Updater(token=TOKEN)
dispatcher = updater.dispatcher

dispatcher.add_handler(MessageHandler(Filters.text, psr))

updater.start_polling()
```

---

# nltk II

<img src="figures/bot5.png" height=450>

---

# nltk III

Estructura *nltk*:

```
Tree(’S’, [Tree(’PERSON’, [(’Mark’, ’NNP’)]), Tree(’ORGANIZATION’,
[(’Pedersen’, ’NNP’)]), (’is’, ’VBZ’), (’working’, ’VBG’), (’at’,
’IN’), Tree(’ORGANIZATION’, [(’Google’, ’NNP’)]), (’since’, ’IN’),
(’1994’, ’CD’), (’.’, ’.’)])
```

Funció per tractar-la:

```python3
def Tree2Str(t):
    if isinstance(t, Tree):
        if t.label() == 'S':
            return [Tree2Str(e) for e in t]
        else:
            cs = [e for e in t]
            return ' '.join([e[0] for e in cs]) + ' - ' + \
                   cs[0][1] + ' - ' + t.label()
    else:
        return t[0] + ' - ' + t[1]
```

---

# Referències

* [Apunts *Bots de Telegram*](https://lliçons.jutge.org/python/telegram.html)

* [Documentació llibreria *python-telegram-bot*](https://python-telegram-bot.readthedocs.io/en/latest/index.html)

* [Documentació *Botfather*](https://core.telegram.org/bots#6-botfather)
