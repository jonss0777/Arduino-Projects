/*************************************************

Small sample to dynamically updating sensor variables using an App Script

Sensor -> request(sends POST)
Sensor <- response(Appscript sends a ContentService object)

Content Service object is a redirect and needs to be handled by the sensor.

*************************************************/
// Dependencies
#include <SPI.h>
#include <WiFi101.h>

// Global variables
String samplingRate = "NaN";
char server_google_script[] = "script.google.com"; 
char server_google_usercontent[] = "script.googleusercontent.com"; 

char ssidg[] = "<SSID>";
char passcodeg[] = "<PASSWORD>";

String payload = "{\"command\":\"appendRow\",\"sheet_name\":\"Sheet1\",\"values\":\"demo,1,2,2,3\"}";
char gsidg[] = "<GOOGLESHEETID>";
uint8_t status; 
WiFiSSLClient client;   

/*!
* @ brief http post method to google App Script
*
*/
void httpPost(char ssidg[]){
    client.println(String("POST /macros/s/") + String(gsidg) + String("/exec HTTP/1.1"));
    client.println("Host: script.google.com");
    client.println("Content-Type: application/json");
    client.print("Content-Length: ");
    client.println(payload.length());
    client.println();
    client.print(payload);
    client.println();
    delay(2000);
}

/*!
* @brief http get method to redirect 
* 
*/
void httpGet(String fullUrl) {
  // Extract path and query from full URL
  int pathIndex = fullUrl.indexOf(".com");
  if (pathIndex == -1) {
    Serial.println("Invalid URL format");
    return;
  }
  String pathAndQuery = fullUrl.substring(pathIndex + 4); // skip ".com"

  client.println("GET " + pathAndQuery + " HTTP/1.1");
  client.println("Host: script.googleusercontent.com");
  client.println();  // End of headers
  delay(2000);
}



/*!
* @brief handle redirect 
*
*/
void handleRedirect(String response){
  // Step 1: Find the start of the "Location: " line
  int locIndex = response.indexOf("Location: ");
  if (locIndex != -1) {
    // Step 2: Find the end of the line (newline character after the URL)
    int endIndex = response.indexOf('\n', locIndex);
    if (endIndex == -1) endIndex = response.length(); // fallback

    // Step 3: Extract and clean the URL
    String locationURL = response.substring(locIndex + 9, endIndex); // +9 = length of "Location: "
    locationURL.trim(); // Remove \r or spaces

    Serial.println("Redirect URL:");
    Serial.println(locationURL);

    Serial.println("Call Get to redirect: ");
    delay(10000);
    httpGet(locationURL);
  } else {
    Serial.println("No Location header found.");
  }
}


/*!
* @brief start connection to google App Script
* @param
*/
void initializeClient(char server[]) {
  Serial.println("\nStarting connection to server... ");
  if (client.connectSSL(server, 443)) {     
    Serial.println("Connected to ");
    Serial.println(server);
    while (client.available()) {
      char c = client.read();
      Serial.write(c);
    }
  }
  else {
    Serial.print("Not connected to ");
    Serial.println(server);
  }
  Serial.println("end intializeClient");
}


/*!
* @brief upload sensors data
* @param 
*/
void payloadUpload(String payload) {

  for (int i = 0; i < 5; i++) {
    status = WiFi.begin(ssidg, passcodeg);
    int retry = 0;
    while (WiFi.status() != WL_CONNECTED && retry++ < 10) {
      delay(1000);
    }
    if (WiFi.status() == WL_CONNECTED) break;
  }
  
    if (WiFi.status() == WL_CONNECTED) {

      if (!client.connected()) {
        Serial.println("Connect to POST server");
        initializeClient(server_google_script);
      }
      Serial.print("payload: ");
      //payload = payload + String("\"}");
      Serial.println(payload);
      httpPost(gsidg);
      if (client.available())
        Serial.println("POST Response: ");

      String response = "";
      while (client.available()) {
        char c = client.read();
        Serial.write(c);
        response += c;
      }

      if (response.indexOf("302 Moved Temporarily") != -1) {
        Serial.println();
        Serial.println("POST response Received... ");
      } else {
        Serial.println();
        Serial.println("POST Something went wrong...");
      }
      Serial.println();
     
      client.stop();
       if (!client.connected()) {
        Serial.println("disconnected from server");
      };
  
      delay(1000);

      if (!client.connected()) {
        Serial.println("Connect to GET server");
        initializeClient(server_google_usercontent);
      }

      Serial.println("Handle Response(Redirect):");

      handleRedirect(response);
      Serial.println();

      if (client.available())
        Serial.println("GET Response: ");

      // TODO: PARSE THE GET RESPONSE 
      // UPDATATE sampling rate
      response = "";
      while (client.available()) {
        char c = client.read();
        Serial.write(c);
        response += c;
      }


      if (response.indexOf("200 OK") != -1) {
        Serial.println();
        Serial.println("GET response Received... ");
      } else {
        Serial.println();
        Serial.println("GET Something went wrong...");
      }
      Serial.println();

      client.stop();
      if (!client.connected()) {
        Serial.println("disconnected from server");
      };
      WiFi.end();
    
    } 
    else{ //(status != WL_CONNECTED)
      Serial.println("Continuing without WiFi");
    }
}

void setup() {
  Serial.begin(9600);
  // Pins for Adafruit M0 Feather WIC1500
  WiFi.setPins(8,7,4,2);
}

void loop() {
  
  payloadUpload(payload);
  delay(1000000);

}
