## 💡 IoT 기반 조도·온도 스마트 방재 시스템 (Light & Temperature-based Smart Fire Detection System)

<br>
본 문서는 Arduino를 기반으로 조도 센서와 온도 센서를 융합하여 화재를 조기에 감지하고 경보를 울리는 시스템 구현 내용을 담고 있습니다.

-----

### 🌟 1. 프로젝트 개요

화재는 초기 대응 여부가 피해 규모를 크게 좌우하므로 **조기 감지**가 무엇보다 중요합니다. 본 프로젝트는 \*\*조도 센서 (LDR)\*\*와 \*\*온도 센서 (DHT11/LM35)\*\*를 활용하여 실시간 환경 변화를 모니터링하고, \*\*"온도 상승 + 조도 급변"\*\*이 동시에 발생할 경우를 화재 위험으로 판단하는 **IoT 기반 스마트 방재 시스템**입니다.

### 🎯 2. 핵심 기능

  * **실시간 모니터링:** 온도 및 조도 변화 실시간 감지
  * **화재 조기 감지:** 융합 센서 로직을 통한 신속한 위험 판단
  * **자동 부저 경보:** 위험 상황 발생 시 즉각적인 경고음 출력
  * **손쉬운 시연:** 별도의 연기 없이 촛불, 라이터 등으로 간편 테스트 가능
  * **IoT 확장 가능:** ESP32, App Inventor 등과의 연계를 고려한 설계

-----

### 🔧 3. 사용 하드웨어 및 센서

| 구분 | 센서/부품 | 기능 |
| :--- | :--- | :--- |
| **온도 감지** | **DHT11 / LM35** | 주변 환경의 열(온도) 변화 감지 |
| **밝기 감지** | **LDR (Light Dependent Resistor)** | 주변 환경의 밝기(조도) 변화 감지 |
| **경보 출력** | mit app inventor 내 mp3 파일 출력 | 화재 위험 시 경고 알람 출력 |
| **제어 보드** | **Arduino UNO** | 센서 데이터 처리 및 로직 실행 |

---

### 🔥 4. 화재 위험 판단 로직

본 시스템은 두 가지 위험 요소가 동시에 충족될 때만 화재로 판단하여 **오탐률을 최소화**합니다.

$$
\text{IF } \bigl(T > T_{\text{th}}\bigr) \;\land\; \bigl(\Delta L > L_{\text{th}}\bigr)
\;\Rightarrow\; \text{Fire} = \text{TRUE} \;\Rightarrow\; \text{Piezo Buzzer ON}
$$

* $T_{\text{th}}$: 설정된 **온도 임계값** (예: $30^\circ\text{C}$)  
* $L_{\text{th}}$: 설정된 **조도 변화 임계값** (직전 값 대비 변화량)

---

### 🛠 5. 회로 연결 (Wiring Diagram)

| 센서/부품 | Arduino 핀 | 비고 |
| :--- | :--- | :--- |
| **LDR** | **A0** (아날로그 0) | 풀다운(Pull-down) 저항($10\text{k}\Omega$) 구성: $5\text{V} \rightarrow \text{LDR} \rightarrow \text{A0} \rightarrow 10\text{k}\Omega \rightarrow \text{GND}$ |
| **DHT11 Data** | **D2** (디지털 2) | VCC $\rightarrow 5\text{V}$, GND $\rightarrow \text{GND}$ |
| **Piezo Buzzer (+)** | **D8** (디지털 8) | $\text{Buzzer}(-) \rightarrow \text{GND}$ |

-----

### 💻 6. Arduino 코드

> **주의:** 임계값 (`TEMP_THRESHOLD`, `LDR_CHANGE_THRESHOLD`)은 설치 환경에 따라 튜닝이 필요합니다.

```cpp
#include <math.h>

void setup() {
  Serial.begin(9600);
  // 시작음
  tone(4, 523, 500); // 도
  delay(600);
  tone(4, 987, 500); // 시
  delay(600);
}

double th(int v) {
  double t;
  t = log(((10240000.0 / v) - 10000.0));
  t = 1.0 /(0.001129148 + (0.000234125*t) + (0.0000000876741*t*t*t));
  t = t - 273.15; // 켈빈 -> 섭씨
  return t;
}

void loop() {
  int a = analogRead(A0);  // 조도
  int s = analogRead(A2);  // 온도용 서미스터값
  double tempC = th(s);

  // 1) 시리얼 모니터용(있어도 상관 없음)
  Serial.print("LDR: ");
  Serial.print(a);
  Serial.print("  Temp: ");
  Serial.println(tempC);

  // 2) Processing에서 파싱하기 좋은 CSV 한 줄
  Serial.print(a);
  Serial.print(",");
  Serial.println(tempC);

  delay(500);
}

```

-----

### 🧪 7. 테스트 시나리오 및 결과

| 테스트 조건 | 변화 내용 | 결과 |
| :--- | :--- | :--- |
| **정상 모니터링** | 온도 정상 + 조도 변화 없음 | **부저 OFF** |
| **조도만 변화** | 밝기 급변 (예: 전등 ON/OFF) + 온도 정상 | **부저 OFF** (오탐 방지) |
| **온도만 변화** | 온도 상승 (예: 히터 근처) + 조도 변화 없음 | **부저 OFF** (오탐 방지) |
| **조도 + 온도 변화** | 둘 다 임계값 초과 (예: 라이터/촛불 접근) | **부저 ON** (화재 감지 성공) |

-----

### 🧠 8. 장점 및 확장 가능성

  * **쉬운 시연:** 연기 센서 없이도 **열과 급격한 밝기 변화**를 이용해 화재 상황을 시뮬레이션 가능.
  * **구현 용이성:** 기본적인 아두이노 센서만 사용하여 구현 난이도가 낮음.
  * **IoT 확장성:** **ESP32**나 **ESP8266** 같은 Wi-Fi 모듈을 추가하고 클라우드(Firebase 등)와 연동하여 **원격 알림(Push Notification)** 기능을 갖춘 완전한 IoT 시스템으로 확장이 용이함.
---

### 🚨 9. 한계 및 개선 사항

  * **정확도 한계:** 실제 **연기(Smoke)** 센서가 없어 판단 정확도가 제한적임.
      * 👉 **개선 방안:** **MQ-2 가스/연기 센서**를 추가하여 정확도를 높일 수 있음.
  * **환경 의존성:** 임계값 설정이 환경에 따라 수동 튜닝이 필요함.
  * **완전한 IoT 시스템으로의 발전:** 서버 연동을 통해 원격 모니터링 및 제어 기능 도입 필요.
