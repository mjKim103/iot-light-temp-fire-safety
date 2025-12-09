💡 IoT 기반 조도·온도 스마트 방재 시스템 (Light & Temperature-based Smart Fire Detection System)🌟 프로젝트 개요화재는 초기 대응이 피해 규모를 크게 좌우하므로 조기 감지가 무엇보다 중요합니다. 본 프로젝트는 **조도 센서 (LDR)**와 **온도 센서 (DHT11/LM35)**를 활용하여 실시간 환경 변화를 모니터링하고, **"온도 상승 + 조도 급변"**이 동시에 발생할 경우를 화재 위험으로 판단하는 IoT 기반 스마트 방재 시스템입니다.🎯 핵심 기능 요약실시간 모니터링: 온도 및 조도 변화 실시간 감지화재 조기 감지: 융합 센서 로직을 통한 신속한 위험 판단자동 부저 경보: 위험 상황 발생 시 즉각적인 경고음 출력손쉬운 시연: 별도의 연기 없이 촛불, 라이터 등으로 간편 테스트 가능IoT 확장 가능: ESP32, App Inventor 등과의 연계를 고려한 설계🔧 사용 하드웨어 및 센서구분센서/부품기능온도 감지DHT11 / LM35주변 환경의 열(온도) 변화 감지밝기 감지LDR (Light Dependent Resistor)주변 환경의 밝기(조도) 변화 감지경보 출력피에조 부저 (Piezo Buzzer)화재 위험 시 경고 알람 출력제어 보드Arduino UNO센서 데이터 처리 및 로직 실행🔥 화재 위험 판단 로직본 시스템은 두 가지 위험 요소가 동시에 충족될 때만 화재로 판단하여 오탐률을 최소화합니다.$$\text{IF } (\text{Temperature} > T_{\text{threshold}}) \quad \text{AND} \quad (\text{Light\_Change} > L_{\text{threshold}}) \\
\rightarrow \text{Fire} = \text{TRUE} \\
\rightarrow \text{Piezo Buzzer ON}$$$T_{\text{threshold}}$: 설정된 온도 임계값 (예: $30^\circ\text{C}$)$L_{\text{threshold}}$: 설정된 조도 변화 임계값 (직전 값 대비 변화량)🛠 회로 연결 (Wiring Diagram)센서/부품Arduino 핀비고LDRA0 (아날로그 0)풀다운(Pull-down) 저항($10\text{k}\Omega$) 구성: $5\text{V} \rightarrow \text{LDR} \rightarrow \text{A0} \rightarrow 10\text{k}\Omega \rightarrow \text{GND}$DHT11 DataD2 (디지털 2)VCC $\rightarrow 5\text{V}$, GND $\rightarrow \text{GND}$Piezo Buzzer (+)D8 (디지털 8)$\text{Buzzer}(-) \rightarrow \text{GND}$💻 Arduino 코드 (핵심 부분)주변 환경에 맞게 임계값을 설정하여 사용해야 합니다.C++#include <DHT.h>

// 핀 설정
#define DHTPIN 2
#define DHTTYPE DHT11
#define BUZZER_PIN 8
#define LDR_PIN A0

// 임계값 설정 (환경에 따라 튜닝 필요)
const float TEMP_THRESHOLD = 30.0; // 온도 임계값 (예: 30.0 °C)
const int LDR_CHANGE_THRESHOLD = 150; // 조도 변화 임계값 (직전 대비 변화량)

DHT dht(DHTPIN, DHTTYPE);
int previousLDRValue = 0; // 직전 조도 값 저장

void setup() {
  Serial.begin(9600);
  dht.begin();
  pinMode(BUZZER_PIN, OUTPUT);
}

void loop() {
  // 온도 측정
  float temp = dht.readTemperature();

  // 조도 측정
  int currentLDRValue = analogRead(LDR_PIN);
  int ldrChange = abs(currentLDRValue - previousLDRValue); // 조도 변화량 계산

  // 데이터 출력 (선택 사항)
  Serial.print("Temp: "); Serial.print(temp); Serial.print(" °C | LDR: "); Serial.print(currentLDRValue);
  Serial.print(" | LDR Change: "); Serial.println(ldrChange);

  // 화재 판단 로직
  if (temp > TEMP_THRESHOLD && ldrChange > LDR_CHANGE_THRESHOLD) {
    // 화재 위험 감지
    Serial.println("!!! FIRE DETECTED !!!");
    digitalWrite(BUZZER_PIN, HIGH); // 부저 ON
    // 1초 간격으로 경보음을 울림
    delay(1000); 
  } else {
    // 정상 모니터링
    digitalWrite(BUZZER_PIN, LOW); // 부저 OFF
  }
  
  // 현재 조도 값을 다음 루프의 이전 값으로 저장
  previousLDRValue = currentLDRValue;
  delay(2000); // 2초 간격으로 모니터링
}
🧪 테스트 시나리오 및 결과테스트 조건변화 내용결과정상 모니터링온도 정상 + 조도 변화 없음부저 OFF조도만 변화밝기 급변 (예: 전등 ON/OFF) + 온도 정상부저 OFF (오탐 방지)온도만 변화온도 상승 (예: 히터 근처) + 조도 변화 없음부저 OFF (오탐 방지)조도 + 온도 변화둘 다 임계값 초과 (예: 라이터/촛불 접근)부저 ON (화재 감지 성공)🧠 이 시스템의 장점실제 연기 센서 대체 가능: 연기 센서 없이도 라이터나 촛불의 열과 급격한 밝기 변화를 이용하여 화재 상황을 시뮬레이션 및 테스트 가능.쉬운 구현 난이도: 기본적인 아두이노 센서만 사용하여 초급자도 쉽게 구현 및 이해 가능.IoT 확장성: ESP32나 ESP8266 같은 Wi-Fi 모듈과 연계하여 센서 데이터를 서버나 모바일 앱(App Inventor)으로 전송하는 완전한 IoT 시스템으로 확장이 용이함.🚨 한계 및 개선 사항구분한계점개선 방안정확도실제 연기(Smoke) 센서가 없어 판단 정확도가 제한적임.MQ-2 가스/연기 센서를 추가하여 연기 감지 로직을 도입.환경 의존성온도/조도 임계값이 설치 환경에 따라 수동 튜닝이 필요함.초기 환경 값을 학습하는 자율 임계값 설정 로직 또는 머신러닝 기반의 패턴 인식 도입.IoT 완성도단순 경보 시스템에 그치며, 원격 알림 기능이 없음.웹 서버 또는 클라우드 플랫폼(Firebase 등)과 연동하여 원격 알림(Push Notification) 기능 추가.📌 결론본 프로젝트는 **"조도 + 온도 융합 기반 화재 탐지 시스템"**을 구현함으로써 센서 데이터 수집, 위험 판단, 경보 실행이라는 임베디드 시스템의 핵심 구조를 모두 경험할 수 있었습니다. 특히, 두 가지 센서의 융합 로직을 통해 오탐률을 줄이고 IoT로 확장할 수 있는 기반 기술을 다뤘다는 점에서 의미 있는 프로젝트입니다
