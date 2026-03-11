
# IIC_BO - Lens Work Management (렌즈 작업 관리) 

## 📎 프로토 타입 링크
🔗 [프로토 타입 바로가기] : https://v0-lens-work-management.vercel.app/

## 📌 개요
승인된 온, 오프라인 렌즈 Rx 주문을 조회할 수 있으며, 주문별 작업 현황을 관리할 수 있는 메뉴입니다.

## 주요 기능

### 1. 주문 목록 조회
- 온라인 / 오프라인 채널별 RX 주문 목록 조회
- 페이지당 행 수 조정 및 페이지네이션 지원

### 2. 필터 및 검색
- 스토어 필터 (All / 개별 스토어 선택)
- 채널 필터 (온라인 / 오프라인)
- 작업 유형 필터 (IN HOUSE / OUTSOURCED)
- 처리 기간 필터
- 작업 상태 필터 (Pending / Inspection / In Progress / Completed / Finalized)
- 날짜 범위 검색 (Today / 1 Week / 1 Month / 3 Months)
- 키워드 검색 (Store Code, Store Name, Order No., Number)

### 3. 작업 현황 통계
- 처리 기간 기준 랩별 주문 수량 통계 표시
  1. (Basic) IIC Lab — 표준 처리 7~10일
  2. (Basic) Lab 1 — 추가 소요 10~14일
  3. (Basic) Lab 2 — 추가 소요 12~16일
  4. (Tint) IIC Lab — 표준 처리 9~12일
  5. (Tint) Lab 1 — 표준 처리 12~16일
  6. (Tint) Lab 2 — 표준 처리 14~18일

### 4. 주문 상세 정보
- 주문일 / 승인일
- 채널 (Online / Offline)
- 주문 번호 및 넘버
- 주문 유형 (Normal / Pre-order)
- 스토어 코드 및 스토어명
- 작업 상태
- 작업 유형 (IN HOUSE / OUTSOURCED)
- 처리 기간 및 완료일
- 담당 작업자

### 5. 출하 등록 및 다운로드
- 출하 대상 선택 (To Customer / To Store)
- 선택 주문 출하 등록
- 주문 목록 다운로드

### 6. 인쇄
- 개별 주문 Invoice 출력
