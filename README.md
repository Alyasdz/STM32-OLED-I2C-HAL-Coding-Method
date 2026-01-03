# STM32 OLED I2C Interfacing (HAL Coding Method)

This repository demonstrates how to interface a **128×64 SSD1306 OLED display** with **STM32F446RE** using **I2C and HAL drivers** in **STM32CubeIDE**.

The project is designed for **absolute beginners**, while still following **real-world embedded software practices** used in industry.

---

## 🎯 Project Objective

* Configure I2C using STM32CubeIDE
* Initialize SSD1306 OLED using HAL
* Display multi-line text on a 128×64 OLED
* Understand **OLED I2C addressing (0x78 / 0x7A / 0x3C)**
* Learn how to **integrate an open-source OLED driver correctly**

---

## 🧰 Hardware Used

* STM32F446RE (Nucleo board)
* 128×64 OLED Display (SSD1306, I2C)
* Jumper wires

---

## 🔌 OLED Pin Connections

| OLED Pin | STM32F446RE |
| -------- | ----------- |
| VCC      | 3.3V / 5V   |
| GND      | GND         |
| SDA      | I2C1 SDA    |
| SCL      | I2C1 SCL    |

---

## 📡 OLED I2C Address – **Very Important**

This is one of the **most common confusion points** for beginners.

### 🔹 8-bit Addresses (As Shown in Datasheets)

| Operation | Address |
| --------- | ------- |
| Write     | `0x78`  |
| Read      | `0x7A`  |

These addresses **include the R/W bit**.

---

### 🔹 7-bit Address (Used by STM32 HAL)

STM32 HAL expects a **7-bit I2C address**.

```
0x78 >> 1 = 0x3C
0x7A >> 1 = 0x3D
```

✅ **Correct address to use in HAL:**

```c
0x3C
```

> ⚠️ Using `0x78` directly in HAL will cause I2C communication to fail and the OLED will remain blank.

---

### 🔹 Address Used in This Project

```c
#define SSD1306_I2C_ADDR  0x3C
```

HAL internally manages:

* Write → `0x78`
* Read  → `0x7A`

You **do not need to handle this manually**.

---

## 🗂️ Repository Structure

```
STM32-OLED-I2C-HAL-Coding-Method/
├── Core/
│   ├── Src/
│   │   └── main.c
│   └── Inc/
├── Drivers/
├── STM32_I2C_HAL_coding.ioc
├── STM32F446RETX_FLASH.ld
├── STM32F446RETX_RAM.ld
├── README.md
```

---

## 🛠️ Software Requirements

* STM32CubeIDE
* STM32 HAL drivers
* SSD1306 OLED library

---

## 📦 SSD1306 OLED Driver Used (Reference)

This project uses the **open-source SSD1306 driver implementation by afiskon**.

### 🔗 Original Repository

[https://github.com/afiskon/stm32-ssd1306.git](https://github.com/afiskon/stm32-ssd1306.git)

### Files Integrated from That Repository

* `ssd1306.c`
* `ssd1306.h`
* `ssd1306_fonts.h`

---

## 🔍 How the Driver Is Used in This Project

* The driver is integrated directly into the CubeIDE project
* HAL-based I2C communication is used
* OLED address is configured as **7-bit (`0x3C`)**
* Display updates use a **frame buffer mechanism**

👉 No driver logic was modified.
This project focuses on **correct integration and usage**, not re-writing the OLED protocol.

---

## 🧠 Why Use an Existing Driver?

SSD1306 displays require:

* Proper initialization sequence
* Correct I2C timing
* Frame buffer management

Using a **well-tested open-source driver** allows beginners to:

* Focus on STM32 HAL fundamentals
* Avoid low-level display pitfalls
* Build reliable applications faster

This is exactly how **real embedded projects are developed**.

---

## 🧠 Code Walkthrough (Based on `main.c`)

### 1️⃣ Peripheral Initialization

```c
HAL_Init();
SystemClock_Config();
MX_GPIO_Init();
MX_I2C1_Init();
```

Initializes:

* HAL core
* System clock
* GPIO
* I2C1 peripheral

---

### 2️⃣ OLED Initialization

```c
ssd1306_Init();
```

* Initializes SSD1306 controller
* Clears internal frame buffer

---

### 3️⃣ Writing Text to OLED

```c
ssd1306_Fill(Black);

ssd1306_SetCursor(0, 0);
ssd1306_WriteString("STM32F446", Font_11x18, White);

ssd1306_SetCursor(0, 22);
ssd1306_WriteString("STM32 OLED I2C", Font_7x10, White);

ssd1306_SetCursor(0, 44);
ssd1306_WriteString("Daniel Raj.C", Font_6x8, White);

ssd1306_SetCursor(0, 55);
ssd1306_WriteString("_____________", Font_6x8, White);

ssd1306_UpdateScreen();
```

📌 Nothing appears on the OLED until `ssd1306_UpdateScreen()` is called.

---

### 4️⃣ Infinite Loop

```c
while (1)
{
    // OLED continues displaying the last frame
}
```

---

## ⚠️ Common Troubleshooting

If the OLED is blank:

* Check SDA / SCL wiring
* Verify I2C1 is enabled in CubeIDE
* Confirm OLED address is `0x3C`
* Try `0x3D` if SA0 pin is HIGH
* Ensure OLED power is correct
* Make sure `ssd1306_UpdateScreen()` is executed

---

## 🚀 Learning Outcomes

By completing this project, you will understand:

* STM32CubeIDE project structure
* HAL-based I2C communication
* OLED I2C addressing (7-bit vs 8-bit)
* SSD1306 frame buffer operation
* Clean integration of third-party drivers

---

## 📈 Possible Enhancements

* Display live sensor data
* Scrolling text and animations
* Bitmap / logo rendering
* Menu-based UI
* Register-level I2C implementation

---

## 🧾 License

This project is released under the **MIT License**.

The SSD1306 driver follows the license defined in the original repository:
[https://github.com/afiskon/stm32-ssd1306.git](https://github.com/afiskon/stm32-ssd1306.git)

---

