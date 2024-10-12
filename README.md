# Vinylize_IoT


With this guide i will show you how to control a ledstrip using a telegram bot, to simulate a digitally controlled vinyl record player. You wil be able to change the album and automatically switch to that albums RPM, aswell as adjusting the volume.
<br /><br /><br />

# Requirements:
- ESP32 DevKit V1 board
- NeoPixel LED strip 
- Wires
- Laptop with the Arduino IDE installed
- Internet connection
- Phone with Telegram installed
<br /><br /><br />

# Step 1: Creating your Telegram Bot

- Open Telegram and search for the BotFather.
- Use the command "/newbot" to create a new bot.
- Give the bot a name and username.
- Save the bot token, which will be used in your code to connect to the bot.<br /><br />
<img height="500px" src="botfather.jpeg"> <br />
  
You can now message your new bot. Make sure you are chatting with your own bot, and not with the BotFather. <br /><br />
<img height="200px" src="bot_dont.jpeg"><br />
<br />You can click the link to go to your own bot.<br /><br />
<img height="200px" src="bot_do.jpeg"> <br />
<br />To get this response from your own bot, you will have to keep following the guide.
<br /><br /><br />

# Step 2: Set Up Your ESP32 with Arduino IDE

- Go to Tools -> Boards -> Board Manager.<br /><br />
<img height="300px" src="board_manager.png"> <br />
- While here, look for esp32 and install esp32 by Espressif Systems.<br /><br />
<img height="300px" src="board_library.png"> <br />
- Now select DOIT ESP32 DEVKIT V1.<br /><br />
<img height="300px" src="select_board.png"> <br />
<br /><br /><br />

# Step 3: Install Libraries

You will need to install the following libraries:
- Adafruit NeoPixel: For controlling the NeoPixel LED strip.
- UniversalTelegramBot: For communication with the Telegram bot.<br /><br />
|<img height="400px" src="neopixel.png"> |<img height="400px" src="tgbot.png">|




<br /><br /><br />

# Step 4: Create a new Sketch
- Go to file -> New Sketch.<br /><br />
<img height="200px" src="sketch.png"> <br />
<br /><br /><br />


# Step 5: Code setup
- Copy the code













<br /><br /><br /><br /><br /><br /><br /><br /><br />
Visit https://www.discogs.com/settings/developers and click the "genrate new Token" button to get your API Key.
