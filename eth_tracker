#include <Wire.h>
#include <Adafruit_GFX.h>
#include <Adafruit_SSD1306.h>
#include <ESP8266WiFi.h>
#include <ESP8266HTTPClient.h>
#include <WiFiClientSecure.h>
#include <Arduino_JSON.h>

// Определяем размеры матрицы
#define WIDTH 128
#define HEIGHT 64

// Определяем выводы к которым подключена OLED матрица
const uint8_t disp_SDA = 5;
const uint8_t disp_SCL = 4;

// WiFi network
const char* ssid     = "<YOUR_SSID>";
const char* password = "<SSID_PWD>";

unsigned long lastTime = 0;
// oled update time
unsigned long timerDelay = 60000;

// url settings
const char* url = "https://api.etherscan.io/api?module=stats&action=ethprice&apikey=<API_KEY>";
const char* url_gwei = "https://api.etherscan.io/api?module=gastracker&action=gasoracle&apikey=<API_KEY>";
String ethscanData;
int type;

// Создаём объект дисплея
Adafruit_SSD1306 display(WIDTH, HEIGHT, &Wire, -1);

void setup() {
  
  Serial.begin(115200);
  
  connectToWiFi();
  
  Wire.begin(disp_SDA, disp_SCL);
  if(!display.begin(SSD1306_SWITCHCAPVCC, 0x3C, false, false)) {
    Serial.println(F("Can't connect SSD1306"));
    for(;;);
  }

  display.clearDisplay();   
  display.setTextSize(1);
  display.setTextColor(WHITE);
  display.setCursor(10, 15);
  // Display static text
  display.println("IoT ETH Price Info");
  display.setCursor(10, 30);
  display.println("(c) 2022 @pazhukov");
  display.display(); 

  type = 0;
}

void connectToWiFi() {
  WiFi.mode(WIFI_STA);
  WiFi.begin(ssid, password);
  while (WiFi.status() != WL_CONNECTED) {
    delay(1000);
  }
}

String httpGETRequest(const char* serverUrl) {
  WiFiClientSecure client;
  HTTPClient http;
  String payload = "{}";

  client.setInsecure();
  client.connect(serverUrl, 443);
    
  http.begin(client, serverUrl);
  
  int httpResponseCode = http.GET();   
  if (httpResponseCode > 0) {
    payload = http.getString();
  }
  http.end();

  return payload;
}

void loop() {
  if ((millis() - lastTime) < timerDelay) {
    return;
  }
  
  if(WiFi.status()== WL_CONNECTED){

    if(type == 0) {
      // eth price
      ethscanData = httpGETRequest(url);
      JSONVar jsObj = JSON.parse(ethscanData);
  
      if (JSON.typeof(jsObj) == "undefined") {
        return;
      }
      
      //Serial.println(jsObj); 
      const char* eth = jsObj["result"]["ethusd"]; 
  
      display.clearDisplay();
      display.drawRoundRect(0, 0, 127, 63, 2, WHITE);
      display.setTextSize(1);
      display.setTextColor(WHITE);
      display.setCursor(8,10);
      display.println("ETH/USD Price Info");
      display.setTextSize(2);
      display.setCursor(22, 32);
      display.println(eth);
      display.display();  
  
      type = 1;
          
    } else if(type == 1) {
  
      ethscanData = httpGETRequest(url_gwei);
      JSONVar jsObj = JSON.parse(ethscanData);
  
      if (JSON.typeof(jsObj) == "undefined") {
        return;
      }
      
      //Serial.println(jsObj); 
      const char* gwei = jsObj["result"]["SafeGasPrice"]; 
  
      display.clearDisplay();
      display.drawRoundRect(0, 0, 127, 63, 2, WHITE);
      display.setTextSize(1);
      display.setTextColor(WHITE);
      display.setCursor(34,10);
      display.println("Gas Price");
      display.setTextSize(2);
      display.setCursor(40, 32);
      display.println(gwei);
      display.display();

      type = 0;
      
    }  

  }

  lastTime = millis();
}
