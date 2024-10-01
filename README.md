int MotorL1 = 6;
int MotorL2 = 7;

int MotorR1 = 8;
int MotorR2 = 9;

int buzzer = 11;

const int trigPin = 5;
const int echoPin = 4;

int data;

#define VCC2  3
#define GND2  2


long distance;
int duration;

void setup() 
{
  Serial.begin(9600);

  pinMode(A1, INPUT);
  pinMode(trigPin, OUTPUT); // Sets the trigPin as an Output
  pinMode(echoPin, INPUT);

  pinMode(VCC2, OUTPUT);
  pinMode(GND2, OUTPUT);

  digitalWrite(VCC2, HIGH);
  digitalWrite(GND2, LOW);

  Serial.begin(9600);
  
  pinMode(MotorL1, OUTPUT);
  pinMode(MotorL2, OUTPUT);
  pinMode(MotorR1, OUTPUT);
  pinMode(MotorR2, OUTPUT); 
  
  Forward();
  delay(1000);
  Backward();
  delay(1000);
  Left();
  delay(1000);
  Right();
  delay(1000);
  Stop();
  
}

void loop() 
{

  int s = analogRead(A1);
  float voltage = s * (5.0 / 1023.0);
  float rs = ((5.0 * 10.0) / voltage) - 10.0;
  float ratio = rs / 76.63;
  
  double a = 116.6020682;
  double b = -2.769034857;
  double PPM = a * pow(ratio, b);
  double PPMS = PPM / 7.636;

  Serial.print("CO2 PPM : ");
  Serial.println(PPMS);

  digitalWrite(trigPin, LOW);
  delayMicroseconds(2);

  digitalWrite(trigPin, HIGH);
  delayMicroseconds(10);
  digitalWrite(trigPin, LOW);
  
  duration = pulseIn(echoPin, HIGH);
  
  distance = duration * 0.034 / 2;
  
  if(distance < 10)
  {
    tone(buzzer, 10);
  }
  else if(distance > 10)
  {
    noTone(buzzer);
  }
  else{
    noTone(buzzer);
  }

  if(Serial.available()>0)
  {
   data = Serial.read();

    if(data=='3')
    {Forward(); 
     delay(400);
     Stop();
    }

    else if(data=='4')
    {Backward();
     delay(400);
     Stop();
    }

    else if(data=='1')
    {Right();
     delay(200);
     Stop();
    }

    else if(data=='2')
    {Left();
     delay(200);
     Stop();
    }

  }
    else
    {Stop();}

} 

void Stop() {
  digitalWrite(MotorL1, LOW);
  digitalWrite(MotorL2, LOW);
  digitalWrite(MotorR1, LOW);
  digitalWrite(MotorR2, LOW);
  Serial.println("STOP");
  }

void Forward() {
  digitalWrite(MotorL1, HIGH);
  digitalWrite(MotorL2, LOW);
  digitalWrite(MotorR1, LOW);
  digitalWrite(MotorR2, HIGH);
  Serial.println("FORWARD");
  }

void Backward() {
  digitalWrite(MotorL1, LOW);
  digitalWrite(MotorL2, HIGH);
  digitalWrite(MotorR1, HIGH);
  digitalWrite(MotorR2, LOW);
  Serial.println("FORWARD");
  }

void Left() {
  digitalWrite(MotorL1, LOW);
  digitalWrite(MotorL2, HIGH);//digitalWrite(MotorL2, LOW); 
  digitalWrite(MotorR1, LOW);
  digitalWrite(MotorR2, HIGH);
  //
  Serial.println("LEFT");
  }

void Right() {
  digitalWrite(MotorL1, HIGH);
  digitalWrite(MotorL2, LOW);
  digitalWrite(MotorR1, HIGH);//digitalWrite(MotorR1, LOW);
  digitalWrite(MotorR2, LOW);
  Serial.println("RIGHT");
  }

  // Function for calculating the distance measured by the Ultrasonic sensor
