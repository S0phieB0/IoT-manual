# IoT-manual - Not done yet
For Internet of Thinks I made a new idea for a diffuser. In this manual I'll try to make the internet part of the diffuser that i thought of.

**Name**: Sophie Bouman
**Date**: 25 oct. 2022

## Supplies
- Arduino IDE
- ESP8266
- LED lights

## Step 1 - Getting the time from the internet in the Arduino IDE
For my diffuser i need a light to automaticly turn off when it's time to sleep.

### 1.1 source
I looked at this website to find out how to do it: https://lastminuteengineers.com/esp8266-ntp-server-date-time-tutorial/

### 1.2 Add ntpclient to library
So you go to **Sketch > Inlcude Library > Manage Librarues**
Search for '**ntpclient**' by Fabrice Weinberg and instal it.

<img width="217" alt="libraryntpclient" src="https://user-images.githubusercontent.com/90249238/198129553-7e77b66c-351d-4042-9b6a-36436857822d.png">


### 1.3 Add code
Add the following code:

#include <NTPClient.h>
#include <ESP8266WiFi.h>
#include <WiFiUdp.h>

const char *ssid     = "YOUR_SSID";
const char *password = "YOUR_PASS";

const long utcOffsetInSeconds = 3600;

char daysOfTheWeek[7][12] = {"Sunday", "Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday"};

// Define NTP Client to get time
WiFiUDP ntpUDP;
NTPClient timeClient(ntpUDP, "pool.ntp.org", utcOffsetInSeconds);

void setup(){
  Serial.begin(115200);

  WiFi.begin(ssid, password);

  while ( WiFi.status() != WL_CONNECTED ) {
    delay ( 500 );
    Serial.print ( "." );
  }

  timeClient.begin();
}

void loop() {
  timeClient.update();

  Serial.print(daysOfTheWeek[timeClient.getDay()]);
  Serial.print(", ");
  Serial.print(timeClient.getHours());
  Serial.print(":");
  Serial.print(timeClient.getMinutes());
  Serial.print(":");
  Serial.println(timeClient.getSeconds());
  //Serial.println(timeClient.getFormattedTime());

  delay(1000);
}

