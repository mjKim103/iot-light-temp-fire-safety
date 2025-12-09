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
| **경보 출력** | **피에조 부저 (Piezo Buzzer)** | 화재 위험 시 경고 알람 출력 |
| **제어 보드** | **Arduino UNO** | 센서 데이터 처리 및 로직 실행 |

-----

### 🔥 4. 화재 위험 판단 로직

본 시스템은 두 가지 위험 요소가 동시에 충족될 때만 화재로 판단하여 **오탐률을 최소화**합니다.

$$
\text{IF } (\text{Temperature} > T_{\text{threshold}}) \quad \text{AND} \quad (\text{Light\_Change} > L_{\text{threshold}}) \\
\rightarrow \text{Fire} = \text{TRUE} \\
\rightarrow \text{Piezo Buzzer ON}
$$

  * $T_{\text{threshold}}$: 설정된 **온도 임계값** (예: $30^\circ\text{C}$)
  * $L_{\text{threshold}}$: 설정된 **조도 변화 임계값** (직전 값 대비 변화량)

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
#include <DHT.h>

// 핀 설정
#define DHTPIN 2
#define DHTTYPE DHT11
#define BUZZER_PIN 8
#define LDR_PIN A0

// 임계값 설정 (환경에 따라 튜닝 필요)
// T_threshold: 설정된 온도 임계값 (예: 30.0 °C)
const float TEMP_THRESHOLD = 30.0; 
// L_threshold: 조도 변화 임계값 (직전 값 대비 변화량)
const int LDR_CHANGE_THRESHOLD = 150; 

DHT dht(DHTPIN, DHTTYPE);
int previousLDRValue = 0; // 직전 조도 값 저장

void setup() {
  Serial.begin(9600);
  // DHT 라이브러리 초기화
  dht.begin();
  // 부저 핀 출력 설정
  pinMode(BUZZER_PIN, OUTPUT);
  // 초기 조도 값 설정
  previousLDRValue = analogRead(LDR_PIN);
}

void loop() {
  // 온도 측정
  float temp = dht.readTemperature();

  // 조도 측정
  int currentLDRValue = analogRead(LDR_PIN);
  // 조도 변화량 계산: 절대값 사용
  int ldrChange = abs(currentLDRValue - previousLDRValue); 

  // 데이터 출력 (모니터링)
  if (isnan(temp)) {
    Serial.println("Failed to read from DHT sensor!");
  } else {
    Serial.print("Temp: "); Serial.print(temp); Serial.print(" °C | LDR: "); Serial.print(currentLDRValue);
    Serial.print(" | LDR Change: "); Serial.println(ldrChange);
  }


  // --- 화재 판단 로직 ---
  // 온도 임계값 초과 AND 조도 변화 임계값 초과 시
  if (temp > TEMP_THRESHOLD && ldrChange > LDR_CHANGE_THRESHOLD) {
    // 화재 위험 감지
    Serial.println("!!! FIRE DETECTED !!!");
    digitalWrite(BUZZER_PIN, HIGH); // 부저 ON
    // 경보 지속 시간 (예: 1초)
    delay(1000); 
  } else {
    // 정상 모니터링
    digitalWrite(BUZZER_PIN, LOW); // 부저 OFF
  }
  
  // 현재 조도 값을 다음 루프의 이전 값으로 저장
  previousLDRValue = currentLDRValue;
  
  // 모니터링 간격
  delay(2000); 
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

### 🚨 9. 한계 및 개선 사항

  * **정확도 한계:** 실제 **연기(Smoke)** 센서가 없어 판단 정확도가 제한적임.
      * 👉 **개선 방안:** **MQ-2 가스/연기 센서**를 추가하여 정확도를 높일 수 있음.
  * **환경 의존성:** 임계값 설정이 환경에 따라 수동 튜닝이 필요함.
  * **완전한 IoT 시스템으로의 발전:** 서버 연동을 통해 원격 모니터링 및 제어 기능 도입 필요.
