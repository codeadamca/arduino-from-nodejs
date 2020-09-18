# arduino-from-nodejs

A basic example of communication between a browser interface and an Arduino. 

This tutorial will walkthrough the process of creating a web interface to control an Arduino. The web interface will include an on and off button that will turn a light on and off on a USB connected Arduino. 

## The Arduino

Using [Arduino Create](https://create.arduino.cc/editor) create the collowing sketch and upload it to your Arduino. 

```csharp
// Define the port for the LED
int lightPin = 2;
 
void setup() 
{ 
  // Initialize the light pin
  pinMode(lightPin, OUTPUT);
  
  // Initialize the Serial
  Serial.begin(9600);
}

void loop() {
  
  // CHeck to see if Serial data is being received
  if (Serial.available() > 0) {
    
    // Create a new string variable to receive Serial data
    String receivedString = "";
    
    // Loop through received data and append to the receivedString variable
    while (Serial.available() > 0) {
      receivedString += char(Serial.read ());
    }
    
    // Print received Serial data
    Serial.println(receivedString);
    
    // Change LED status based on received data
    if(receivedString == "1")
      digitalWrite(lightPin,HIGH);  
    else
      digitalWrite(lightPin,LOW);
    
  }

}
```

The previous code will listen to the serial port for an incoming message. Once a message is received, if the message is a one the light will turn on, if the message is a zero the light will turn off. 

You will need to set up the following circuit using your Arduino:

![Tinkercad Circuit](https://raw.githubusercontent.com/codeadamca/arduino-from-nodejs/master/tinkercad-from-nodejs.png)

[View the Circuit on Tinkercad](https://www.tinkercad.com/things/h0C03Xahv9R )

## Steps

1. Open up a new file and name it variables.php.
2. Add the following code to the new PHP file:

```php
<!doctype html>
<html>
<head>
    <title>Links and Variables</title>
</head>
<body>
    
    <h1>Links and Variables</h1>
    
    <p>Use PHP echo and variables to output the following link information:</p>
        
    <hr>
    
    <?php
    
    $linkName = 'Codecademy';
    $linkURL = 'https://www.codecademy.com/';
    $linkImage = 'https://production.cdmycdn.com/webpack/44e01805165bfde4e6e4322c540abf81.svg';
    $linkDescription = 'Learn to code interactively, for free.';
    
    ?>
        
</body>
</html>
```

3. After the variables are defined use a series of `echo` statements to display the content. For example outputting the `$linkname` might look like this:

```php
<?php

echo '<h1>'.$linkName.'</h1>';

?>
```

> Hint: Add each value from the array one at a time. Test your PHP after each new line of PHP. 

> [More information on PHP variables](https://www.php.net/manual/en/language.variables.variable.php)

## Tutorial Requirements:

* [Visual Studio Code](https://code.visualstudio.com/) or [Brackets](http://brackets.io/) (or any code editor)
* [Filezilla](https://filezilla-project.org/) (or any FTP program)

Full tutorial URL: https://codeadam.ca/learning/php-variables.html

<a href="https://codeadam.ca">
<img src="https://codeadam.ca/images/code-block.png" width="100">
</a>

