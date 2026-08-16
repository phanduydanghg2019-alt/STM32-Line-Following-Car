# 🏎️ Xe Dò Line Tự Động - STM32F4 (PID Control)

Dự án xe dò line (Line Following Robot) sử dụng vi điều khiển **STM32F4** kết hợp với bộ cảm biến hồng ngoại 5 mắt (QTR-8) và thuật toán điều khiển **PID** để xe bám vạch mượt mà, tối ưu tốc độ.

---

## 🛠️ Phần Cứng Sử Dụng

* **Vi xử lý:** STM32F4 (ARM Cortex-M4)
* **Cảm biến:** Cụm 5 mắt đọc hồng ngoại
* **Mạch điều khiển động cơ:** L298N
* **Động cơ:** 2 Động cơ DC (Trái/Phải)
* **Nguồn điện:** Pin 18650 (12V)

---

## ⚙️ Cấu Hình Phần Mềm (STM32CubeIDE)

* **Timer 1 (TIM1):** Xuất xung PWM 2 kênh (`TIM_CHANNEL_1`, `TIM_CHANNEL_2`) điều khiển tốc độ 2 động cơ.
  * **Period (ARR):** `999`
  * **Prescaler (PSC):** `4`
* **GPIO Output:** 4 chân xuất tín hiệu hướng quay (`IN1`, `IN2`, `IN3`, `IN4`).
* **GPIO Input:** 5 chân đọc tín hiệu số từ cảm biến (`LINE_2` đến `LINE2`).

---

## 🧠 Thuật Toán Điều Khiển (PID)

Xe sử dụng thuật toán **PID rời rạc** để tính toán độ lệch so với vạch đen và điều chỉnh xung PWM cho 2 bánh:

$$\text{Motorspeed} = (P \times K_p) + (I \times K_i) + (D \times K_d)$$

* **$K_p$ (Proportional):** Lực bẻ lái tức thì dựa trên độ lệch hiện tại.
* **$K_i$ (Integral):** Bù sai số tích lũy theo thời gian (dùng mảng lưu 5 sai số gần nhất).
* **$K_d$ (Derivative):** Phanh chủ động chống vẫy đuôi cá và giúp xe vào cua êm ái.

### 📌 Bộ thông số gợi ý ban đầu:
```c
float Kp = 0.25;
float Ki = 0.0;
float Kd = 1.2;
volatile int base_speed = 500; // PWM nền (0 - 999)
