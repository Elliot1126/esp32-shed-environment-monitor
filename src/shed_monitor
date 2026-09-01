#include <WiFi.h>
#include <Wire.h>
#include <Adafruit_SHT31.h>

#include "secrets.h"

WiFiServer server(80);
Adafruit_SHT31 sht31 = Adafruit_SHT31();

void setup() {
  Serial.begin(115200);
  Wire.begin();

  if (!sht31.begin(0x44)) {
    Serial.println("Couldn't find SHT31");
    while (1) {
      delay(10);
    }
  }

  WiFi.begin(WIFI_SSID, WIFI_PASSWORD);

  Serial.print("Connecting to Wi-Fi");

  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print(".");
  }

  Serial.println();
  Serial.println("Wi-Fi connected!");

  Serial.print("ESP32 IP Address: ");
  Serial.println(WiFi.localIP());

  Serial.print("Subnet: ");
  Serial.println(WiFi.subnetMask());

  Serial.print("Gateway: ");
  Serial.println(WiFi.gatewayIP());

  server.begin();
}

void loop() {
  WiFiClient client = server.available();

  if (!client) {
    return;
  }

  Serial.println("Client connected!");

  while (client.connected() && !client.available()) {
    delay(1);
  }

  while (client.available()) {
    client.read();
  }

  float tempC = sht31.readTemperature();
  float tempF = tempC * 9.0 / 5.0 + 32.0;
  float humidity = sht31.readHumidity();
  long wifiSignal = WiFi.RSSI();

  client.println("HTTP/1.1 200 OK");
  client.println("Content-Type: text/html");
  client.println("Connection: close");
  client.println();

  client.println("<!DOCTYPE html>");
  client.println("<html>");

  client.println("<head>");
  client.println("<title>Smart Shed Monitor</title>");
  client.println("<meta name='viewport' content='width=device-width, initial-scale=1'>");
  client.println("<meta http-equiv='refresh' content='5'>");

  client.println("<style>");
  client.println(
      "body { font-family: Arial, sans-serif; background: #e4f2e7; "
      "margin: 0; padding: 20px; text-align: center; }");

  client.println(
      ".container { max-width: 500px; margin: auto; }");

  client.println(
      "h1 { color: #222; margin-bottom: 5px; }");

  client.println(
      ".subtitle { color: #666; margin-bottom: 25px; }");

  client.println(
      ".card { background: white; padding: 20px; margin: 15px 0; "
      "border-radius: 12px; box-shadow: 0 4px 12px rgba(0,0,0,0.1); }");

  client.println(
      ".label { font-size: 16px; color: #666; }");

  client.println(
      ".value { font-size: 34px; font-weight: bold; color: #111; "
      "margin-top: 8px; }");

  client.println(
      ".status { color: green; font-weight: bold; }");

  client.println("</style>");
  client.println("</head>");

  client.println("<body>");
  client.println("<div class='container'>");

  client.println("<h1>Smart Shed Monitor</h1>");
  client.println("<p class='subtitle'>Live ESP32 Dashboard</p>");

  client.println("<div class='card'>");
  client.println("<div class='label'>Temperature</div>");
  client.print("<div class='value'>");
  client.print(tempF, 1);
  client.println(" &deg;F</div>");
  client.println("</div>");

  client.println("<div class='card'>");
  client.println("<div class='label'>Humidity</div>");
  client.print("<div class='value'>");
  client.print(humidity, 1);
  client.println(" %</div>");
  client.println("</div>");

  client.println("<div class='card'>");
  client.println("<div class='label'>Wi-Fi Signal</div>");
  client.print("<div class='value'>");
  client.print(wifiSignal);
  client.println(" dBm</div>");
  client.println("</div>");

  client.println("<div class='card'>");
  client.println("<div class='label'>System Status</div>");
  client.println("<div class='value status'>ONLINE</div>");
  client.println("</div>");

  client.println("</div>");
  client.println("</body>");
  client.println("</html>");

  client.stop();

  Serial.println("Client disconnected.");
}
