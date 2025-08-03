# MCU 데이터시트를 통한 BaudRate 설정

## Aurix MCU 개요

### 제조사 및 특징
- **제조사**: Infineon Technologies (독일)
- **제품군**: Aurix (TC2x, TC3x, TC4x 시리즈)
- **CPU 아키텍처**: TriCore
- **주요 용도**: 자동차 제어 시스템

### Aurix의 장점
- **고성능**: 멀티코어 아키텍처로 복잡한 제어 가능
- **안전성**: ISO 26262 ASIL-D 등급 지원
- **통합성**: 다양한 페리퍼럴 내장
- **신뢰성**: 자동차 환경에 최적화된 설계

<br>

## 자동차 업계에서의 Aurix 활용

### 주요 기업 채용 동향

#### 현대자동차그룹
- **현대자동차**: 전기차 구동모터 제어 소프트웨어 개발
  - 우대사항: Infineon MCU 개발 경험자
- **현대모비스**: 전자제어 시스템 개발
- **현대트랜시스**: 변속기 제어 시스템

#### 전장부품 업체
- **LG Magna**: 전기차 인버터/컨버터 소프트웨어 개발
  - 우대사항: Infineon TriCore MCU 경험자
- **LG이노텍**: 모터 제어 소프트웨어 개발
  - 우대사항: Aurix 사용 경험자
- **Mando**: 자율주행 및 제동 시스템
  - 우대사항: Aurix 사용 경험자

#### 기타 주요 기업
- **현대케피코**: 엔진/파워트레인 제어
- **삼성SDI**: 배터리 관리 시스템
- **LG에너지솔루션**: 배터리 제어 시스템

### 적용 분야
- **파워트레인**: 엔진, 변속기, 하이브리드 시스템
- **전기차**: 인버터, 컨버터, 모터 제어
- **섀시**: 브레이크, 조향, 서스펜션
- **ADAS**: 자율주행 보조 시스템
- **배터리**: BMS (Battery Management System)

<br>

## Aurix MCU 데이터시트 구조

### 사용자 매뉴얼 구성
- **General Information**: MCU 개요 및 기본 사양
- **Memory Organization**: 메모리 맵 및 구조
- **Peripheral Modules**: 각종 페리퍼럴 상세 설명
- **System Units**: 시스템 제어 및 관리
- **Application Information**: 실제 사용 예제

### 페리퍼럴 모듈 예시
- **CAN Controller**: CAN 통신 제어
- **UART/LIN**: 시리얼 통신
- **SPI/I2C**: 동기식 통신
- **ADC**: 아날로그-디지털 변환
- **PWM**: 펄스 폭 변조
- **Timer**: 시간 측정 및 제어
- **DMA**: 직접 메모리 접근

<br>

## CAN 컨트롤러 데이터시트 분석

### CAN 컨트롤러 챕터 구성
- **Overview**: CAN 컨트롤러 개요
- **Functional Description**: 기능별 상세 설명
- **Register Description**: 레지스터 맵 및 설정
- **Programming Guide**: 실제 사용 방법
- **Application Examples**: 구현 예제

### 비트 타이밍 섹션
데이터시트에서 확인할 수 있는 내용:
- **비트 구조 다이어그램**: TSEG1, TSEG2, 샘플링 포인트 표시
- **타이밍 파라미터**: 각 세그먼트의 역할 및 설정 범위
- **계산 공식**: BaudRate 및 샘플링 포인트 계산 방법

<br>

## 레지스터를 통한 BaudRate 설정

### CAN 비트 타이밍 레지스터 (예: CAN_NBTR)

#### 레지스터 구조
```
Bit Field    | Bits  | Description
-------------|-------|------------------
NTSEG2       | 6:0   | Nominal Time Segment 2
NTSEG1       | 15:8  | Nominal Time Segment 1
NBRP         | 24:16 | Nominal Baud Rate Prescaler
NSJW         | 31:25 | Nominal Synchronization Jump Width
```

#### 설정 가능 범위
- **NTSEG1**: 1~256 Time Quantum
- **NTSEG2**: 1~128 Time Quantum
- **NSJW**: 1~128 Time Quantum
- **NBRP**: 1~512 (분주비)

### 실제 설정 예시

#### 500kbps BaudRate 설정
```c
// CAN 클럭: 20MHz 가정
// 목표 BaudRate: 500kbps
// 샘플링 포인트: 87.5%

// 계산 과정:
// Time Quantum = 20MHz / (NBRP × BaudRate × Total_Tq)
// 500kbps = 20MHz / (2 × 40) → Total_Tq = 40

#define CAN_NBRP    1       // Baud Rate Prescaler - 1
#define CAN_NTSEG1  34      // Time Segment 1 (35 Tq including Sync)
#define CAN_NTSEG2  5       // Time Segment 2
#define CAN_NSJW    1       // Synchronization Jump Width

// 레지스터 설정
CAN_NBTR = (CAN_NSJW << 25) | (CAN_NBRP << 16) | 
           (CAN_NTSEG1 << 8) | CAN_NTSEG2;

// 샘플링 포인트 = (1 + 34) / 40 = 87.5%
```

