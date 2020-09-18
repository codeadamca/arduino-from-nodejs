# arduino-from-nodejs

This tutorial will walkthrough the process of creating a web interface to control an Arduino. The web interface will include an on and off button that will turn a light on and off on a USB connected Arduino. 

## HTML and JavaScript

Create an HTML file called `index.html`. Add the following code:

```javascript
<!doctype html>
<html>
   <head>

      <title>Communicating from Node.js to an Arduino</title>
      <script src='https://cdnjs.cloudflare.com/ajax/libs/socket.io/2.0.4/socket.io.js'></script>

   </head>
   <body>

      <h1>Communicating from Node.js to an Arduino</h1>

      <button id="lightOn">Turn Light On</button>
      <button id="lightOff">Turn Light Off</button>

      <script>

      var socket = io();

      document.getElementById('lightOn').onclick = function() {
        socket.emit('lights', { "status":"1" });
      };

      document.getElementById('lightOff').onclick = function(){               
        socket.emit('lights', { "status":"0" });
      };

      </script>

   </body>
</html>
```

The above code creates a webpage with two buttons. When the buttons are clicked they use JavaScript and Socket.io to send a one or zero message to the Node.js server.

## Node.js Server

Before we setup the Node.js server we need to know the name of the serialport your Arduino is attached to. You can find the name of your serialport, it will look something like `/dev/tty.wchusbserialfa1410`. On a Mac using the Terminal and entering the following command:

```
ls /dev/{tty,cu}.*
```

On a PC you can use the command line and the following command:

```

```

Or you can find the name in [Arduino Create](https://create.arduino.cc/editor) in the drop down menu used to select your Arduino.

Create a file called `app.js` and add the following code:

```javascript
var http = require('http');
var fs = require('fs');
var index = fs.readFileSync( 'index.html');

var SerialPort = require('serialport');
const parsers = SerialPort.parsers;

const parser = new parsers.Readline({
    delimiter: '\r\n'
});

var port = new SerialPort('/dev/tty.wchusbserialfa1410',{ 
    baudRate: 9600,
    dataBits: 8,
    parity: 'none',
    stopBits: 1,
    flowControl: false
});

port.pipe(parser);

var app = http.createServer(function(req, res) {
    res.writeHead(200, {'Content-Type': 'text/html'});
    res.end(index);
});

var io = require('socket.io').listen(app);

io.on('connection', function(socket) {
    
    socket.on('lights',function(data){
        
        console.log( data );
        port.write( data.status );
    
    });
    
});

app.listen(3000);
```

The above code uses Socket.io to listen for a message from the HTML/JavaScript webpage and then simply passes on the message to the connected Arduino. 

> Note: Make sure to change the name of the serialport.

## The Arduino

Using [Arduino Create](https://create.arduino.cc/editor) create the following sketch and upload it to your Arduino. 

```csharp
int lightPin = 2;
 
void setup() 
{ 
  pinMode(lightPin, OUTPUT);
  Serial.begin(9600);
}

void loop() {
  
  if (Serial.available() > 0) {
    
    String receivedString = "";
    
    while (Serial.available() > 0) {
      receivedString += char(Serial.read ());
    }
    
    Serial.println(receivedString);
    
    if(receivedString == "1")
      digitalWrite(lightPin,HIGH);  
    else
      digitalWrite(lightPin,LOW);
    
  }

}
```

The previous code will listen to the serialport for an incoming message. Once a message is received, if the message is a one the light will turn on, if the message is a zero the light will turn off. 

[View the Arduino code on Arduino Create](https://create.arduino.cc/editor/professoradam/af5288bf-00cc-406c-844e-f20485fa2df8/preview)

You will need to setup the following circuit using your Arduino:

![Tinkercad Circuit](https://raw.githubusercontent.com/codeadamca/arduino-from-nodejs/master/tinkercad-from-nodejs.png)

[View the Circuit on Tinkercad](https://www.tinkercad.com/things/h0C03Xahv9R)

## Launch Application

1. Using the Terminal start your Node.js app using `node app.js`.
2. Open up a browser and enter the URL `http://localhost:3000/`.
3. Using [Arduino Create](https://create.arduino.cc/editor) upload the sketch to your Arduino.
4. Using your browser push the on and off buttons and watch your Arduino for a changing light. 

## Tutorial Requirements:

* [Visual Studio Code](https://code.visualstudio.com/) or [Brackets](http://brackets.io/) (or any code editor)
* [Arduino Create](https://create.arduino.cc/editor) 
* [SerialPort NPM](https://www.npmjs.com/package/serialport)
* [Socket.io](https://socket.io/)

Full tutorial URL: https://codeadam.ca/learning/arduino-from-nodejs.html

<a href="https://codeadam.ca">
<img src="https://codeadam.ca/images/code-block.png" width="100">
</a>

