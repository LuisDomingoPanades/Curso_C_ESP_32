# Curso_Programacion_Arduino_2021
#Practica 1
volatile int interruptCounter;
const int potPin=34;
const int PWMpin=32;
const int freq=5000;
const int ledChannel=0;
const int resolution=12;
int potValue=0;
float Voltage=0;
hw_timer_t*timer=NULL;
portMUX_TYPE timerMux = portMUX_INITIALIZER_UNLOCKED;
int totalInterruptCounter;
void IRAM_ATTR onTimer() {
  portENTER_CRITICAL_ISR(&timerMux);
  interruptCounter++;
  portEXIT_CRITICAL_ISR(&timerMux);
 
}
void setup() {
  // put your setup code here, to run once:
Serial.begin(115200);
ledcSetup(ledChannel,freq,resolution);
ledcAttachPin(PWMpin, ledChannel);
timer=timerBegin(0,80,true);
timerAttachInterrupt(timer,&onTimer,true);
timerAlarmWrite(timer,10000000,true);
timerAlarmEnable(timer);
}


  void loop() {
    
  ledcWrite(ledChannel,potValue);
  if (interruptCounter > 0) {
 
    portENTER_CRITICAL(&timerMux);
    interruptCounter--;
    portEXIT_CRITICAL(&timerMux);
    totalInterruptCounter++;
    potValue=analogRead(potPin);
    
    Serial.println(potValue);
    Voltage=potValue*3.3/4095;
    Serial.print("El voltaje es: ");
    Serial.println(Voltage);
 
  }
}
