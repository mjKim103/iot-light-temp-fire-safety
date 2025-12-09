📘 IoT 기반 조도/온도 스마트 방재 시스템

조도·온도 센서를 활용한 실시간 화재 위험 감지 시스템
(Embedded System Final Project)

🧭 개요 Overview

화재는 짧은 시간 안에 매우 큰 피해를 줄 수 있으므로
조기 위험 탐지가 무엇보다 중요합니다.

🔎 본 프로젝트는 **조도 센서(LDR)**와 온도 센서(DHT11/LM35) 를 이용하여
온도 상승 + 조도 급변이 동시에 발생할 경우 화재 위험으로 판단하고
피에조 부저로 알림을 제공하는 시스템입니다.

✨ 핵심 기능 요약
기능 설명
🔥 화재 위험 판단	온도 + 조도 변화 결합
🔔 부저 경보	자동 알람 응답
📊 실시간 모니터링	센서값 출력 및 위험 상태 확인
🧪 테스트 가능	라이터/촛불/손으로 쉽게 시연
🧩 System Diagram (텍스트형 UI)
flowchart TD
A[조도 센서 LDR] --> C(Arduino)
B[온도 센서 DHT11] --> C
C --> D{화재 판단 로직}
D -->|위험| E[부저 Alarm]
D -->|정상| F[모니터링]


위 다이어그램은 GitHub에서 자동으로 렌더링 됩니다 👍

🔗 Hardware List
부품	역할
Arduino UNO	MCU
LDR	조도 측정
DHT11/LM35	온도 측정
Piezo	경보 출력
10kΩ Resistor	분압 회로
🧲 회로 연결(Circuit)
<div align="center">
5V --- LDR ---- A0 ---- 10kΩ ---- GND

DHT VCC → 5V
DHT GND → GND
DHT DATA → D2

Piezo + → D8
Piezo - → GND

</div>
🔥 Fire Detection Algorithm
IF 온도 > 임계값 AND 조도 변화량 > 기준치
→ 부저 활성화


온도 + 빛 변화의 조합으로 판단하는 실험적 방재 방식

📜 Arduino Code
#include <DHT.h>
...
// 생성했던 코드 그대로 (생략)

📡 실험 시나리오 UI
● Case 1 : 정상 상태

🟢 온도 정상
🟢 조도 변화 없음
➡ 부저 OFF

● Case 2 : 조도 급상승

🟡 조도 상승
🟢 온도 정상
➡ 부저 OFF (조건 미충족)

● Case 3 : 조도 급감

🟡 조도 급감
🟢 온도 정상
➡ 부저 OFF

● Case 4 : 조도 급변 + 온도 상승

🔴 위험 탐지
🔴 부저 ON
➡ 성공 시연

🏆 장점 (UI 카드 형식)
👍 센서가 없어도 테스트 가능

라이터·촛불·핸드폰 플래시로 가능

👍 개발 난이도 낮음

아두이노 초급 구성

👍 IoT 확장 쉬움

Wi-Fi / App 연동 가능

📦 GitHub 구조(아이콘표 UI)
📂 SmartFireGuard
 ┣ 📁 arduino
 ┣ 📁 docs
 ┣ 📁 hardware
 ┣ 📁 demo
 ┗ README.md

📌 결론 Conclusion

✔ 조도 + 온도만으로도 실험적 화재 위험 탐지가 가능했고
✔ 센서 한계를 창의적으로 보완한 IoT 시스템을 완성하였으며
✔ 향후 MQ-2, 앱, 서버를 추가하여 더 정교한 서비스로 발전 가능
