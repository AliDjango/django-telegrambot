NOTE: This is a fork of the original django-telegrambot module, it contains updates to new django and ptb versions and features/fixes from contributors. To install this fork run: ``pip install -e git+https://github.com/telebotter/django-telegrambot.git#egg=django-telegrambot --upgrade``. 
## Documentation

The full documentation of the original library is at https://django-telegrambot.readthedocs.org.


## Changelog
The latest version to the base repository was v1.0.1, this fork started as a PR for v1.0.2


## Quickstart
Install django-telegrambot

    pip install -e git+https://github.com/telebotter/django-telegrambot.git#egg=django-telegrambot --upgrade

## Register the App

Add ``django_telegrambot`` in ``INSTALLED_APPS`` 

       #settings.py
       INSTALLED_APPS = (
           ...
           'django_telegrambot',
           ...
       )


## Setup your Bots

        #settings.py
        #Django Telegram Bot settings

        DJANGO_TELEGRAMBOT = {

            'MODE' : 'WEBHOOK', #(Optional [str]) # The default value is WEBHOOK,
                                # otherwise you may use 'POLLING'
                                # NB: if use polling you must provide to run
                                # a management command that starts a worker

            'WEBHOOK_SITE' : 'https://mywebsite.com',
            'WEBHOOK_PREFIX' : '/prefix', # (Optional[str]) # If this value is specified,
                                          # a prefix is added to webhook url

            #'WEBHOOK_CERTIFICATE' : 'cert.pem', # If your site use self-signed
        	                 #certificate, must be set with location of your public key
        	                 #certificate.(More info at https://core.telegram.org/bots/self-signed )

            'STRICT_INIT': True, # If set to True, the server will fail to start if some of the
                                 # apps contain telegrambot.py files that cannot be successfully
                                 # imported.

            'DISABLE_SETUP': False, # If set to True, there will be no tries to set webhook or read
                                    # updates from the telegram server on app's start
                                    # (useful when developing on local machine; makes django's startup faster)

            'BOT_MODULE_NAME': 'telegrambot_handlers', #(Optional [str])  # The default name for file name containing telegram handlers which has to be placed inside your local app(s). Default is 'telegrambot'. Example is to put "telegrambot_handlers.py" file to local app's folder.

            'BOTS' : [
                {
                   'ID': 'MainBot', # Unique identifier for your bot (used in your code only)

                   'TOKEN': '123456:ABC-DEF1234ghIkl-zyx57W2v1u123ew11', # Your bots token (provided by botfather)

                   'CONTEXT': True,  # Use context based handler functions (should be true for future versions)

                   #'ALLOWED_UPDATES':(Optional[list[str]]), # List the types of
        						   #updates you want your bot to receive. For example, specify
        						   #``["message", "edited_channel_post", "callback_query"]`` to
        						   #only receive updates of these types. See ``telegram.Update``
        						   #for a complete list of available update types.
        						   #Specify an empty list to receive all updates regardless of type
        						   #(default). If not specified, the previous setting will be used.
        						   #Please note that this parameter doesn't affect updates created
        						   #before the call to the setWebhook, so unwanted updates may be
        						   #received for a short period of time.

                   #'TIMEOUT':(Optional[int|float]), # If this value is specified,
        		                   #use it as the read timeout from the server

                   #'WEBHOOK_MAX_CONNECTIONS':(Optional[int]), # Maximum allowed number of
        		                   #simultaneous HTTPS connections to the webhook for update
        		                   #delivery, 1-100. Defaults to 40. Use lower values to limit the
        		                   #load on your bot's server, and higher values to increase your
        		                   #bot's throughput.

                   # 'MESSAGEQUEUE_ENABLED':(Optinal[bool]), # Make this True if you want to use messagequeue

                   # 'MESSAGEQUEUE_ALL_BURST_LIMIT':(Optional[int]), # If not provided 29 is the default value

                   # 'MESSAGEQUEUE_ALL_TIME_LIMIT_MS':(Optional[int]), # If not provided 1024 is the default value

                   # 'MESSAGEQUEUE_REQUEST_CON_POOL_SIZE':(Optional[int]), # If not provided 8 is the default value

                   #'POLL_INTERVAL' : (Optional[float]), # Time to wait between polling updates from Telegram in
                                   #seconds. Default is 0.0

                   #'POLL_CLEAN':(Optional[bool]), # Whether to clean any pending updates on Telegram servers before
        		                   #actually starting to poll. Default is False.

                   #'POLL_BOOTSTRAP_RETRIES':(Optional[int]), # Whether the bootstrapping phase of the `Updater`
        		                   #will retry on failures on the Telegram server.
        		                   #|   < 0 - retry indefinitely
        		                   #|     0 - no retries (default)
        		                   #|   > 0 - retry up to X times

                   #'POLL_READ_LATENCY':(Optional[float|int]), # Grace time in seconds for receiving the reply from
        		                   #server. Will be added to the `timeout` value and used as the read timeout from
                                   #server (Default: 2).
                },
                # Other bots here with same structure.
            ],

        }



