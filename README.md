📘 IoT 기반 조도·온도 스마트 방재 시스템
Light & Temperature-based Smart Fire Detection System
<br>
🧭 프로젝트 개요

화재는 초기 대응 여부가 피해 규모를 크게 좌우하기 때문에, 조기 감지가 무엇보다 중요합니다.
본 프로젝트는 조도 센서(LDR) 와 온도 센서(DHT11/LM35) 를 이용하여 실시간으로 환경 변화를 모니터링하고,

📌 “온도 상승 + 조도 급변”이 동시에 발생하면 화재 위험으로 판단하는 IoT 기반 방재 시스템입니다.

<br>
🎯 핵심 기능 요약

✔ 실시간 온도/조도 모니터링
✔ 화재 조기 감지
✔ 자동 부저 경보
✔ 손쉽게 시연 가능
✔ IoT 확장 가능

<br>
🔧 사용 센서
센서	기능
🌡 온도 센서 (DHT11/LM35)	열 감지
💡 조도 센서 (LDR)	밝기 변화 감지
🔔 피에조 부저	알람 출력
<br>
🔥 화재 위험 판단 로직
IF 온도 > T_임계값  AND  조도변화 > L_임계값
→ Fire = TRUE
→ 부저 ON

<br>
🧩 System Diagram
flowchart TD
A[LDR / Light Sensor] --> C(Arduino)
B[DHT11 / Temp Sensor] --> C
C --> D{Fire Detected?}
D -->|YES| E[Piezo Buzzer ON]
D -->|NO| F[Monitoring]

<br>
🛠 회로 연결
📌 LDR  
5V --- LDR ---- A0 ---- 10kΩ ---- GND  

📌 DHT11  
VCC → 5V  
GND → GND  
DATA → D2  

📌 Piezo  
+ → D8  
- → GND  

<br>
💻 Arduino 코드
#include <DHT.h>
...

(생략 — 위 코드 그대로 사용)

<br>
🧪 테스트 시나리오
테스트	조건	결과
정상	온도 정상 + 조도 변화 없음	부저 OFF
조도만 변화	온도 정상	부저 OFF
온도만 변화	조도 변화 없음	부저 OFF
조도 + 온도 변화	둘 다 변화	부저 ON
<br>
🧠 이 시스템의 장점
🔹 센서가 없어도 화재 실험 가능

촛불 / 라이터 / 휴대폰 플래시 모두 가능

🔹 구현 난이도 낮음

아두이노 초급자도 가능

🔹 IoT 확장이 매우 쉬움

App Inventor, ESP32 등과 연계

<br>
📦 GitHub Repository 구조
📂 SmartFireGuard
 ┣ 📁 arduino
 ┣ 📁 docs
 ┣ 📁 hardware
 ┣ 📁 demo
 ┗ README.md

<br>
🚨 한계 및 개선 사항

실제 연기 센서가 없기 때문에 판단 정확도는 제한적

온도/조도 임계값 환경별 튜닝 필요

MQ-2 추가 시 정확도 상승

서버(App) 연동하면 완전한 IoT 시스템으로 확장 가능

<br>
📌 결론

본 프로젝트는
“조도 + 온도 융합 기반 화재 탐지 시스템”을 구현함으로써

✔ 센서 데이터 수집
✔ 위험 판단
✔ 경보 실행

이라는 임베디드 시스템 핵심 구조를 모두 경험할 수 있었으며
IoT로 확장할 수 있는 기반 기술을 다뤘다는 점에서 의미 있는 프로젝트임.

<br>
