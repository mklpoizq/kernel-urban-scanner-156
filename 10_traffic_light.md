# Arduino：交通灯系统（状态机）



## 说明

模拟十字路口交通灯：红灯→绿灯→黄灯循环切换。引入状态机编程思想，学习用 `enum` 枚举定义状态、`switch/case` 分支控制、以及 `millis()` 非阻塞延时。



## 硬件需求

- Arduino UNO ×1

- 红色 LED ×1，黄色 LED ×1，绿色 LED ×1

- 220Ω 电阻 ×3



## 电路连接

红/黄/绿 LED 正极分别接 D11/D10/D9，负极通过 220Ω 接地。



## 代码

```cpp

enum LightState { RED, GREEN, YELLOW };

LightState state = RED;



const int RED_PIN    = 11;

const int YELLOW_PIN = 10;

const int GREEN_PIN  = 9;



unsigned long lastChange = 0;



// 各状态持续时间（毫秒）

const unsigned long RED_DURATION    = 5000;  // 红灯 5 秒

const unsigned long GREEN_DURATION  = 4000;  // 绿灯 4 秒

const unsigned long YELLOW_DURATION = 2000;  // 黄灯 2 秒



void setup() {

  pinMode(RED_PIN, OUTPUT);

  pinMode(YELLOW_PIN, OUTPUT);

  pinMode(GREEN_PIN, OUTPUT);

  setLights(LOW, LOW, LOW);  // 全部熄灭

}



void setLights(int r, int y, int g) {

  digitalWrite(RED_PIN, r);

  digitalWrite(YELLOW_PIN, y);

  digitalWrite(GREEN_PIN, g);

}



void loop() {

  unsigned long now = millis();



  switch (state) {

    case RED:

      setLights(HIGH, LOW, LOW);

      if (now - lastChange >= RED_DURATION) {

        state = GREEN;

        lastChange = now;

      }

      break;



    case GREEN:

      setLights(LOW, LOW, HIGH);

      if (now - lastChange >= GREEN_DURATION) {

        state = YELLOW;

        lastChange = now;

      }

      break;



    case YELLOW:

      // 黄灯闪烁效果

      int blinkPhase = (now / 250) % 2;  // 每 250ms 翻转

      setLights(LOW, blinkPhase, LOW);

      if (now - lastChange >= YELLOW_DURATION) {

        state = RED;

        lastChange = now;

      }

      break;

  }

}

```



## 教学重点

- **状态机模式**：`enum` 定义状态 + `switch/case` 控制转换，代码清晰可维护

- **非阻塞延时**：用 `millis()` 对比时间戳，不卡死主循环

- 黄灯闪烁用 `(now / 250) % 2` 实现翻转（不需要额外变量）

- 状态机是嵌入式中最常用的编程范式之一



## 常见错误

- `delay()` 阻塞导致黄灯无法闪烁 → 必须用 `millis()`

- 状态切换时机不正确 → 检查 `lastChange` 赋值和 `>=` 判断

- `millis()` 约 50 天后会溢出归零，长期运行需考虑

