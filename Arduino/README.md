## 1. Пиктограф (Etch-A-Sketch)

**Краткое описание:**  
Эмуляция классического «Etch-A-Sketch» на OLED-дисплее: две ручки-потенциометра задают X/Y-координаты курсора, одна кнопка сбрасывает изображение.

### 1.1. Компоненты

- Arduino Uno или ESP32 
    
- OLED 128×64 (I²C SSD1306 или SH1106) 
    
- 2× потенциометр 10 кΩ
    
- 1× кнопка (с INPUT_PULLUP)
    
- Провода и макетная плата
    

### 1.2. Сборка схемы

1. Подключите OLED по I²C (SDA → A4, SCL → A5 на Uno) 
    
2. Один вывод каждого потенциометра → 5 V, второй → GND, средний (вых.) → аналоговые входы A0 и A1.
    
3. Кнопку: один контакт → GND, другой → цифровой пин с подтяжкой (например, D2 + INPUT_PULLUP).
    

### 1.3. Код

```cpp
#include <Adafruit_SSD1306.h>
#include <Adafruit_GFX.h>
#define OLED_ADDR 0x3C
Adafruit_SSD1306 oled(128,64,&Wire);
const int potX = A0, potY = A1, btnPin = 2;
int prevX=64, prevY=32;

void setup() {
  oled.begin(SSD1306_SWITCHCAPVCC, OLED_ADDR);
  pinMode(btnPin, INPUT_PULLUP);
  oled.clearDisplay();
}

void loop() {
  if (digitalRead(btnPin)==LOW) {
    oled.clearDisplay();          // Сброс экрана при нажатии
  }
  // Считываем и масштабируем значения потенциометров
  int x = map(analogRead(potX),0,1023,0,127);
  int y = map(analogRead(potY),0,1023,0,63);
  // Рисуем линию от предыдущей точки
  oled.drawLine(prevX, prevY, x, y, SSD1306_WHITE);
  prevX = x; prevY = y;
  oled.display();
  delay(50);
}
```

### 1.4. Пояснения

- **map()** переводит диапазон 0–1023 в размер экрана [Arduino Forum](https://forum.arduino.cc/t/etch-a-sketch-with-arduino/12913?utm_source=chatgpt.com).
    
- **drawLine()** рисует соединительный фрагмент «резинки» Etch-A-Sketch.
    
- **clearDisplay()** стирает всё при нажатии кнопки.
    

---

## 2. Мини-пианино

**Краткое описание:**  
Нажатие кнопки генерирует ноту через пассивный зуммер. Можно добавить потенциометр для изменения частоты (или громкости).

### 2.1. Компоненты

- Arduino Uno или ESP32
    
- Пассивный зуммер
    
- 3–4 кнопки [Instructables](https://www.instructables.com/Arduino-Piezo-Three-Button-Piano/?utm_source=chatgpt.com)
    
- (опционально) потенциометр 10 кΩ для диапазона частот
    
- Провода и макетная плата
    

### 2.2. Сборка схемы

1. Зуммер → цифровой вывод (например, D8) и GND.
    
2. Каждая кнопка: один контакт → GND, другой → цифровой пин (например, D2–D5) с INPUT_PULLUP.
    
3. (Если есть) потенциометр: средний вывод → A0, боковые → 5 V и GND.
    

### 2.3. Код (основная логика)

```cpp
const int buzzer = 8;
const int btnPins[] = {2,3,4,5};
const int notes[] = {262,294,330,349}; // частоты C4,D4,E4,F4

void setup() {
  for (int i=0; i<4; i++) pinMode(btnPins[i], INPUT_PULLUP);
}

void loop() {
  for (int i=0; i<4; i++){
    if (digitalRead(btnPins[i])==LOW){
      tone(buzzer, notes[i]);      // запускаем ноту
    }
  }
  // Если ни одна кнопка не нажата — отключаем звук
  if (digitalRead(btnPins[0]) && digitalRead(btnPins[1])
    && digitalRead(btnPins[2]) && digitalRead(btnPins[3])) {
      noTone(buzzer);
  }
}
```

### 2.4. Расширения и советы

- **Потенциометр**: `tone(buzzer, map(analogRead(A0),0,1023,200,800));` [Instructables](https://www.instructables.com/Arduino-Piezo-Three-Button-Piano/?utm_source=chatgpt.com)
    
- **Мелодии**: хранить массив частот и задержек, проигрывать в `for`-цикле [Arduino Project Hub](https://projecthub.arduino.cc/rahulkhanna/arduino-tutorial-mini-piano-0c7ec5?utm_source=chatgpt.com).
    

---

## 3. Игра «Реакция» (Reaction Time)

**Краткое описание:**  
LED загорается после случайной задержки, игрок должен нажать кнопку как можно быстрее; время реакции выводится в Serial Monitor.

### 3.1. Компоненты

- Arduino Uno или ESP32
    
- 1 LED + резистор 220 Ω
    
- 1 кнопка [Instructables](https://www.instructables.com/Arduino-Reaction-Time-Tester/?utm_source=chatgpt.com)
    
- Провода и макетная плата
    

### 3.2. Сборка схемы

1. LED: длинная ножка → цифровой пин D13 (или другой) через резистор, короткая → GND.
    
2. Кнопка: один контакт → 5 V, другой → цифровой пин D2 с INPUT_PULLUP, через 10 кΩ к GND [Instructables](https://www.instructables.com/Arduino-Reaction-Time-Tester/?utm_source=chatgpt.com).
    

### 3.3. Код (машина состояний)

```cpp
const int btnPin = 2, ledPin = 13;
unsigned long startTime, reactionTime;
enum {WAITING, READY, DONE} state = WAITING;

void setup() {
  pinMode(btnPin, INPUT_PULLUP);
  pinMode(ledPin, OUTPUT);
  Serial.begin(115200);
  Serial.println("Press to start");
}

void loop() {
  if (state==WAITING && digitalRead(btnPin)==LOW) {
    delay(50); state = READY;
    unsigned long wait = random(1000,5000);
    delay(wait);
    digitalWrite(ledPin,HIGH);
    startTime = millis();
    Serial.println("GO!");
  }
  else if (state==READY && digitalRead(btnPin)==LOW) {
    reactionTime = millis() - startTime;
    digitalWrite(ledPin,LOW);
    Serial.print("Time: "); Serial.print(reactionTime); Serial.println(" ms");
    state = DONE; delay(2000);
    Serial.println("Press to start again");
  }
  else if (state==DONE) {
    state = WAITING;
  }
}

```

### 3.4. Пояснения

- **random(1000,5000)** задаёт задержку 1–5 с перед сигналом
    
- **millis()** даёт точный временной штамп для измерения реакции без блокировки.
- 
- **INPUT_PULLUP** упрощает схему (кнопка замыкает на GND).
    

---