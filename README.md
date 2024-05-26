# Edge - FE Club

# Projeto de medição de distância entre carros, e temperatura da pista (Fórmula E)

## Equipe

Rafael Duarte, RM 558644 <br>
Rafael Gaspar, RM 557228 <br> 
Vinicius Monteiro, RM 555088 <br>
Guilherme Oliveira, RM 555180 <br> 
Enzo Diógenes, RM 555062 <br>

## Link da simulação em TinkerCad
https://www.tinkercad.com/things/eaBaSlt0gzp-medidores-de-distancia-entre-carros-e-temperatura-da-pista

## Dependências:
1. Buzzer
2. Sensor de temperatura
3. 3x leds (um vermelho, um amarelo, um verde)
4. 3x Resistores, 3x de 220Ω
5. Sensor ultrassônico
6. Tela LCD
7. Conectores macho / fêmea e macho / macho

## Conceito do projeto / seu funcionamento

A ideia do projeto consistiu em construir um protótipo no qual seja possível medir a distância entre carros durante corridas da Fórmula E, além de calcular a média dessa distância ao longo de toda corrida. Em cima disso, caso o piloto esteja muito próximo do carro da frente, é emitido um som no buzzer e o led vermelho acenderá, indicando aproximação perigosa. A média de distância entre carros ao fim da corrida não apenas serve como uma informação para as equipes, mas também para a demonstração da própria modalidade de uma de suas características: proximidade entre os carros. <br>
Para complementar, foi realizada a implementação de um sensor de temperatura, com o objetivo de detectar a temperatura da pista e avisar tanto a central da equipe, quanto ao piloto, sobre a mesma. Caso a pista esteja muito quente, é emitido um som no buzzer e o led vermelho acenderá. Dessa forma, torna-se possível a ciência da equipe sobre as condições da pista para tomadas de decisões estratégicas, afinal uma pista quente desgasta mais os pneus.


### Reprodução visual:

![image](https://github.com/RafaelDuarteF/fe-club-edge/assets/103393497/94f00ba7-128b-459f-bc45-7e82f0c35771)
![image](https://github.com/RafaelDuarteF/fe-club-edge/assets/103393497/a8d14c65-4260-4dc5-a4f2-6cd52d88e957)


### Código utilizado:

```C++
#include <Adafruit_LiquidCrystal.h>

Adafruit_LiquidCrystal lcd_1(0);

#define echoPin 6
#define trigPin 7
unsigned int duracao = 0;
unsigned int distancia = 0;
unsigned int media = 0;
const int analogIn = A0;
const int BUZZER_PIN = 8;
int totalLoop = 0;
int soma = 0;
int ledGreen = 4;
int ledYellow = 3;
int ledRed = 2;
int RawValue= 0;
double Voltage = 0;
double tempC = 0;

void setup()
{
  pinMode(LED_BUILTIN, OUTPUT);
  pinMode(echoPin, INPUT);
  pinMode(trigPin, OUTPUT);
  pinMode(ledRed, OUTPUT);
  pinMode(ledYellow, OUTPUT);
  pinMode(ledGreen, OUTPUT);
  lcd_1.begin(16, 2);
  lcd_1.begin(16, 2, LCD_5x8DOTS);
  lcd_1.setCursor(0, 0);
  Serial.begin(9600);
}

void loop()
{
  
  digitalWrite(trigPin, HIGH);
  delayMicroseconds(10);
  digitalWrite(trigPin, LOW);

  duracao = pulseIn(echoPin, HIGH);
  distancia = duracao * 0.01719445; // Velocidade do som dividido por 2
	
  if(distancia < 332) {
     if(distancia <= 4) {
    	digitalWrite(ledRed, HIGH);
     	digitalWrite(ledYellow, LOW);
        digitalWrite(ledGreen, LOW);
       	tone(BUZZER_PIN, 1000, 200);
    } else if(distancia <= 10) {
      	digitalWrite(ledRed, LOW);
        digitalWrite(ledGreen, LOW);
    	digitalWrite(ledYellow, HIGH);
    } else {
    	digitalWrite(ledRed, LOW);
        digitalWrite(ledGreen, HIGH);
    	digitalWrite(ledYellow, LOW);
    }
    lcd_1.clear();
    lcd_1.print(distancia);
    lcd_1.print("cm");
    lcd_1.setCursor(0, 1);
    totalLoop++;
	soma = soma + distancia;
    media = soma/totalLoop;
    lcd_1.print("Media: ");
    lcd_1.print(media);
    lcd_1.print("cm");
    lcd_1.setCursor(0, 0);
  }
  else {
    lcd_1.clear();
    lcd_1.print("Sem distancia");
  }
  
  delay(2000);
  noTone(BUZZER_PIN);  // Para o som
  
  RawValue = analogRead(analogIn);
  Voltage = (RawValue / 1023.0) * 5000; // 5000 to get millivots.
  tempC = (Voltage-500) * 0.1; // 500 is the offset
  
  if(tempC > 45) {
    digitalWrite(ledRed, HIGH);
    digitalWrite(ledYellow, LOW);
    digitalWrite(ledGreen, LOW);
    tone(BUZZER_PIN, 1000, 200);
  } else if(tempC > 25) {
    digitalWrite(ledRed, LOW);
    digitalWrite(ledYellow, HIGH);
    digitalWrite(ledGreen, LOW);
  } else {
  	digitalWrite(ledRed, LOW);
    digitalWrite(ledYellow, LOW);
    digitalWrite(ledGreen, HIGH);
  }
  lcd_1.clear();
  lcd_1.setCursor(0, 0);
  lcd_1.print("Temp. da pista:");
  lcd_1.setCursor(0, 1);
  lcd_1.print(tempC);
  lcd_1.print(" C");
  digitalWrite(ledRed, HIGH);
  digitalWrite(ledYellow, LOW);
  digitalWrite(ledRed, LOW);
  delay(2000);
  noTone(BUZZER_PIN);
}
```
