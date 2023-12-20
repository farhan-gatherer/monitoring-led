## About Laravel and NodeMCU ESP8266

This project for your testing connection between NodeMCU ESP8266 to Laravel as server. Focus point in this project is how to make connection between NodeMCU ESP8266 to Laravel as server therefore in this project just make a switch for turnon / turnoff led. this project made using JQuery for AJAX to server, MySQL for database and Bootstrap for UI. lets install this project before you run.

## Initialitation

-   run XAMPP (`Apache` and `MySQL`)

## How to Install

1. Clone this repository into your directory (Free as you wish)
2. rename this project according to your wishes
3. cd `this project`
4. Run `composer update`
5. In Terminal `cp .env.example .env`
6. Generate the application key using `php artisan key:generate`
7. Set your .env file
8. Run database migrations and seed the database using `php artisan migrate --seed`

## Running

1. Split your Terminal
2. In Terminal `npm i`
3. In one side run `php artisan serve --host=0.0.0.0` and another `npm run dev`
4. Your search Ip Address `ipconfig` in terminal
5. Usually you will got URL `(ipaddress):8000` and open that URL in your browser

## Code Program from Arduino Uno

-   declare define led as `led` for your Pin LED
-   declare variabel `*ssid` and `*password` your Wifi Connection
-   declare variabel host as `const char* host` for your IP

```

#include <ESP8266WiFi.h>
#include <ESP8266HTTPClient.h>
#include <map>

#define led D0

const char *ssid = "Wifi Name";
const char *password = "Wifi Pass";
String host = "ip";
String port = ":8000";
String BASE_URL = "http://" + String(host) + String(port) + "/api";
WiFiClient client;
HTTPClient http;

void setup() {
  pinMode(led, OUTPUT);
  Serial.begin(115200);
  WiFi.begin(ssid, password);

  Serial.print("Connecting to WiFi");
  while (WiFi.waitForConnectResult() != WL_CONNECTED) {
    Serial.println("Wifi Gagal Terhubung Dengan Arduino");
  }

  Serial.println();
  Serial.println("Connected to WiFi");
}

void loop() {
  sendRequest();
}

void sendRequest() {
  String url = BASE_URL + "/led";
  http.begin(client, url);
  http.setTimeout(5000);

  int httpCode = http.GET();

  if (httpCode > 0) {
    if (httpCode == HTTP_CODE_OK) {
      String response = http.getString();
      Serial.println("Response from server: " + response);

      std::map<String, int> responseMap = {{"1", HIGH}, {"0", LOW}};

      if (responseMap.find(response) != responseMap.end()) {
        digitalWrite(led, responseMap[response]);
      } else {
        Serial.println("Invalid response from server");
      }

    } else {
      Serial.println("HTTP request failed with error code: " + String(httpCode));
    }
  } else {
    Serial.println("Connection failed");
  }

  http.end();

  delay(2000);
}

```
