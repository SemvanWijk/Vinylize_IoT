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

# Step 5: Assemble your board
Take your board and ledstrip and do the following:
- Put the GND cable on a GND pin
- Put the +5V cable on a 3V3 pin
- Put the DIN cable on any of your D pins, I used D5, as its closest to the rest of used pins, but it doesnt really matter which one you use.<br /><br />
|<img height="300px" src="board.jpeg"> |<img height="300px" src="cables.jpeg">|<br />
<br /><br /><br />



# Step 6: Code setup
- Copy the code and adjust the SSID to the name of your wifi network, Wifi password to the respective and Bot token to the token you recieved from the botfather. The token should follow this structure: XXXXX:XXXXXXXXXXXXXXX
- Make sure the NUMPIXELS is equal to the amount of LEDs on your strip, 15 in my case.
- Adjust LED_PIN to the pin to which you added your ledstrip, the D5 pin in my case.


```
#include <WiFi.h>
#include <WiFiClientSecure.h>
#include <UniversalTelegramBot.h>
#include <Adafruit_NeoPixel.h>

// WiFi credentials
#define WIFI_SSID "your-SSID"
#define WIFI_PASSWORD "your-PASSWORD"

// Telegram bot token
#define BOT_TOKEN "your-telegram-bot-token"

// Pin connected to the NeoPixel strip
#define LED_PIN 5
#define NUMPIXELS 15  // Number of LEDs on the NeoPixel strip

Adafruit_NeoPixel strip(NUMPIXELS, LED_PIN, NEO_GRB + NEO_KHZ800);

WiFiClientSecure client;
UniversalTelegramBot bot(BOT_TOKEN, client);
```
<br /><br /><br />

# Step 7: Adding LPs
We are goin to need some LPs with different RPMs, so we'll add those in ourselves for now. You can add any amount off LPs, but in this case i asked ChatGPT for some populair ones, making sure 33, 45 and 78 RPM are all represented.

```
// Define LP records and their corresponding RPM values
struct LPRecord {
  String name;
  int rpm;
};

LPRecord records[] = {
  {"Abbey Road", 33},
  {"Thriller", 45},
  {"Blue Train", 33},
  {"Kind of Blue", 33},
  {"Elvis Presley", 78}
};
```










<br /><br /><br /><br /><br /><br /><br /><br /><br />
Visit https://www.discogs.com/settings/developers and click the "genrate new Token" button to get your API Key.
