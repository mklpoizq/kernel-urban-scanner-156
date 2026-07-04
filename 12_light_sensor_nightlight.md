# Arduino：光敏电阻自动夜灯



## 说明

当环境光线变暗时自动点亮 LED 灯。无需手动开关，模拟路灯自动控制。学习光敏电阻分压电路、模拟阈值判断。



## 硬件需求

- Arduino UNO ×1

- 光敏电阻（LDR）×1

- 10KΩ 固定电阻 ×1（分压用）

- LED ×1，220Ω 电阻 ×1



## 电路连接

光敏电阻与 10KΩ 组成分压电路：5V → LDR → A0 → 10KΩ → GND。LED 接 D13。



## 代码

```cpp

const int LDR_PIN = A0;

const int LED_PIN = 13;



// 阈值：低于此值认为光照不足，需要亮灯

const int LIGHT_THRESHOLD = 400;



void setup() {

  pinMode(LED_PIN, OUTPUT);

  Serial.begin(9600);

  Serial.println("自动夜灯启动...");

}



void loop() {

  int lightValue = analogRead(LDR_PIN);



  Serial.print("光照值: ");

  Serial.println(lightValue);



  if (lightValue < LIGHT_THRESHOLD) {

    digitalWrite(LED_PIN, HIGH);

    Serial.println("  → 光线暗，开灯");

  } else {

    digitalWrite(LED_PIN, LOW);

    Serial.println("  → 光线充足，关灯");

  }



  delay(500);

}

```



## 教学重点

- 光敏电阻阻值随光照变化：光越强阻值越小，A0 电压越高

- 分压电路 `Vout = Vin × R2/(R1+R2)`，这里 R2=10KΩ 固定

- 阈值应根据实际环境调试，用串口观察读数设定

- 可在阈值前后加滞后区间（如 380~420），防止临界抖动



## 常见错误

- 分压电阻选太大（>100KΩ）→ 读数范围太窄

- 阈值设反了（暗时读数小，亮时读数大取决于分压接法）

- 光敏电阻被遮挡 → 无法正确感知环境光线