#### 250kbps BaudRate 설정
```c
// 목표 BaudRate: 250kbps
// 샘플링 포인트: 75%

#define CAN_NBRP    1       // Baud Rate Prescaler - 1
#define CAN_NTSEG1  59      // Time Segment 1 (60 Tq including Sync)
#define CAN_NTSEG2  20      // Time Segment 2
#define CAN_NSJW    4       // Synchronization Jump Width

// Total_Tq = 80, Sampling Point = 60/80 = 75%
```

### 레지스터 설정 절차

#### 1. CAN 컨트롤러 초기화 모드 진입
```c
// Configuration Change Enable
CAN_CCCR |= CAN_CCCR_CCE;
// Initialization Mode
CAN_CCCR |= CAN_CCCR_INIT;
// Wait for initialization complete
while(!(CAN_CCCR & CAN_CCCR_INIT));
```

#### 2. 비트 타이밍 레지스터 설정
```c
// Nominal Bit Timing Register 설정
CAN_NBTR = (NSJW << 25) | (NBRP << 16) | 
           (NTSEG1 << 8) | NTSEG2;

// Data Bit Timing Register 설정 (CAN FD의 경우)
CAN_DBTP = (DSJW << 4) | (DTSEG2 << 0) | 
           (DTSEG1 << 8) | (DBRP << 16);
```

#### 3. 정상 동작 모드 전환
```c
// Normal Operation Mode
CAN_CCCR &= ~CAN_CCCR_INIT;
// Configuration Change Disable
CAN_CCCR &= ~CAN_CCCR_CCE;
```

<br>

## 클럭 소스 고려사항

### CAN 클럭 소스 선택

#### 내부 오실레이터
- **장점**: 외부 부품 불필요, 비용 절약
- **단점**: 정확도 낮음, 온도 특성 불량
- **용도**: 저속 통신, 비중요 시스템

#### 외부 크리스털
- **장점**: 높은 정확도, 안정된 주파수
- **단점**: 외부 부품 필요, 비용 증가
- **용도**: 고속 통신, 중요 시스템

#### PLL (Phase-Locked Loop)
- **장점**: 유연한 주파수 생성, 높은 정확도
- **단점**: 복잡한 설정, 잠금 시간 필요
- **용도**: 다양한 클럭 주파수 요구 시스템

### 클럭 정확도 요구사항
- **CAN 표준**: ±1.58% 이내의 클럭 정확도 권장
- **실제 설계**: ±0.5% 이내로 설계하는 것이 안전
- **온도 보상**: 전체 동작 온도 범위에서 정확도 유지

<br>

## 실제 구현 시 고려사항

### 하드웨어 설계
- **클럭 소스**: 적절한 정확도의 클럭 선택
- **전원 노이즈**: 깨끗한 전원 공급으로 클럭 안정성 확보
- **PCB 레이아웃**: 클럭 신호 경로 최적화

### 소프트웨어 구현
- **초기화 순서**: 올바른 레지스터 설정 순서 준수
- **에러 처리**: 초기화 실패 시 재시도 메커니즘
- **동적 변경**: 런타임 중 BaudRate 변경 시 주의사항

### 테스트 및 검증
- **시뮬레이션**: 설정값의 이론적 검증
- **실측**: 오실로스코프를 통한 실제 타이밍 측정
- **호환성**: 다른 제조사 제품과의 상호 운용성 확인

<br>

## MCU별 차이점

### 레지스터 구조
- **비트 필드**: MCU마다 다른 비트 할당
- **설정 범위**: 지원하는 파라미터 범위 차이
- **명명 규칙**: 레지스터 및 필드 이름 차이

### 클럭 구조
- **분주기**: 내부 분주기의 구조 및 설정 방법
- **소스 선택**: 사용 가능한 클럭 소스 종류
- **정확도**: 내부 오실레이터의 정확도 사양

### 추가 기능
- **CAN FD**: 고속 데이터 전송 지원 여부
- **TTL**: Time-Triggered 통신 지원
- **진단**: 내장 자가진단 기능

<br>

## 개발 도구 활용

### 설정 도구
- **ConfigTool**: MCU 제조사 제공 설정 도구
- **Pin Planner**: 핀 배치 및 기능 설정
- **Clock Wizard**: 클럭 구조 설정 도구

### 디버깅 도구
- **Debugger**: 실시간 레지스터 값 확인
- **Trace**: CAN 통신 동작 추적
- **Profiler**: 성능 분석 및 최적화

### 시뮬레이션
- **Virtual Platform**: 가상 환경에서 동작 검증
- **Model-in-the-Loop**: 시뮬레이션 모델과 연동
- **Hardware-in-the-Loop**: 실제 하드웨어와 연동

<br>

## 핵심 요약

1. **Aurix MCU**: 자동차 업계 표준 MCU로 널리 사용
2. **데이터시트 활용**: CAN 컨트롤러 챕터에서 설정 방법 확인
3. **레지스터 설정**: TSEG1, TSEG2, SJW, BRP 값을 통한 BaudRate 제어
4. **클럭 고려사항**: 정확한 클럭 소스 선택이 통신 품질의 핵심
5. **실무 적용**: 이론적 계산과 실제 측정을 통한 검증 필수
6. **MCU별 차이**: 제조사별 레지스터 구조 및 설정 방법 상이
7. **도구 활용**: 개발 효율성을 위한 적절한 도구 선택 중요