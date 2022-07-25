# ESP8266-NodeMCU-Relay-Module
### why we used NodeMCU low cost, integrated support for WiFi networks, a smaller board size, and lower energy
### This project you can be established smart home or Control you car anything you need control by wifi (less than 250v and 10A if you need power over that can be change relay v.) only go to start this project for small project.


   [![Watch the video](https://img.youtube.com/vi/eSm8LEGhvn8/0.jpg)](https://youtu.be/eSm8LEGhvn8)
   
            #include <SoftwareSerial.h>
            #include <ESP8266WiFi.h>        // Include the Wi-Fi library
            #include <ESP8266WebServer.h>
            
             //Server name and password
             const char* ssid = "Wonder developer"; //your wifi name
             const char* password = "youtubechannel";  //Enter Password here
             ESP8266WebServer server(80);


             String relay1State = "off";
             String relay2State = "off";

             #define relay1 D0
             #define relay2 D1
             
             void setup() 
             {
               // Initialize the output variables as outputs
               pinMode(relay1, OUTPUT);
               pinMode(relay2, OUTPUT);

              // Set outputs to HIGH. relay active LOW
              digitalWrite(relay1, LOW);
               digitalWrite(relay2, LOW);

  
                // put your setup code here, to run once:
                Serial.begin(115200);  
                Serial.println();
                Serial.print("Connecting to ");

               // Server Start code
               WiFi.mode(WIFI_AP);
               WiFi.softAP(ssid, password);
               IPAddress myIP = WiFi.softAPIP();
               Serial.print("AP IP address: ");
               Serial.println(myIP);
              // Starting WEB-server
               server.begin();
               Serial.println("HTTP server started");



              server.on("/", handle_OnConnect);

              server.onNotFound(handle_NotFound);

               server.begin();
               Serial.println("HTTP server started");
 
             }

             void loop()
              {
                server.handleClient();
              }
              
           void handle_OnConnect() 
              {
               server.send(200, "text/html", SendHTML(false)); 
              }

          void handle_NotFound()
              {
               server.send(404, "text/plain", "Not found");
              }

           String SendHTML(uint8_t relayes){

             String value = server.arg("value"); //this lets you access a query param (http://x.x.x.x/action1?value=1)
             int number = value.toInt();

              if(number == 0) 
                {
                   Serial.println("relay1 on");
                   relay1State = "on";
                   digitalWrite(relay1, LOW);
                }
             else if (number == 1)
               {
                  Serial.println("relay1 off");
                  relay1State = "off";
                  digitalWrite(relay1, HIGH);
               }
             else if (number == 2) 
               {
                  Serial.println("relay2 on");
                  relay2State = "on";
                  digitalWrite(relay2, LOW);
               }
              else if (number == 3)
              {
                Serial.println("relay2 off");
                relay2State = "off";
                 digitalWrite(relay2, HIGH);
              }


             String ptr = "<!DOCTYPE html>\n";
             ptr += "<head><meta name='viewport' content='width=device-width, initial-scale=1'> <title>RELAY Control</title> <style>\n";
             ptr += "<style>html, body { font-family: Helvetica; display: block; margin: 0px auto; text-align: center;}";
             ptr += ".button { background-color: #209e48; border: none; color: white; padding: 12px 24px;";
             ptr += "text-decoration: none; font-size: 20px; margin: 2px; cursor: pointer;}";
             ptr += ".button2 {background-color: #c20a0a;}";
             ptr += ".textbox {width: 80px; border: 1px solid #333; padding: 16px 20px 0px 24px; background-image: linear-gradient(180deg, #fff, #ddd 40%, #ccc);}";
             ptr+= ".mytext {font-size: 16px; font-weight:bold; font-family:Arial ; text-align: justify;}";
             ptr += "#container {width: 100%; height: 50px; margin-left: 5px; margin-top: 20px; padding: 10px; display: -webkit-flex; -webkit-justify-content: center;                display: flex; justify-content: center;} ";

             ptr += "a:link {background-color: YELLOW;text-decoration: none;}";
             ptr += "table, th, td </style> </head> <body> \n";
             ptr += "<h1> Control</h1>";
      
             ptr += "<div id='container'>";
             ptr += "<p><div class='textbox mytext'>RELAY 1 </div> ";
            // If the relay1State is off, it displays the ON button
            if (relay1State == "off") {
              ptr += "<a href='?value=0'><button class='button'>OFF</button></a></p>";
            } else {
              ptr += "<a href='?value=1'><button class='button button2'>ON</button></a></p>";
            }
            ptr += "</div>";
            // Display current state, and ON/OFF buttons for GPIO 4
            ptr += "<div id='container'>";
            ptr+= "<p><div class='textbox mytext'>RELAY 2 </div> ";
            // If the relay2State is off, it displays the ON button
            if (relay2State == "off") {
            ptr += "<a href='?value=2'><button class='button'>OFF</button></a></p>";
            } else {
             ptr+= "<a href='?value=3'><button class='button button2'>ON</button></a></p>";
            }
            ptr += "</div>";
 
            ptr +="</body>\n";
            ptr +="</html>\n";
             return ptr;
           }




