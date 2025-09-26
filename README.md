#define PIN_LED  9
#define PIN_TRIG 12
#define PIN_ECHO 13

// configurable parameters
#define SND_VEL 346.0
#define INTERVAL 25
#define PULSE_DURATION 10
#define _DIST_MIN 100.0
#define _DIST_MAX 300.0

#define TIMEOUT ((INTERVAL / 2) * 1000.0) 
#define SCALE (0.001 * 0.5 * SND_VEL)     

unsigned long last_sampling_time;   

void setup() {
  pinMode(PIN_LED, OUTPUT);
  pinMode(PIN_TRIG, OUTPUT);  
  pinMode(PIN_ECHO, INPUT);   
  digitalWrite(PIN_TRIG, LOW);  
  
  Serial.begin(57600);
}

void loop() { 
  if (millis() < (last_sampling_time + INTERVAL))
    return;

  float distance = USS_measure(PIN_TRIG, PIN_ECHO); 

  int brightness = 255; 

  if ((distance == 0.0) || (distance > _DIST_MAX) || (distance < _DIST_MIN)) {
      brightness = 255;  
  } else {
      if (distance <= 200) {
          brightness = map(distance, 100, 200, 255, 0);
      } else {
          brightness = map(distance, 200, 300, 0, 255);
      }
  }

  analogWrite(PIN_LED, brightness);

  
  Serial.print("distance: ");  
  Serial.print(distance);  
  Serial.print(" mm, brightness: ");  
  Serial.println(brightness);
  
  last_sampling_time += INTERVAL;
}


float USS_measure(int TRIG, int ECHO)
{
  digitalWrite(TRIG, HIGH);
  delayMicroseconds(PULSE_DURATION);
  digitalWrite(TRIG, LOW);
  
  return pulseIn(ECHO, HIGH, TIMEOUT) * SCALE; 
}
