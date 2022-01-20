# esp8266_eth_price_tracker
Track ETH price on embedded oled display using ESP8266 board

Board https://letyshops.com/r/aliexpress-12bee6e891b8

Change in code:

1. WiFi setting
```
const char* ssid     = "<YOUR_SSID>";
const char* password = "<SSID_PWD>";
```

2. API KEY from https://etherscan.io/
```
const char* url = "https://api.etherscan.io/api?module=stats&action=ethprice&apikey=<API_KEY>";
const char* url_gwei = "https://api.etherscan.io/api?module=gastracker&action=gasoracle&apikey=<API_KEY>";
```
