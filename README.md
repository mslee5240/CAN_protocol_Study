# CAN Protocol Study

## CAN 통신 개요

### CAN 통신이란?
CAN(Controller Area Network) 통신은 독일의 자동차 부품 회사인 **보쉬(Bosch)**에서 차량 내 컨트롤러 간 통신을 위해 개발한 통신 기술.

### 개발 배경 및 필요성
- 최신 자동차에서는 다양한 기능들이 전자 장치와 컨트롤러로 제어. 
- 이러한 컨트롤러들이 효율적으로 작동하려면 서로 간의 원활한 정보 교환이 필수적이기 때문에 컨트롤러 간 통신의 중요성이 크게 증가.

## 목차
- CAN 통신 등장 배경 및 개요
- 토폴로지 & 전송 방식
- 커넥터
- 종단저항
- CAN High & CAN Low signal
- CAN Tranceiver & CAN Controller
- CAN 트랜시버 (TJA1043) 데이터시트
- 통신속도 : BaudRate
- (TSEG1, TSEG2, SJW 의미)
- Aurix MCU 데이터 시트

<br>

- 메세지의 ID
- 메세지의 우선순위
- Bus Load
- 메세지의 DLC 와 시그널
- 시그널의 Factor & Offset

<br>

- 에러 처리 개요
- 에러 감지 및 반응 (Error state)
- 에러의 종류
- Aurix MCU 데이터 시트

<br>

- CAN FD
- CAN DB & CAN DBC 파일
- CAN DBC 파일 직접 만들어보기
- Canoe (시뮬레이션 툴)

<br>

- E2E Protocol
- E2E 프로토콜 개념
- E2E 카운터(Counter)
- E2E 프로파일(Profile)
- CRC 개념
- E2E에서의 CRC 활용
- E2E Data ID 개념
- E2E 관련 기타 정보