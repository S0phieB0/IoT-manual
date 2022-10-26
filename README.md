# IoT-manual
For Internet of Thinks I made a new idea for a diffuser. In this manual I'll try to make the internet part of the diffuser that i thought of.

**Name**: Sophie Bouman
**Date**: 25 oct. 2022

## Supplies
- Arduino IDE
- ESP8266
- LED lights

## Step 1 - Getting the time from the internet
For my diffuser i need a light to automaticly turn off when it's time to sleep.

### 1.1 source
I looked at this website to find out how to do it: https://lastminuteengineers.com/esp8266-ntp-server-date-time-tutorial/

### 1.2 Add ntpclient to library
So you go to **Sketch > Inlcude Library > Manage Librarues**
Search for '**ntpclient**' by Fabrice Weinberg and instal it.

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

# step 2 - Linking your google calendar to ESP8266
For my diffuser want to be able to link google calendar to it so that the diffuser goes on at the right time.

### 2.1 source
I looked at this website to find out how to do it: https://www.instructables.com/Google-Calendar-Events-to-ESP8266/

### 2.2 Adafruit feed
I already made an Adafruit account, if you don't have one you need to make one first.
- Go to https://io.adafruit.com/
- Go to **Feeds** and click on **New feed**
- Give it a name so you can easily recognize it
- Open the feed and see that its empty, you will later send data to it using Zapier.

### 2.3 Make a Zap in Zappier
...

