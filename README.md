# Resume
My complete resume
#include <ESP8266WiFi.h>

const char* ssid = "YOUR_WIFI_NAME";
const char* password = "YOUR_WIFI_PASSWORD";

WiFiServer server(80);

int relayPin = D1;  // GPIO5

void setup() {
  Serial.begin(115200);
  pinMode(relayPin, OUTPUT);
  digitalWrite(relayPin, HIGH); // Relay OFF (Active LOW)

  WiFi.begin(ssid, password);
  Serial.print("Connecting to WiFi");

  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print(".");
  }

  Serial.println("");
  Serial.println("WiFi connected");
  Serial.print("IP Address: ");
  Serial.println(WiFi.localIP());

  server.begin();
}

void loop() {
  WiFiClient client = server.available();
  if (!client) {
    return;
  }

  Serial.println("New Client");
  String request = client.readStringUntil('\r');
  Serial.println(request);
  client.flush();

  if (request.indexOf("/ON") != -1) {
    digitalWrite(relayPin, LOW);  // Turn ON Light
  }

  if (request.indexOf("/OFF") != -1) {
    digitalWrite(relayPin, HIGH); // Turn OFF Light
  }

  // Webpage
  client.println("HTTP/1.1 200 OK");
  client.println("Content-Type: text/html");
  client.println("");
  client.println("<!DOCTYPE HTML>");
  client.println("<html>");
  client.println("<h1>IoT Light Control</h1>");
  client.println("<a href=\"/ON\"><button>Turn ON</button></a>");
  client.println("<a href=\"/OFF\"><button>Turn OFF</button></a>");
  client.println("</html>");

  delay(1);
  Serial.println("Client disconnected");
}
