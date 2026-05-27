#include <Servo.h>
#include <LiquidCrystal_I2C.h>

//Consts
const byte IN=3, OUT=4, MOTOR=2, AGUA=9, HORA=6;


//Objs
Servo motor;
LiquidCrystal_I2C tela(0x20, 16, 2);


//Vars
int hora = 1;
int min = 1;  //(1 min tempo teste)
unsigned long ultimoTempo = 0;


void setup() {
  Serial.begin(9600);
  
  pinMode(OUT, OUTPUT); 
  pinMode(IN, INPUT);
  pinMode(AGUA, OUTPUT);
  pinMode(HORA, INPUT_PULLUP);
  motor.attach(MOTOR);
  
  //Setando potência do motor d'água
  analogWrite(AGUA, 20);
  
  //iniciando display
  tela.init();
  tela.backlight();
  tela.print("INICIALIZANDO...");
  delay(2000);
  
  //Adição de ração ao ligar a fonte
  Serial.println("Colocando Comida");
  tela.clear();
  tela.print("Colocando Comida");
  motor.write(0);
  delay(2000);
  motor.write(90);
  
  ultimoTempo = millis();
  atualizarLCD();
}

void loop() {
  delay(100);
  
  // Timer baseado em millis() - 60 segundos
  if(millis() - ultimoTempo >= 60000) {
    ultimoTempo = millis();
    min--;
    
    if(min < 0) {
      min = 59;
      hora--;
    }
    atualizarLCD();
  }
  
  // Verificar botão de hora
  if(digitalRead(HORA) == 0) {
    delay(50); // debounce
    if(digitalRead(HORA) == 0) {
      hora++;
      if(hora > 12) 
      	hora = 0;
      	min = 1;  //Reseta para 1 minuto(teste)
      atualizarLCD();
      Serial.println((String)"Hora ajustada: " + hora + ":" + (min < 10 ? "0" : "") + min);
    }
    //acho que não precisa desse while:
    //while(digitalRead(HORA) == 0);
    //delay(50);
  }
  
  // Controlar água pela distância do gato
  if(distancia() <= 200) {
    analogWrite(AGUA, 255);
  } else {
    analogWrite(AGUA, 20);
  }
  
  // Colocar ração quando timer chegar a 0
  if(hora == 0 && min == 0) {
    Serial.println("Colocando Comida");
    tela.clear();
    tela.print("Colocando Comida");
    motor.write(0);
    delay(2000);
    motor.write(90);
    hora = 0;
    min = 1; //tempo teste
    atualizarLCD();
    delay(1000); // Evita múltiplas ativações
  }
}

void atualizarLCD() {
  tela.clear();
  tela.setCursor(0, 0);
  tela.print((String)"Faltam: " + hora + ":");
  if(min < 10) tela.print("0");
  tela.print(min);
  tela.setCursor(0, 1);
  tela.print("pra repor comida.");
}


float distancia() {
  digitalWrite(OUT, LOW);
  delayMicroseconds(2);
  digitalWrite(OUT, HIGH);
  delayMicroseconds(10);
  digitalWrite(OUT, LOW);
  return (pulseIn(IN, HIGH, 50000) * 0.0343) / 2;
}
