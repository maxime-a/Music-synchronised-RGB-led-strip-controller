# Music synchronised RGB led strip controller

## Working principle

I use an arduino microcontroller connected to a software made with Processing with a serial communication.
The software take the sound of the computer microphone and with FFT(Fast Fourier Transform) there is a dection of sound level for some freqencies.
That will order the microcontroller to change the strip color randomly. 
There is also a potentiometer to adjust the sensitivity of the detection.
