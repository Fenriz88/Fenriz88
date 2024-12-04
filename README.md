#include <Keypad.h>
#include <LiquidCrystal.h>
const int buzzerPin = 8;
const int ledOpenPin = 9;
const int ledClosePin = 10;
// Configuración del teclado matricial
const byte rows = 4; // Número de filas
const byte cols = 4; // Número de columnas
char keys[rows][cols] = {
  {'1', '2', '3', 'A'},
  {'4', '5', '6', 'B'},
  {'7', '8', '9', 'C'},
  {'*', '0', '#', 'D'}
};
byte rowPins[rows] = {2, 3, 4, 5}; // Pines de filas
byte colPins[cols] = {6, 7, 8, 9};  // Pines de columnas

Keypad keypad = Keypad(makeKeymap(keys), rowPins, colPins, rows, cols);
LiquidCrystal lcd(10, 11, 12, 13, A0, A1);

const String correctCode = "1234"; // Código correcto
String inputCode = ""; // Código ingresado
int attemptCount = 0;

void setup() {
  pinMode(buzzerPin, OUTPUT);
  pinMode(ledOpenPin, OUTPUT);
  pinMode(ledClosePin, OUTPUT);
  lcd.begin(16, 2);
  lcd.print("Introduce el");
  lcd.setCursor(0, 1);
  lcd.print("codigo:");
  digitalWrite(ledClosePin, HIGH); // La puerta comienza cerrada
  digitalWrite(buzzerPin, HIGH); // Activa la chicharra
delay(2000);                   // La chicharra sonará durante 2 segundos
digitalWrite(buzzerPin, LOW);  // Apaga la chicharra
  pinMode(buzzerPin, OUTPUT);
  digitalWrite(buzzerPin, HIGH); // Activa la chicharra
  delay(2000);                   // La chicharra sonará durante 2 segundos
  digitalWrite(buzzerPin, LOW);  // Apaga la chicharra
}
void loop() {
  char key = keypad.getKey();

  if (key) {
    inputCode += key;
    lcd.setCursor(inputCode.length() - 1, 1);
    lcd.print(key);

    if (inputCode.length() == 4) {
      lcd.clear();

      if (inputCode == correctCode) {
        digitalWrite(ledOpenPin, HIGH);  // Activar LED de puerta abierta
        digitalWrite(ledClosePin, LOW);  // Apagar LED de puerta cerrada
        lcd.print("Acceso Aprobado");
        delay(2000);
        digitalWrite(ledOpenPin, LOW);   // Apagar LED de puerta abierta
        digitalWrite(ledClosePin, HIGH); // Encender LED de puerta cerrada
        attemptCount = 0;                // Reiniciar conteo de intentos
      } else {
        attemptCount++;
        lcd.print("Codigo Incorrecto");
        delay(2000);
        if (attemptCount >= 3) {
          lcd.clear();
          lcd.print("Alarma Activada!");
          digitalWrite(buzzerPin, HIGH); // Activar alarma
          delay(5000);                   // Alarma durante 5 segundos
          digitalWrite(buzzerPin, LOW);  // Apagar alarma
          attemptCount = 0;              // Reiniciar conteo de intentos
        }
      }
      // Reiniciar entrada de código
      inputCode = "";
      lcd.clear();
      lcd.print("Introduce el");
      lcd.setCursor(0, 1);
      lcd.print("codigo:");
}
}
