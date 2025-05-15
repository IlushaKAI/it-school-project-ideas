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

- **map()** переводит диапазон 0–1023 в размер экрана 
    
- **drawLine()** рисует соединительный фрагмент «резинки» Etch-A-Sketch.
    
- **clearDisplay()** стирает всё при нажатии кнопки.
    

---

## 2. Мини-пианино

**Краткое описание:**  
Нажатие кнопки генерирует ноту через пассивный зуммер. Можно добавить потенциометр для изменения частоты (или громкости).

### 2.1. Компоненты

- Arduino Uno или ESP32
    
- Пассивный зуммер
    
- 3–4 кнопки
    
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

- **Потенциометр**: `tone(buzzer, map(analogRead(A0),0,1023,200,800));
    
- **Мелодии**: хранить массив частот и задержек, проигрывать в `for`-цикле 
    

---

## 3. Игра «Реакция» (Reaction Time)

**Краткое описание:**  
LED загорается после случайной задержки, игрок должен нажать кнопку как можно быстрее; время реакции выводится в Serial Monitor.

### 3.1. Компоненты

- Arduino Uno или ESP32
    
- 1 LED + резистор 220 Ω
    
- 1 кнопка
    
- Провода и макетная плата
    

### 3.2. Сборка схемы

1. LED: длинная ножка → цифровой пин D13 (или другой) через резистор, короткая → GND.
    
2. Кнопка: один контакт → 5 V, другой → цифровой пин D2 с INPUT_PULLUP, через 10 кΩ к GND 
    

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
    

## 4 Автоматическое уличное освещение с индикацией на OLED

**Цель:** Автоматически включать светодиод при снижении освещённости ниже заданного порога и отображать текущий уровень освещённости на OLED-дисплее.

### Необходимые компоненты:

- Arduino Uno или совместимая плата
    
- Фоторезистор (LDR)
    
- Резистор 10 кОм
    
- Светодиод (LED)
    
- Резистор 220 Ом для светодиода
    
- OLED-дисплей 128x64 на контроллере SSD1306 (I2C)
    
- Провода и макетная плата
    

### Схема подключения:

1. **Фоторезистор:**
    
    - Один вывод LDR подключите к 5V.
        
    - Второй вывод LDR подключите к аналоговому входу A0 и одновременно через резистор 10 кОм к GND (создавая делитель напряжения).
        
2. **Светодиод:**
    
    - Анод (длинная ножка) светодиода через резистор 220 Ом подключите к цифровому пину D9.
        
    - Катод (короткая ножка) светодиода подключите к GND.
        
3. **OLED-дисплей:**
    
    - VCC → 5V
        
    - GND → GND
        
    - SDA → A4 (на Arduino Uno)
        
    - SCL → A5 (на Arduino Uno)
        

### Установка библиотеки GyverOLED:

1. Откройте Arduino IDE.
    
2. Перейдите в **Скетч → Подключить библиотеку → Управлять библиотеками...**
    
3. В поле поиска введите "GyverOLED".
    
4. Установите библиотеку GyverOLED от AlexGyver.
    

### Код программы:

```cpp
#include <GyverOLED.h>

GyverOLED<SSD1306_128x64, OLED_BUFFER> oled;

const int ldrPin = A0;
const int ledPin = 9;
const int threshold = 500; // Порог освещенности

void setup() {
  pinMode(ledPin, OUTPUT);
  oled.init();
  oled.clear();
  oled.setScale(1);
  oled.home();
  oled.print("LDR Monitor");
  oled.update();
}

void loop() {
  int ldrValue = analogRead(ldrPin);
  
  // Управление светодиодом
  if (ldrValue < threshold) {
    digitalWrite(ledPin, HIGH);
  } else {
    digitalWrite(ledPin, LOW);
  }

  // Отображение значения на OLED
  oled.clear();
  oled.setCursor(0, 0);
  oled.print("LDR Value:");
  oled.setCursor(0, 2);
  oled.print(ldrValue);
  oled.update();

  delay(500);
}

```

**Пояснения:**

- `analogRead(ldrPin)` считывает значение освещенности.
    
- Если значение ниже порога, включается светодиод.
    
- OLED-дисплей обновляется каждые 500 мс, отображая текущее значение освещенности
    

---

## 5 Вольтметр-индикатор освещенности с графиком на OLED

**Цель:** Считывать уровень освещенности с фоторезистора и отображать его в виде графика на OLED-дисплее.

### Необходимые компоненты:

- Arduino Uno или совместимая плата
    
- Фоторезистор (LDR)
    
- Резистор 10 кОм
    
- OLED-дисплей 128x64 на контроллере SSD1306 (I2C)
    
- Провода и макетная плата
    

### Схема подключения:

Такая же, как в первом проекте, без подключения светодиода.

### Установка библиотеки GyverOLED:

Следуйте тем же шагам, что и в первом проекте.

### Код программы:

```cpp
#include <GyverOLED.h>

GyverOLED<SSD1306_128x64, OLED_BUFFER> oled;

const int ldrPin = A0;
const int graphHeight = 40;
const int graphWidth = 128;
int xPos = 0;

void setup() {
  oled.init();
  oled.clear();
  oled.setScale(1);
  oled.home();
  oled.print("LDR Graph");
  oled.update();
  delay(1000);
  oled.clear();
}

void loop() {
  int ldrValue = analogRead(ldrPin);
  int yValue = map(ldrValue, 0, 1023, graphHeight, 0); // Инвертируем для графика

  // Рисуем точку на графике
  oled.dot(xPos, yValue, 1);
  oled.update();

  xPos++;
  if (xPos >= graphWidth) {
    xPos = 0;
    oled.clear();
  }

  delay(100);
}

```

**Пояснения:**

- `map()` преобразует значение освещенности в координату Y для графика.
    
- `oled.dot(xPos, yValue, 1)` рисует точку на дисплее.
    
- График обновляется в реальном времени, отображая изменения освещенности.