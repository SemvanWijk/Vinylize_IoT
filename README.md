# Vinylize_IoT


With this guide i will show you how to control a ledstrip using a telegram bot, to simulate a digitally controlled vinyl record player. You wil be able to change the LP(vinyl record) and automatically switch to that LPs RPM(revolutions per minute), aswell as adjusting the volume.
<br /><br /><br />

## Requirements:
- ESP32 DevKit V1 board
- NeoPixel LED strip 
- Wires
- Laptop with the Arduino IDE installed
- Internet connection
- Phone with Telegram installed
<br /><br /><br />

## Step 1: Creating your Telegram Bot

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

## Step 3: Install Libraries

You will need to install the following libraries:
- Adafruit NeoPixel: For controlling the NeoPixel LED strip.
- UniversalTelegramBot: For communication with the Telegram bot.<br /><br />
|<img height="400px" src="neopixel.png"> |<img height="400px" src="tgbot.png">|




<br /><br /><br />

## Step 4: Create a new Sketch
- Go to file -> New Sketch.<br /><br />
<img height="200px" src="sketch.png"> <br />
<br /><br /><br />

## Step 5: Assemble Your board
Take your board and ledstrip and do the following:
- Put the GND cable on a GND pin
- Put the +5V cable on a 3V3 pin
- Put the DIN cable on any of your D pins, I used D5, as its closest to the rest of used pins, but it doesnt really matter which one you use.<br /><br />
|<img height="300px" src="board.jpeg"> |<img height="300px" src="cables.jpeg">|<br />
<br /><br /><br />



## Step 6: Code Setup
We will now ensure that all the required libraries are included, aswell as defining the key settings for our project. Copy the code and adjust the following
- Change the SSID to the name of your wifi network, Wifi password to the respective and Bot token to the token you recieved from the BotFather. The token should follow this structure: XXXXX:XXXXXXXXXXXXXXX
- Make sure the NUMPIXELS is equal to the amount of LEDs on your strip, there are 15 in my case.
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

## Step 7: Adding LPs
We are goin to need some LPs with different RPMs, so we'll add those in ourselves for now. You can add any amount off LPs, but in this case i asked ChatGPT for some populair ones, making sure 33, 45 and 78 RPM are all represented.
To simulate different vinyl albums and their respective RPMs, we’ll create a custom structure to store the names and RPMs of each LP. This information will be used to control the LED colors based on the selected LPs RPM.

```
// Define LP records and their corresponding RPM values
struct LPRecord {
  String name;  // Name of the album
  int rpm;      // RPM value of the album (e.g., 33, 45, 78)
};

// A list of LP records with their names and corresponding RPMs
LPRecord records[] = {
  {"Abbey Road", 33},
  {"Thriller", 45},
  {"Blue Train", 33},
  {"Kind of Blue", 33},
  {"Elvis Presley", 78}
};
```
<br /><br /><br />

## Step 8: Initializing Current Volume and Color
After initializing the NeoPixel strip, we also need to set up variables to keep track of the current volume and the color of the LEDs based on the selected LP’s RPM.
```
int currentVolume = NUMPIXELS;  // Start with all LEDs on (full volume)
uint32_t currentColor = strip.Color(255, 255, 255);  // Default color (white), will change based on LP RPM
```
currentVolumetracks the current volume level by determining how many LEDs are lit on the strip. It starts with all LEDs on (maximum volume).<br>
currentColor holds the color of the LEDs based on the selected LP’s RPM. It will be updated when you choose a new LP via the Telegram bot. Initially, it’s set to white.
<br /><br /><br />

## Step 9: Bot Commands
Now that we’ve set up the basics, it's time to implement the functionality that allows the Telegram bot to respond to user commands. The bot will handle two main types of interactions:
- Adjusting the volume by turning LEDs on or off. The LEDs pixels symbolise the volume, with all light on representing a full volume bar, and no lights on representing the volume being off.
- Changing the currently playing LP, which, depending on the record's RPM, adjusts to color of the strip, representing the different speeds with a different color.

We’re going to use two commands to adjust the volume:
- volume_up: This command increases the volume by turning on more LEDs.
- volume_down: This command decreases the volume by turning off LEDs.

Here's what the functionality behind that looks like
```
// Function to update LED strip based on the current volume and color
void updateVolumeDisplay() {
  strip.clear();  // Clear the LED strip
  for (int i = 0; i < currentVolume; i++) {
    strip.setPixelColor(i, currentColor);  // Set each active LED to the current color
  }
  strip.show();  // Update the strip with new settings
}
```
The code turns off all LEDs, then checks the current volume level to light up the right number of LEDs. It sets them to the color of the selected LPs RPM and updates the LED strip to show these changes.
<br /><br /><br />

### LP Selection<br />

When selecting a new LP, we will send the bot a message like “Put on Abbey Road.” The bot will look up the RPM for that LP and change the LED color accordingly. Here’s how we define the colors based on RPM:

