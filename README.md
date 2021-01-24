# Anything-Enter-Key

## Overview
This is an Arduino sketch that uses Digispark to create a touch-sensitive Enter key.
Wiring is easy, just connect a 1M ohm resistor to pins 0 and 2 and a 0.1μF capacitor in series with pin 2.
Connect a non-insulator (metal, fruit, etc.) to the end of the capacitor (this is the touch sensor).
Then connect Digispark to your computer via USB. When you touch the touch sensor, it acts as an enter key.

## Circuit
![Circuit](https://github.com/naoto64/Anything-Enter-Key/blob/master/enter-key-circuit.png)

## Sketch
```c++:Anything-Enter-Key.ino
#include <DigiKeyboard.h>

#define REPEAT_TIME 25
#define FIRST_REPEAT_TIME 500
#define KEYUP_THRESHOLD 50
#define KEYDOWN_THRESHOLD 75
#define OUT_PIN 0
#define IN_PIN 2

unsigned long timer = 0;
int keytimes = 0;

void setup() {
  pinMode(OUT_PIN, OUTPUT);
  pinMode(IN_PIN, INPUT);
}

int get_touch() {
  int t = 0;
  int i;
  for(i = 0; i < 10; i++){
    digitalWrite(OUT_PIN, HIGH);
    while(digitalRead(IN_PIN) != HIGH) t++;
    digitalWrite(OUT_PIN, LOW);
    pinMode(IN_PIN, OUTPUT);
    digitalWrite(IN_PIN, LOW);
    DigiKeyboard.delay(1);
    pinMode(IN_PIN, INPUT);
  }
  return t;
}

void loop() {
  int touch = get_touch();
  if(touch > KEYDOWN_THRESHOLD && (timer == 0 || keytimes >= 2)){
    DigiKeyboard.sendKeyStroke(0x28);
    DigiKeyboard.delay(REPEAT_TIME);
    keytimes = min(keytimes + 1, 2);
    timer = millis();
  }else{
    if(touch <= KEYUP_THRESHOLD){
      timer = 0;
      keytimes = 0;
    }
    DigiKeyboard.sendKeyStroke(0);
    DigiKeyboard.delay(REPEAT_TIME);
  }
  if(millis() >= timer + FIRST_REPEAT_TIME){
    timer = 0;
  }
}
```
