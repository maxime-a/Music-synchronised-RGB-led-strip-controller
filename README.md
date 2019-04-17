# Music synchronised RGB led strip controller

<iframe width="560" height="315" src="https://www.youtube.com/embed/AieWlq01SQo" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
Demo video :
https://www.youtube.com/watch?v=AieWlq01SQo

<iframe width="560" height="315" src="https://www.youtube.com/embed/AieWlq01SQo" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

## Working principle

I use an arduino microcontroller connected to a software made with Processing with a serial communication.
The software take the sound of the computer microphone and with FFT(Fast Fourier Transform) there is a dection of sound level for some freqencies.
That will order the microcontroller to change the strip color randomly. 
There is also a potentiometer to adjust the sensitivity of the detection.

## Wiring

![Wiring](/Wiring.PNG)

## Case

![Case](/3D printed case design.PNG)

## Processing code

```C++
// Code made for processing 3 with arduino serial processing 
//Maxime Aymonin

import ddf.minim.*;  
import ddf.minim.analysis.*;
import processing.serial.*;
import cc.arduino.*;

Arduino arduino;
  
Minim minim;  
//AudioPlayer song;
AudioInput in;
FFT fft;

int redPin1 = 9;
int greenPin1 = 11;
int bluePin1 = 10;

int pot = 0;
float sensi = 0;

int common_cathode = 0;


int r = 0;
int g = 0;
int b = 0;

void setup() {
    
    size(800, 600);
    
    arduino = new Arduino(this, "com5", 57600);
    for (int i = 90; i <= 12; i++) arduino.pinMode(i, Arduino.OUTPUT);
    arduino.pinMode(pot, Arduino.INPUT);

    minim = new Minim(this);  
    // activate if you have mp3 files to play
    //song = minim.loadFile("Kool  The Gang - Get Down On It.mp3");
    in = minim.getLineIn();
    //song.play();
    fft = new FFT(in.bufferSize(), in.sampleRate());    
}
 
void draw() {  
  
  
  
    float value = arduino.analogRead(pot);
    sensi = map(value, 512, 1023, 2 , 5);
    
    background(#151515);

    fft.forward(in.mix);

    strokeWeight(1.3);
    stroke(#FFF700);

    // frequency
    pushMatrix();
      translate(250, 0);   
      for(int i = 0; i < 0+fft.specSize(); i++) {
        line(i, height*4/5, i, height*4/5 - fft.getBand(i)*4); 
        if(i%100==0) text(fft.getBand(i), i, height*4/5+20);
        if(i==200) {
          if(fft.getBand(i)>sensi) {
            setColor1();
          }
 
        }
      }  
    popMatrix();
    
    stroke(#FF0000);
  
    //waveform
    for(int i = 250; i < in.left.size() - 1; i++) {
      line(i, 50 + in.left.get(i)*50, i+1, 50 + in.left.get(i+1)*50);
      line(i, 150 + in.right.get(i)*50, i+1, 150 + in.right.get(i+1)*50);
      line(i, 250 + in.mix.get(i)*50, i+1, 250 + in.mix.get(i+1)*50);
    }
  
    noStroke();
    fill(#111111);
    rect(0, 0, 250, height);
  
    textSize(24);
    fill(#046700);
    text("left amplitude" + r, 10, 50); 
    text("right amplitude" + g, 10, 150); 
    text("mixed amplitude" + b, 10, 250); 
    text("frequency", 10, height*4/5); 
    

}

void stop()
{
    for (int i = 0; i <= 13; i++) arduino.digitalWrite(i,arduino.HIGH);
    in.close();  
    minim.stop();
    super.stop();
}
void setColor1()
{
  
  
  
    r = int(random(0,255))*int((in.mix.get(0)*10));
  
    g = int(random(0,255));
    
    b = int(random(0,255));
  
  arduino.analogWrite(redPin1, r);
  arduino.analogWrite(greenPin1, g);
  arduino.analogWrite(bluePin1, b);  
}
```