- 33 RPM: Green.
- 45 RPM: Blue.
- 78 RPM: Red.

Here’s the code to handle the LP selection and change the LED color:
```
// Function to update the LED color based on the album's RPM
void setColorByRPM(int rpm) {
  if (rpm == 33) {
    currentColor = strip.Color(0, 255, 0);  // Green for 33 RPM
  } else if (rpm == 45) {
    currentColor = strip.Color(0, 0, 255);  // Blue for 45 RPM
  } else if (rpm == 78) {
    currentColor = strip.Color(255, 0, 0);  // Red for 78 RPM
  }
  updateVolumeDisplay();  // Apply the new color to the LED strip
}
```
Make sure you copy both of these pieces of code.
<br /><br /><br />

## Step 10: Void Setup
In the Void Setup, we start up our board, connect it to the internet, and set up the LED strip and Telegram bot.
- Open the serial monit and set the Baus rate to 115200.<br /><br />
|<img height="300px" src="serial_monitor.png"> |<img height="300px" src="baud.png">|<br />
- Now put the following code in your file:
```
void setup() {
  // Initialize serial communication for debugging
  Serial.begin(115200);

  // Connect to Wi-Fi
  WiFi.begin(WIFI_SSID, WIFI_PASSWORD);
  while (WiFi.status() != WL_CONNECTED) {
    delay(1000);
    Serial.println("Connecting to WiFi...");
  }
  Serial.println("Connected to WiFi");

  // Initialize the NeoPixel LED strip
  strip.begin();
  strip.show();  // Turn off all LEDs initially
  strip.setBrightness(50);  // Optional: Set brightness level (0-255)

  // Setup Telegram Bot
  client.setInsecure();  // Bypass SSL checks
  Serial.println("Telegram bot ready");
}
``` 





## Step 11: Void Loop





## Step 12: Test it Out!




## Code up Until Now
With everything explained, here is the complete code that has been used until now:
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

// Define LP records and their corresponding RPM values
struct LPRecord {
  String name;  // Name of the album
  int rpm;      // RPM value of the album (e.g., 33, 45, 78)
};

// A list of LP records with their names and corresponding RPMs
LPRecord records[] = {
  {"Abbey Road", 33},
  {"Thriller", 45},
  {"Blue Train", 33},
  {"Kind of Blue", 33},
  {"Elvis Presley", 78}
};

// Initialize current volume and color
int currentVolume = NUMPIXELS;  // Current volume level (start with all LEDs on)
uint32_t currentColor = strip.Color(255, 255, 255);  // Default color (white)

// Function to update LED strip based on the current volume and color
void updateVolumeDisplay() {
  strip.clear();  // Clear the LED strip
  for (int i = 0; i < currentVolume; i++) {
    strip.setPixelColor(i, currentColor);  // Set each active LED to the current color
  }
  strip.show();  // Update the strip with new settings
}

// Function to update the LED color based on the album's RPM
void setColorByRPM(int rpm) {
  if (rpm == 33) {
    currentColor = strip.Color(0, 255, 0);  // Green for 33 RPM
  } else if (rpm == 45) {
    currentColor = strip.Color(0, 0, 255);  // Blue for 45 RPM
  } else if (rpm == 78) {
    currentColor = strip.Color(255, 0, 0);  // Red for 78 RPM
  }
  updateVolumeDisplay();  // Apply the new color to the LED strip
}

void setup() {
  Serial.begin(115200);

  WiFi.begin(WIFI_SSID, WIFI_PASSWORD);
  while (WiFi.status() != WL_CONNECTED) {
    delay(1000);
    Serial.println("Connecting to WiFi...");
  }
  Serial.println("Connected to WiFi");

  strip.begin();
  strip.show();  
  strip.setBrightness(50);  

  client.setInsecure();  
  Serial.println("Telegram bot ready");
}

void loop() {
  int numNewMessages = bot.getUpdates(bot.last_message_received + 1);

  while (numNewMessages) {
    Serial.println("Got a message");
    
    for (int i = 0; i < numNewMessages; i++) {
      String text = bot.messages[i].text;
      String fromName = bot.messages[i].from_name;

      if (text == "/volume_up") {
        if (currentVolume < NUMPIXELS) {
          currentVolume++;
          updateVolumeDisplay();
        }
      }

      if (text == "/volume_down") {
        if (currentVolume > 0) {
          currentVolume--;
          updateVolumeDisplay();
        }
      }

      if (text.startsWith("Put on ")) {
        String albumName = text.substring(7);
        for (int j = 0; j < sizeof(records) / sizeof(records[0]); j++) {
          if (albumName == records[j].name) {
            setColorByRPM(records[j].rpm);
            break;
          }
        }
      }
    }

    numNewMessages = bot.getUpdates(bot.last_message_received + 1);
  }
}
```



<br /><br /><br /><br /><br /><br /><br /><br /><br />
Visit https://www.discogs.com/settings/developers and click the "genrate new Token" button to get your API Key.
