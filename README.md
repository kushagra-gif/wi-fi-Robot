Arduino Code:
// Motor Pins
const int motor1Pin1 = 3;
const int motor1Pin2 = 4;
const int motor2Pin1 = 5;
const int motor2Pin2 = 6;

void setup() {
  Serial.begin(9600); // Initialize serial communication with ESP8266
  // Motor pins as output
  pinMode(motor1Pin1, OUTPUT);
  pinMode(motor1Pin2, OUTPUT);
  pinMode(motor2Pin1, OUTPUT);
  pinMode(motor2Pin2, OUTPUT);
}

void loop() {
  if (Serial.available() > 0) {
    char command = Serial.read(); // Read the incoming command
    moveRobot(command); // Move robot based on the command
  }
}

void moveRobot(char command) {
  switch (command) {
    case 'F': // Move Forward
      digitalWrite(motor1Pin1, HIGH);
      digitalWrite(motor1Pin2, LOW);
      digitalWrite(motor2Pin1, HIGH);
      digitalWrite(motor2Pin2, LOW);
      break;
    case 'B': // Move Backward
      digitalWrite(motor1Pin1, LOW);
      digitalWrite(motor1Pin2, HIGH);
      digitalWrite(motor2Pin1, LOW);
      digitalWrite(motor2Pin2, HIGH);
      break;
    case 'L': // Turn Left
      digitalWrite(motor1Pin1, LOW);
      digitalWrite(motor1Pin2, HIGH);
      digitalWrite(motor2Pin1, HIGH);
      digitalWrite(motor2Pin2, LOW);
      break;
    case 'R': // Turn Right
      digitalWrite(motor1Pin1, HIGH);
      digitalWrite(motor1Pin2, LOW);
      digitalWrite(motor2Pin1, LOW);
      digitalWrite(motor2Pin2, HIGH);
      break;
    case 'S': // Stop
      digitalWrite(motor1Pin1, LOW);
      digitalWrite(motor1Pin2, LOW);
      digitalWrite(motor2Pin1, LOW);
      digitalWrite(motor2Pin2, LOW);
      break;
  }
}
ESP8266 Code (Arduino IDE):
#include <ESP8266WiFi.h>

const char* ssid = "your_SSID";
const char* password = "your_PASSWORD";

WiFiServer server(80);

void setup() {
  Serial.begin(9600);
  WiFi.begin(ssid, password);

  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
  }
  
  server.begin();
}

void loop() {
  WiFiClient client = server.available();

  if (client) {
    String request = client.readStringUntil('\r');
    client.flush();

    if (request.indexOf("/FORWARD") != -1) {
      Serial.write('F');
    } else if (request.indexOf("/BACKWARD") != -1) {
      Serial.write('B');
    } else if (request.indexOf("/LEFT") != -1) {
      Serial.write('L');
    } else if (request.indexOf("/RIGHT") != -1) {
      Serial.write('R');
    } else if (request.indexOf("/STOP") != -1) {
      Serial.write('S');
    }

    // HTML to display buttons
    client.print("HTTP/1.1 200 OK\r\nContent-Type: text/html\r\n\r\n");
    client.print("<!DOCTYPE HTML><html><body>");
    client.print("<h2>WiFi Controlled Robot</h2>");
    client.print("<button onclick=\"location.href='/FORWARD'\">FORWARD</button><br>");
    client.print("<button onclick=\"location.href='/BACKWARD'\">BACKWARD</button><br>");
    client.print("<button onclick=\"location.href='/LEFT'\">LEFT</button>");
    client.print("<button onclick=\"location.href='/RIGHT'\">RIGHT</button><br>");
    client.print("<button onclick=\"location.href='/STOP'\">STOP</button>");
    client.print("</body></html>");
  }
}
