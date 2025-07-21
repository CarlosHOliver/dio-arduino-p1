# Projeto de Controle de Temperatura com LCD e LEDs

Este projeto utiliza um sensor de temperatura (conectado ao Arduino) para controlar LEDs baseados na leitura da temperatura e exibir a temperatura no display LCD. O projeto foi desenvolvido utilizando a biblioteca **Adafruit_LiquidCrystal** para o controle do LCD I2C e a leitura do sensor de temperatura através de um pino analógico do Arduino.

## Componentes Utilizados

- **Arduino Uno** (ou outro modelo de Arduino compatível)
- **Sensor de Temperatura** (como o LM35 ou TMP36)
- **LEDs** (3 LEDs de cores diferentes: vermelho, azul e verde)
- **Resistor** (para limitar a corrente para os LEDs)
- **LCD 16x2 com I2C**
- **Fios de conexão** e **Protoboard** para montagem

## Esquema de Conexão

### LCD 16x2 I2C:
- **VCC** -> 5V do Arduino
- **GND** -> GND do Arduino
- **SDA** -> A4 (pino de comunicação I2C no Arduino Uno)
- **SCL** -> A5 (pino de comunicação I2C no Arduino Uno)

### Sensor de Temperatura (conectado ao pino A0):
- **VCC** -> 5V do Arduino
- **GND** -> GND do Arduino
- **Saída** (Sinal analógico) -> A0 do Arduino

### LEDs:
- **LED Vermelho** -> Pino 8
- **LED Azul** -> Pino 9
- **LED Verde** -> Pino 10
- Cada LED deve ser conectado ao GND através de um resistor (geralmente 220Ω a 330Ω).

## Código

O código abaixo lê a temperatura do sensor de temperatura conectado ao pino **A0** do Arduino, controla os LEDs de acordo com a faixa de temperatura e exibe a temperatura no **LCD 16x2**:

```cpp
#include <Wire.h>
#include <Adafruit_LiquidCrystal.h>

// Instancia o LCD no endereço 0 (o mesmo que 0x20)
Adafruit_LiquidCrystal lcd_1(0);

// Definição dos pinos
int PinRoj = 8;
int PinAzu = 9;
int PinVer = 10;
int pinTMP = A0;  // Certifique-se de que o sensor está conectado no pino A0

int valorTMP = 0;

void setup() {
  pinMode(PinRoj, OUTPUT);
  pinMode(PinAzu, OUTPUT);
  pinMode(PinVer, OUTPUT);

  Serial.begin(9600);

  lcd_1.begin(16, 2);  // Inicializa o LCD com 16 colunas e 2 linhas
  lcd_1.print("Temperatura:");
  
  // Inicializa os LEDs
  digitalWrite(PinRoj, HIGH);
  digitalWrite(PinAzu, LOW);
  digitalWrite(PinVer, LOW);
}

void loop() {
  valorTMP = analogRead(pinTMP);  // Lê o valor do sensor de temperatura

  // Converte o valor lido em temperatura (aproximação simples)
  float temperatura = (valorTMP * 5.0 * 100.0) / 1024.0;
  
  // Exibe a temperatura no LCD
  lcd_1.setCursor(0, 1);  // Posiciona o cursor na segunda linha
  lcd_1.print(temperatura);  // Exibe a temperatura
  lcd_1.print(" C");  // Exibe a unidade de temperatura

  // Exibe o valor no monitor serial (para monitoramento)
  Serial.print("Valor TMP = ");
  Serial.println(valorTMP);

  // Controle dos LEDs com base na temperatura
  if (valorTMP <= 125) {
    digitalWrite(PinRoj, LOW);
    digitalWrite(PinAzu, HIGH);
    digitalWrite(PinVer, LOW);
  }

  if (valorTMP > 125 && valorTMP <= 160) {
    digitalWrite(PinRoj, LOW);
    digitalWrite(PinAzu, LOW);
    digitalWrite(PinVer, HIGH);
  }

  if (valorTMP > 160) {
    digitalWrite(PinRoj, HIGH);
    digitalWrite(PinAzu, LOW);
    digitalWrite(PinVer, LOW);
  }

  delay(1000);  // Atualiza a leitura a cada 1 segundo
}
