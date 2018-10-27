# visual-synth
A visual synthesizer that takes real world input (through the input mic or the line in jack and uses it to remix visuals

//allows for buttons
import controlP5.*;
//allows for video
import processing.video.*;
//allows for sound 
import processing.sound.*;
//lets in synthesizer
import ddf.minim.*;

ControlP5 gui; 
ControlP5 cp5;

float diameter = 10.0f;
float FILTER;
float RED;
float BLUE;
float GREEN;
float slider = 255;
int shape = 0;


Capture video;
AudioIn input;
Amplitude loudness;
Minim minim;
//connects processing to Synth via computers line-in, works off loudness like the mic input
AudioInput in;
AudioRecorder recorder;

void setup() {
  size(900,600);
 //from processing visual library
  video = new Capture(this,640,480,15);
  video.start();
  minim = new Minim(this);
  
  //from processing audio library
      input = new AudioIn(this, 0);
  input.start();
         loudness = new Amplitude(this);
  loudness.input(input);
  //from minim library, minim is a synthesizer library
  in = minim.getLineIn();
  recorder = minim.createRecorder(in, "myrecording.wav");

  background(0);

//sliders that let me control the colours in the program, ideally these would be connected to hardware sliders that 
//could control manually using ardunio 
  gui = new ControlP5(this);
  gui.addSlider("RED")
     .setPosition(480,500)
     .setRange(1.0f,255.0f)
     .setValue(255.0f);  
     
      gui = new ControlP5(this);
  gui.addSlider("GREEN")
     .setPosition(610,500)
     .setRange(1.0f,255.0f)
     .setValue(255.0f);
     
       gui = new ControlP5(this);
  gui.addSlider("BLUE")
     .setPosition(750,500)
     .setRange(1.0f,255.0f)
     .setValue(255.0f);
  

  
  noStroke();
      cp5 = new ControlP5(this);
//these buttons are generated with the p5 library, you use them to scroll between the mic and synth inputs
 cp5.addButton("THRESHOLD")
     .setValue(0)
     .setPosition(50,20)
     .setSize(170,85);
     
      cp5.addButton("MICINPUT")
     .setValue(100)
     .setPosition(50,148.5)
     .setSize(170,85);

  cp5.addButton("SYNTHINPUT")
     .setPosition(50,278)
     .setSize(170,85)
     .setValue(0);
     
       cp5.addButton("RESET")
     .setPosition(50,398)
     .setSize(170,42)
     .setValue(0);
     
}
//connects to the webcam, in my alt version of this processing sketch I connect to my android phones camera for input
void captureEvent(Capture video) {
video.read();
}
  
void draw() {

  // loudness.analyze() return a value between 0 and 1, this feeds into the filter, providing the movement in the colours 
  float volume = loudness.analyze();
   // this line of code plays around with the colours, you get different colour variations by playing with it
  int loudness = int(map(volume, 0, 10.5, 2, 250));

  background(0);
  stroke(255);
  
  // draws the waveforms that visualize the music
  for(int i = 0; i < in.bufferSize() - 1; i++)
  {
    line(i, 525 + in.left.get(i)*50, i+1, 525 + in.left.get(i+1)*50);
    line(i, 575 + in.right.get(i)*50, i+1, 575 + in.right.get(i+1)*50);
  }
 //provides the raw image when the program is launched
  image(video,270,0);
  
    if(shape == 1) {
    tint(255,FILTER,FILTER);
    filter(THRESHOLD);
  } else if (shape == 2) {
     tint(RED,GREEN,BLUE);
     //the posterize filter with the loudness variable is the backbone of this program
     filter(POSTERIZE,loudness);;
  }else if (shape == 3) {
     tint(RED,GREEN,BLUE);
     filter(POSTERIZE, loudness);;
  }else if (shape == 4) {
     tint(255,255,255);
     image(video,270,0);;
   }
}

void THRESHOLD() {
  shape = 1;
}

void MICINPUT() {
  shape = 2;
}

void SYNTHINPUT() {
  shape = 3;
}

void RESET() {
  shape = 4;
}
