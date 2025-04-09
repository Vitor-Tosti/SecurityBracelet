# 🔒 Security Bracelet – Sistema de Alerta Infantil com Arduino e Bluetooth

Projeto de Conclusão de Curso Técnico em Eletrônica – Etec Lauro Gomes

---

## 💡 Objetivo

Prevenir desaparecimentos de crianças em ambientes públicos através de uma pulseira inteligente que **monitora a conexão Bluetooth com o celular do responsável**. Caso a criança se afaste e a conexão seja perdida, **um alarme sonoro e visual é acionado automaticamente**.

---

## ⚙️ Tecnologias Utilizadas

- Arduino PRO MINI
- Módulo Bluetooth HC-06
- Linguagem C++ (Arduino IDE)
- LEDs (para status de conexão)
- Buzzer (para alarme)

---

## 📲 Como Funciona

1. A pulseira com Arduino e Bluetooth HC-06 é pareada com o celular do responsável.
2. Enquanto a conexão estiver ativa, o LED verde permanece aceso.
3. Se a conexão Bluetooth for perdida por mais de 5 segundos:
   - O **buzzer** é ativado (som de alerta).
   - Um **LED vermelho** acende, indicando desconexão.
4. O sistema reseta o temporizador e continua monitorando.

---

## 🔧 Código

```cpp
#include <SoftwareSerial.h>

SoftwareSerial BTSerial(11, 12); // RX = pino 12, TX = pino 11

const int buzzerPin = 8;
const int ledConnected = 7;
const int ledDisconnected = 6;

unsigned long lastTimeReceived = 0;
const unsigned long timeout = 5000;

void setup() {
  pinMode(buzzerPin, OUTPUT);
  pinMode(ledConnected, OUTPUT);
  pinMode(ledDisconnected, OUTPUT);

  Serial.begin(9600);
  BTSerial.begin(9600);

  Serial.println("Bluetooth configurado e pronto para monitorar a conexão!");
}

void loop() {
  if (BTSerial.available()) {
    char c = BTSerial.read();
    Serial.write(c);
    lastTimeReceived = millis();
    digitalWrite(ledConnected, HIGH);
    digitalWrite(ledDisconnected, LOW);
  }

  if (millis() - lastTimeReceived > timeout) {
    Serial.println("Conexão perdida! Acionando buzzer e LED de alerta...");
    digitalWrite(buzzerPin, HIGH);
    digitalWrite(ledDisconnected, HIGH);
    digitalWrite(ledConnected, LOW);
    delay(1000);
    digitalWrite(buzzerPin, LOW);
    lastTimeReceived = millis();
  }

  delay(100);
}