Include in your urls.py the ``django_telegrambot.urls``

        #urls.py
        urlpatterns = [
            ...
            url(r'^', include('django_telegrambot.urls')),
            ...
        ]

Then use it in a project creating a module ``telegrambot.py`` in your app

        #myapp/telegrambot.py
        # Example code for telegrambot.py module
        from telegram.ext import CommandHandler, MessageHandler, Filters
        from django_telegrambot.apps import DjangoTelegramBot

        import logging
        logger = logging.getLogger(__name__)


        # Define a few command handlers. These usually take the two arguments bot and
        # update. Error handlers also receive the raised TelegramError object in error.
        def start(bot, update):
            bot.sendMessage(update.message.chat_id, text='Hi!')


        def help(bot, update):
            bot.sendMessage(update.message.chat_id, text='Help!')


        def echo(bot, update):
            bot.sendMessage(update.message.chat_id, text=update.message.text)


        def error(bot, update, error):
            logger.warn('Update "%s" caused error "%s"' % (update, error))


        def main():
            logger.info("Loading handlers for telegram bot")

            # Default dispatcher (this is related to the first bot in settings.DJANGO_TELEGRAMBOT['BOTS'])
            dp = DjangoTelegramBot.dispatcher
            # To get Dispatcher related to a specific bot
            # dp = DjangoTelegramBot.getDispatcher('BOT_n_id')        #get by bot identifier
            # dp = DjangoTelegramBot.getDispatcher('BOT_n_token')     #get by bot token
            # dp = DjangoTelegramBot.getDispatcher('BOT_n_username')  #get by bot username

            # on different commands - answer in Telegram
            dp.add_handler(CommandHandler("start", start))
            dp.add_handler(CommandHandler("help", help))

            # on noncommand i.e message - echo the message on Telegram
            dp.add_handler(MessageHandler([Filters.text], echo))

            # log all errors
            dp.add_error_handler(error)



## Features of this Fork

* Support latest django and python-telegram-bot versions
* Support latest telegram bot API
* Optionally skip webhook setup
* Change bots from settings (use personal bot_id instead of registered botname)


## Original Features

* Multiple bots
* Admin dashboard available at ``/admin/django-telegrambot``
* Polling mode by management command (an easy to way to run bot in local machine, not recommended in production!)

      ``(myenv) $ python manage.py botpolling --username=<username_bot>``
* Supporting messagequeues





## Running Tests

NOTE: The tests have not been updated for latest features. See Issue #13

Does the code actually work?

    source <YOURVIRTUALENV>/bin/activate
    (myenv) $ pip install -r requirements-test.txt
    (myenv) $ python runtests.py


## Sample Application

There a sample application in `sampleproject` directory. Here is installation instructions:

1. Install requirements with command

        pip install -r requirements.txt
2. Copy file `local_settings.sample.py` as `local_settings.py` and edit your bot token

        cp sampleproject/local_settings.sample.py sampleproject/local_settings.py

        nano sampleproject/local_settings.py
3. Run Django migrations

        python manage.py migrate
4. Run server

        python manage.py runserver
5. If **WEBHOOK** Mode setted go to 8

6. If **POLLING** Mode setted, open in your browser http://localhost/

7. Open Django-Telegram Dashboard http://localhost/admin/django-telegrambot and follow instruction to run worker by management command `botpolling`. Then go to 10

8. To test webhook locally install `ngrok` application and run command

        ./ngrok http 8000
9. Change `WEBHOOK_SITE` and `ALLOWED_HOSTS` in local_settings.py file

10. Start a chat with your bot using telegram.me link avaible in **Django-Telegram Dashboard** at http://localhost/admin/django-telegrambot


## Credits

*  `Python Telegram Bot` https://github.com/python-telegram-bot/python-telegram-bot
* `Django` https://www.djangoproject.com/
* `django-telegrambot` https://github.com/JungDev/django-telegrambot



