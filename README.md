//# Generative-Sound-Mapping
//From rhttp://responsivedesign.de/wp-content/uploads/2016/05/tutorial-06_processing-soundmapping2.pdf
import ddf.minim.*; 
import processing.pdf.*;
import java.util.Calendar;

Minim minim;
AudioPlayer song;
AudioMetaData meta;

int spacing = 5;
int border = 20;
int amplification = 40;
int num = 100;
int cutBack = 20000;
int cutFront = 10000;
int pos, counter;

float[] x = new float[num];
float[] y = new float[num];

void setup() {
  size(2000, 2000);
  minim = new Minim(this);
  song = minim.loadFile("bike.mp3");
  meta = song.getMetaData();
  
  song.play();
  song.cue(cutFront);
  
  background(255);
  noFill();
  strokeWeight(1);
  stroke(0);
}

void draw() {
  beginShape();
  x[0] = pos + border;
  y[0] = border;
  curveVertex(x[0], y[0]);
  for (int i = 0; i < num; i++) {
    x[i] = pos + border + song.mix.get(i)*amplification;
    y[i] = map( i, 0, num, border, height-border );
    curveVertex(x[i], y[i]);
  }
  x[num-1] = x [0];
  y[num-1] = height-border;
  curveVertex(x[num-1], y[num-1]);
  endShape();
  
  int skip = (song.length() - cutFront - cutBack) / ((width-2*border) / spacing);
  
  if (pos + border < width-border) {
    song.skip(skip);
    pos += spacing;
  } else {
    minim.stop();
  }
  position();
  if (song.isPlaying() == false) endRecord();
}

void position() {
 
   int totalSeconds = (int)(song.length()/1000) % 60;
   int totalMinutes = (int)(song.length()/(1000*60)) % 60;
   int playheadSeconds = (int)(song.position()/1000) % 60;
   int playheadMinutes = (int)(song.position()/(1000*60)) % 60;
   String info = playheadMinutes + ":" + nf(playheadSeconds, 2) + "/" + totalMinutes + ":" + nf(totalSeconds, 2);
   println(info);
}

void keyReleased() {
  if (key == 's' || key =='S') saveFrame(timestamp()+"_##.png");
  if ((song.isMuted() == false && key == ' ')) song.mute();
  else if ((song.isMuted() == true && key == ' ')) song.unmute();
}

String timestamp() {
  Calendar now = Calendar.getInstance();
  return String.format("%1$tH%1$tM%1$tS", now);
}