### 1.4 Add your info in the code
You need to fill in a few things before the code can work:
- Add your network and password
- Adjust the UTC offset for your timezone(This link is where you can find yours: https://en.wikipedia.org/wiki/List_of_UTC_offsets)
  For most of Europe its UTC+01:00 --> 1 * 60 * 60 = 3600
  So then you fill in: 'const long utcOffsetInSeconds = 3600;'
  
### 1.5 upload your code
Verify the sketch and upload it. Don't forget to check if you linked the right board and port.
If everything worked you can now open your serial monitor (Button at the top right) and you can see the day of the week and the time.

### 🚩 1.6 Mistake with the timezone 🚩
The serial monitor said that it was one hour earlier than it actually was. I think it is because of daylight saving time. So instead of 'const long utcOffsetInSeconds = 3600;' it is 'const long utcOffsetInSeconds = 7200;' and then the time is correct.

### 1.7 The part I coundn't do
I wanted the light to go on and off at certain times, but the problem is that I don't know how to do that exactly.
I know that i had to write an If, If else code for it to work, but I don't get what exactly I could write for the code to understand at what time it needs to change.

My best guess was to add:

#define NUM_LEDS 60
#define DATA_PIN D5
#define CLOCK_PIN 13
CRGB leds[NUM_LEDS];

void setup(){
    delay(100);
    FastLED.addLeds<WS2811, DATA_PIN, RGB>(leds, NUM_LEDS);

}

void loop() {
If (timeClient.getHours >= 22 && <=5)
     for(int whiteLed = 0; whiteLed < NUM_LEDS; whiteLed = whiteLed + 1) {
      leds[whiteLed] = CRGB::DarkRed;
      FastLED.show();
      delay(10);
      leds[whiteLed] = CRGB::Black;
 }
 
  else (timeClient.getHours > 5)
     for(int whiteLed = 0; whiteLed < NUM_LEDS; whiteLed = whiteLed + 1) {
      leds[whiteLed] = CRGB::DarkRed;
      FastLED.show();
      delay(10);
      leds[whiteLed] = CRGB::Black;
 }


## step 2 - Linking your google calendar to ESP8266
For my diffuser want to be able to link Google Calendar to it so that the diffuser goes on at the right time.

### 2.1 source
I looked at this website to find out how to do it: https://www.instructables.com/Google-Calendar-Events-to-ESP8266/

### 2.2 Adafruit feed
I already made an Adafruit account, if you don't have one you need to make one first.
- Go to https://io.adafruit.com/
- Go to **Feeds** and click on **New feed**
- Give it a name so you can easily recognize it
- Open the feed and see that its empty, you will later send data to it using Zapier.

<img width="300" alt="createnewfeed" src="https://user-images.githubusercontent.com/90249238/198129916-3053fb63-387e-42c1-92ca-a26bccbbfd78.png">
<img width="300" alt="adafruitfeed" src="https://user-images.githubusercontent.com/90249238/198129963-2be3cb14-b123-41b1-a42a-df60cb6fff49.png">


### 2.3 Make a Zap in Zappier 
I didn't have a Zappier account yet so I had to make one first. Just click on sign in and fill out the needed information.

<img width="500" alt="zaphome" src="https://user-images.githubusercontent.com/90249238/198130183-90b3beab-ea8e-4520-a1c1-147e0fa98838.png">
- Go to http://zapier.com/
- Click on '**Make a zap**'

<img width="300" alt="zapstep1" src="https://user-images.githubusercontent.com/90249238/198130247-15e80278-eb5d-453b-96c7-79bc74bfceff.png">
<img width="300" alt="zapstep1trigger" src="https://user-images.githubusercontent.com/90249238/198130265-7c5fdd9d-c0d1-4590-b19d-f548350c3764.png">
First you have to costumize the Google Calendar events:
- Select **Google Calendar** for the app
- The trigger event is '**Event start**'
- Choose the Google Calendar account that you want to use and the calendar that you want to use from your account.
- Set up the trigger. Choose the amount of time you want to get alerted about an upcoming event.
_- You can also add a Search term, then Zapier will only trigger events with a certain name. If you don't use this, all the events from that caledar will trigger Zapier._
- Click on '**Test and continue**'. If all goes well you will see that it all worked and you can continue to the next part.

<img width="300" alt="zapstep2" src="https://user-images.githubusercontent.com/90249238/198130380-6eb93695-21f9-48d3-bdf2-99afb97227a4.png">
<img width="300" alt="zapstep2value" src="https://user-images.githubusercontent.com/90249238/198130434-4558762d-03dd-4c8a-95aa-8eb18217280d.png">
The second part is connecting Zapier to Adafruit:
- Under **Choose app** you have to select '**ADafruit IO**'
- Under **Choose Action** Event select '**Create Feed Data**'
- Under **Choose account** you have to log in with your Adafruit account.
- Fill in the Feed you want to use
- Under **Value** click on the add icon and select '**1. Event begins:**' ans '**1. Event Ends**'
  _🚩 Select them in the right order enn don't select the 'pretty' versions otherwise it might not work.
_
- Click '**Test & Continue**' and check in your Adafruit if the test came through.
- Click '**Turn on the Zap**' (in the top right corner)

- <img width="300" alt="adafruitxzap" src="https://user-images.githubusercontent.com/90249238/198130574-c9795a2b-9484-49c4-aa13-567b9250bb1e.png">


## Step 3: Arduino IDE code
- Plug in your ESP8266 if you didn't already do that.
- Go to https://github.com/SummerDanoe/ReadGoogleCalFeed and copy the code
  - Don't forget to fill in your Adafruit username and key and your Wifi and padsword where it needs to be in the code.

### 🚩 3.1 Error in the code 🚩
When tried to verify the code it gave me an error: '**Compilation error: variable or field 'handleMessage' declared void**'
Normaly you only have a void setup and void loop as far as I know, but in this code there was another loop called 'handleMessage' and that caused an error.
<img width="300" alt="error1_googlec" src="https://user-images.githubusercontent.com/90249238/198130482-87ed09ad-21a2-48d7-8f93-6de78cccf28b.png">
<img width="300" alt="error2_googlec" src="https://user-images.githubusercontent.com/90249238/198130504-f33e8cc7-3aa7-46ef-adbb-8757819e5740.png">


