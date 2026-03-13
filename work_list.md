# {RX > Lens Work Management} 화면 상세 기획서

> **상태**: `In Review`
> **프로토타입 URL**: [프로토타입 바로가기](https://v0-lens-work-management.vercel.app/)

## 목차

1. [개요](#1-개요)
2. [검색 영역](#2-검색-영역)
3. [리스트 탭](#3-리스트-탭)
4. [리스트 총 건수 및 다운로드](#4-리스트-총-건수-및-다운로드)
5. [테이블 정의](#5-테이블-정의)
6. [상세](#6-상세)
7. [개발 참고사항](#7-개발-참고사항)
8. [테스트 케이스](#8-테스트-케이스)
9. [미정 사항](#9-미정-사항)
10. [변경 이력](#10-변경-이력)

---

## 1. 개요

### 목적

> 컨펌된 온·오프라인 RX 주문만 모아 작업 상태를 관리할 수 있다.

### 적용 범위

- New (신규)

### 주요 용어

| 용어 | 설명 |
| ---- | ---- |
| RX | 고객 처방 정보에 맞춰진 커스텀 작업 렌즈 |
| C.O.F | Lens Only 구매 렌즈로 고객 프레임 사용 타입 (Customer Of Frame) |
| Pre-Order | 정해진 기간 내 사전예약을 받고, 고객이 수령 가능한 주문 유형 |

### 공통 사항

1. 계정 권한 그룹의 메뉴 조회 조건이 있어야 신규 메뉴 노출이 가능하다.
2. 필터 드롭다운 데이터 노출 기준은 계정에 설정된 브랜드 - 법인 - 스토어에 따라 반영된다.

### 사용자 (운영) 정책

1. Confirm된 최종 작업 목록으로 상세 정보를 수정할 수 없다.
2. To Customer / Store 출고 유형에 따라 배송을 위한 프로세스가 달리 처리되어야 한다.
   - **To Customer**: 배송 및 라벨 정보 전송 처리 후 Shipstation 배송 라벨 출력
   - **To Store**: 배송 및 라벨 정보 전송, 배송 라벨 출력 가능
3. Pre-Order의 경우, Launch Date (MM:DD 00:00) 기준으로 Outbound / Label 처리가 가능하다.

### 저장 정책

1. 이전 상세 정보 중 수정된 내용이 있으면 저장 버튼이 활성화되며 수정할 수 있다.

---

## 2. 검색 영역

### UI 형태

- 드롭다운(Dropdown) + 기간(Calendar Picker) + 텍스트(Text Input) 복합 형태

### 검색 필드 구성 — 드롭다운

| 순서 | 필드명 | 선택 유형 | 기본값 | 비고 |
| ---- | ------ | --------- | ------ | ---- |
| 1 | BP | Single Select | All | 권한 드롭다운 |
| 2 | Store | Multiple Select | All | 권한 드롭다운 |
| 3 | Channel | Single Select | All | |
| 4 | Work Type | Single Select | All | 빈값(-) 가능 |
| 5 | Processing Period | Multiple Select | All | 빈값(-) 가능 |
| 6 | Work Status | Single Select | All | |

### 검색 필드 구성 — 기간

| 순서 | 필드명 | 선택 유형 | 기본값 | 비고 |
| ---- | ------ | --------- | ------ | ---- |
| 1 | Date Type | Single Select | Order Date | |
| 2 | Start (시작일) | Calendar Picker | Today - 30D | |
| 3 | End (종료일) | Calendar Picker | Today | |

### 검색 필드 구성 — 텍스트

| 순서 | 필드명 | 유형 | 기본값 | 비고 |
| ---- | ------ | ---- | ------ | ---- |
| 1 | Store Code | Text Input | - | |
| 2 | Store Name | Text Input | - | |
| 3 | Order No. | Text Input | - | |
| 4 | Number # | Text Input | - | |

### 동작 정책

- **기본값**: 모든 드롭다운 검색 유형은 `All`을 기본값으로 고정한다.
- **기간**: `Order Date` 주문일을 기본값으로 고정하며, 조회 시점의 Today 기준 -30D 데이터를 기본 조회한다.
- **텍스트**: 최소 2자 이상 입력 시 검색을 허용하며, like 검색을 허용한다.
- BP 설정에 따라 Store 필드가 활성화된다.

---

## 3. 리스트 탭

### 탭 구성

작업 완료 후 ➊ 고객에게로 배송 또는 ➋ 스토어 픽업 유형에 따라 리스트를 구분한다. (출고 방식 상이)

| 탭 | 탭명 | 설명 |
| --- | ---- | ---- |
| 1 | To Customer | 온라인 주문 + 오프라인 / 고객 배송 유형 |
| 2 | To Store | 오프라인 / 스토어 배송 유형 |

### 탭 전환 정책

- 메뉴 이동 및 화면 리프레시를 제외하고, **탭 간 이동 시 페이지네이션이 유지** 되어야 한다.
- 페이지네이션 고정은 **페이지 번호(위치) 및 row 수** 상태 유지를 의미한다.
- **검색 조건이 반영된 상태로 탭 전환**이 된다.

---

## 4. 리스트 총 건수 및 다운로드

### 총 건수

- 표시 형식: `총 000 건`

### 다운로드 종류

`다운로드` 선택 시, 현재 필터 기준 전체 데이터 추출(리스트 다운로드)과 선택한 주문별 작업 라벨 출력(라벨 다운로드)을 선택할 수 있다.

- 주문별 라벨은 리스트 체크박스 선택이 필수이다.
- 리스트 다운로드는 체크박스 선택과 무관하다.

### 라벨 다운로드

라벨은 각 주문 정보가 담긴 실물 작업 구분을 위한 작업 라벨을 의미한다.

| 항목 | 정책 |
| ---- | ---- |
| 다운로드 범위 | 선택한 row 주문 |
| 페이징 처리 | 페이징 제외한 선택한 전체 데이터 |
| 빈 데이터 처리 | 없음 |
| 최대 다운로드 | **최대 50건** |

### 리스트 다운로드

| 항목 | 정책 |
| ---- | ---- |
| 다운로드 범위 | 리스트 필터 처리를 반영한 전체 데이터 |
| 페이징 처리 | 페이징 제외한 전체 데이터 |
| 빈 데이터 처리 | 없는 데이터는 빈값(`-`)으로 추출 |
| 최대 다운로드 | **최대 1만 건** |

---

## 5. 테이블 정의

### 정렬

| 항목 | 정책 |
| ---- | ---- |
| 기본 정렬 | {Order Date} 기준, **내림차순** 고정 |
| 정렬 방식 | 컬럼 클릭 시 내림차순 ↔ 오름차순 토글 |

### 컬럼 정의

| 순서 | 컬럼명 | 데이터 타입 | Nullable | 정렬 가능 | 비고 |
| ---- | ------ | ----------- | -------- | --------- | ---- |
| 0 | Checkbox | N/A | N | - | |
| 1 | Order Date | String | N | ✓ | |
| 2 | Approval Date | String | N | ✓ | |
| 3 | Channel | String | N | - | |
| 4 | Order No. | String | N | ✓ | |
| 4 | Number # | String | Y | - | |
| 5 | Order Type | String | N | - | |
| 6 | Store Info (Code / Name) | String | N | ✓ | |
| 7 | Work Status | String | N | ✓ | |
| 8 | Work Type | String | Y | - | |
| 9 | Processing Period | String | Y | ✓ | |
| 10 | Worker | String | Y | - | |
| 11 | Detail | String | N | - | 상세 화면 진입 |
| 12 | Invoice Print | String | N | - | 인쇄 팝업 모달 |
| 13 | Lead Time | String | N | ✓ | 툴팁 포함 |
| 14 | Completion Date | String | Y | ✓ | |

### 페이지네이션

| 항목 | 정책 |
| ---- | ---- |
| 페이지당 row 옵션 | 10 / 30 / 50 / 100 / 300 |
| 기본값 | **30 row** |

---

## 6. 상세

### 진입 조건

- `Detail` 컬럼 클릭 시 상세 페이지 진입

### 화면 레이아웃 정책

**헤더**
- Back (뒤로가기): 클릭 시 리스트로 전환되며, 리스트 필터 및 위치를 기억해 전환된다.
- Order No.
- Work Status: 리스트에 반영된 상태별 **컬러**에 맞게 노출한다.
- Channel

**좌측**
- Member Info
- Order Info
- Prescription | Additional Details | Comments (탭)

**우측**
- Status
- Delivery Tracking
- Status History

---

### Member Info

고객 멤버십 선택이 된 주문의 경우, country, membership이 선택된 상태로 조회된다.

| 정보 명 | 정의 |
| ------- | ---- |
| [Membership] Country | 고객 멤버십 국가 |
| [Membership] Email | 고객 멤버십 정보로, 마스킹 처리 |
| [Membership] Phone | 고객 멤버십 정보로, 마스킹 처리 |
| [Membership] EXP | 처방전 정보를 선택했다면, 해당 만료일자 정보를 노출한다. |
| [Membership] Prescription View | 처방전 정보를 선택했다면, Prescription View 버튼 유지 및 활성화되며, 클릭 시 레이어로 이미지가 플로팅 된다. |

---

### Order Info

| 정보 명 | 필수 / 예외 | 정의 |
| ------- | ----------- | ---- |
| Order Date | 필수 | 주문일자로 스토어 타임존을 반영한다. |
| Store info | 필수 | 주문의 원 스토어 정보로 코드 / 타이틀을 조회할 수 있다. |
| Launch Date | 예외 | Pre-Order일 때만 반영한다. |
| Type | 필수 | 제품별 소 카테고리 명칭을 의미한다. |
| Product info | 필수 | 제품 정보로, 제품코드 / 타이틀 / 바코드 정보를 조회할 수 있다. 바코드 정보는 Optional로 빈값일 수 있으며, `-`으로 표기한다. |
| Qty | 필수 | |
| Total Price | 필수 | |
| C.O.F | 예외 | 맵핑 정보로부터 isCustomerOwnFrame = `True`일 때만 체크박스 설정을 유지한다. |

**예외 사항**
- Order Type = `Pre-Order`일 때, **Launch Date 정보를 함께 노출한다.**
- **C.O.F**는 고객이 렌즈를 프레임보다 많이 구매할 때 발생한다. 고객이 **과거에 구매한 프레임으로 가공 처리하는 유형**을 의미하며, **오프라인만 해당**된다.

---

### Prescription | Additional Details | Comments (탭)

- 온라인의 경우, 주문에서 수신된 처방 정보로 구성된다.
- 오프라인의 경우, 매장 옵션 또는 고객 처방전 정보로 저장된 데이터다.
- 모든 정보는 Confirm된 상태로 조회만 가능하다.
- Comment는 해당 작업 건과 연동된 `POS - RX Operation View` 및 `RX - Prescription Review`에서 동일하게 확인할 수 있으므로, 스토어 직원 간의 정보 공유가 가능하다.

**📍 Prescription 테이블 정의**

| 항목 | SPH (근/원시 기본 도수) | CYL (난시 도수) | Axis (난시 교정 각도) | PD (동공 간 거리) | OC |
| ---- | ----------------------- | --------------- | --------------------- | ------------------ | -- |
| 필수값 | 필수 | 필수 | 필수 | 필수 | 선택 |
| 구분 | - | - | - | Single & Dual | - |
| 음수 | Y | Y | N | N | N |
| 소수점 | Y (0.25 단위) | Y (0.25 단위) | N | Y | N |
| 데이터 범위 | -10.00 ~ 5.00 | -6.00 ~ 0.00 | 1 ~ 180 | 없음 | 0 ~ 40 |

1. 멤버십 또는 직접 등록한 모든 처방 정보에 대해서는 조회만 가능하며, 수정이 불가하다.
2. 처방 정보와 함께 저장된 파일은 클릭 시 레이어로 플로팅 되며 확인할 수 있다.

**📍 Additional Details 정의**

고객 기본 정보, 주소 정보, 렌즈 가공 작업을 위한 의료 정보 수집 및 정책 동의 영역을 포함한다. **'주소 정보를 제외한' 모든 정보는 조회만 가능하다.**

고객 기본 / 주소 정보

| 필드 명 | 내용 |
| ------- | ---- |
| Name | |
| Phone | |
| Email | |
| Address Type | 주소 정보는 수정할 수 있지만, 타입을 수정할 수 없다. |
| Address | Label 출력 전까지 수정이 가능하다. |

정책 (체크박스)

| 체크박스 | 필수 | URL 첨부 |
| -------- | ---- | -------- |
| 기본 정책 | 필수 | Y |
| 의료 정보 수집 동의 (HIPAA) | 필수 | Y |
| 마케팅 | 선택 | N |

**📍 Comments 정의**

코멘트 기능을 통해 오프라인 주문의 경우 정보를 수집하는 스토어부터 작업자까지 해당 주문에 대한 히스토리나 노트를 주고받을 수 있다.

구성 요소

- **인풋 필드**: 공백을 제외한 최대 200자(1회) 등록이 가능하며, 최소 2자 이상 입력 시 등록할 수 있다.
- **Comment Save (BT)**: 최소 2자 이상 입력 시 활성화된다.
- **Comment 목록**: 스크롤로만 처리한다.
- **Comment Delete (BT)**: 저장 완료 후, 작성한 계정에게만 노출된다.

---

### 작업 관리 메뉴 (Status | Delivery Tracking | Status History)

**1. Status**

주문별 작업 상태이며, 수동 / 자동 처리로 상태를 변경할 수 있다.

- 구성: 드롭다운 (Single Select), 수동 설정이 가능한 상태만 반영된다.
- 설정 조건
  - **수동**: 작업자가 자유로이 수정이 가능하다.
  - **자동**: 특정 조건일 때 자동으로 처리된다.
    - Complete
      - To Customer: 송장 정보 등록 완료
      - To Store: 송장 출력 완료
    - Finalized
      - 온라인, 오프라인(To Customer): 출고 상태가 `배송중` 상태로 출고 완료 시점
      - 오프라인(To Store): 스토어에서 고객 인도 완료로, `Fulfillment` 상태 수신 시점

| 상태 (EN) | 상태 (KR) | 내용 | 설정 조건 |
| --------- | --------- | ---- | --------- |
| Pending | 대기 | 최초 상태이며, 작업 진행 전까지의 상태를 의미한다. | 기본값 / 수동 |
| Inbound Inspection | 입고 검수 | 실물 입고에 대한 검수 확인 상태를 의미한다. | 수동 |
| In Progress | 작업 중 | 실제 작업 처리중일 때의 상태를 의미한다. (외부 처리 시 활용) | 수동 |
| Outbound Inspection | 출고 검수 | 실물 출고 전 검수 단계의 상태를 의미한다. | 수동 |
| Complete | 작업 완료 | 최종 작업이 완료된 상태를 의미한다. | 자동 |
| Finalized | 종결 | 고객 인도 완료로 작업이 최종적으로 종결된 상태를 의미한다. | 자동 |

**2. Work Type**

IIC 내부 작업으로 이뤄지는 건인지, 업체를 통해 이뤄지는 건인지에 대한 태그값이며, 해당 상태는 언제든 수정이 가능하다.

| Type |
| ---- |
| INHOUSE |
| OUTSOURCED |

**3. Processing Period**

작업 유형별 소요되는 기간을 구간별로 나누어둔 카테고리로, 작업자가 자유로이 수정할 수 있다.

| 기간 범위 | 내용 |
| --------- | ---- |
| (Basic) IIC Lab | 기본 처리 기간 7 ~ 10 D |
| (Basic) Lab 1 | 일반 대비 추가 소요 10 ~ 14 D |
| (Basic) Lab 2 | 일반 대비 추가 소요 12 ~ 16 D |
| (Tint) IIC Lab | 기본 처리 기간 9 ~ 12 D |
| (Tint) Lab 1 | 기본 처리 기간 12 ~ 16 D |
| (Tint) Lab 2 | 기본 처리 기간 14 ~ 18 D |

**4. Delivery Tracking (출고 정보)**

해당 주문에 대한 출고를 등록해 최종 수신받은 **Tracking No. 와 배송사(carrier) 정보를 노출한다.**

- Tracking No.를 클릭하면 해당 배송사의 배송 추적 페이지가 새 창으로 열린다.

**5. Status History (상태 변경 이력)**

작업 상태가 변경될 때마다 히스토리를 저장한다.

- 변경한 상태
- 일시 (계정 타임존)
- 계정 정보 (ID)

---

### 상세 버튼 기능

| 버튼 명 | 내용 | 동작 |
| ------- | ---- | ---- |
| Work Picking Print | 작업자 피킹 리스트가 출력된다. | 인쇄 모달 출력 |
| Invoice Print | Channel에 따라 고객에게 전달될 인보이스가 출력된다. | 인쇄 모달 출력 |
| Label Print | **미결** | 인쇄 모달 출력 |

### 저장 정책

- 이전 상세 정보 중 수정된 내용이 있으면 저장(Save) 버튼이 활성화된다.

---

## 7. 개발 참고사항

### 화면 진입 시 동작

- 진입 즉시 권한에 맞는 전체 데이터 자동 조회

### 데이터 요구사항

| 항목 | 설명 | 비고 |
| ---- | ---- | ---- |
| 주문 조회 | 리스트를 통해 주문 내역을 조회할 수 있어야 한다. | |
| 상태 / 작업 유형 수정 | 각 주문의 상세에서 상태 및 작업 유형을 수정할 수 있어야 한다. | |
| 출고 / 송장 처리 | Outbound, Label 등의 연동 처리에 따라 출고 생성과 송장 출력이 처리되어야 한다. | |

### 연동 처리

- ERP (US: Netsuite) 연동
- TMS 연동
- 저장 시 Status History에 변경 이력 자동 기록
- Comment는 `POS - RX Operation View` 및 `RX - Prescription Review`와 공유

### 로컬라이즈 목록

| Localize Key | 필드 명 (한국어) | English | 日本語 | Description |
| ------------ | --------------- | ------- | ------ | ----------- |
| breadcrumb.rx_lms | RX (LMS) | RX (LMS) | RX (LMS) | 상단 브레드크럼 |
| breadcrumb.lens_work_management | 렌즈 작업 관리 | Lens Work Management | レンズ作業管理 | 메뉴 경로 |
| page.title_lens_work_management | 렌즈 작업 관리 | Lens Work Management | レンズ作業管理 | 페이지 타이틀 |
| filter.store | 스토어 | Store | 店舗 | 매장 필터 |
| filter.channel | 채널 | Channel | チャネル | 주문 채널 필터 |
| filter.work_type | 작업 유형 | Work Type | 作業タイプ | 작업 방식 필터 |
| filter.processing_period | 작업 기간 | Processing Period | 処理期間 | 작업 처리 기간 |
| filter.work_status | 작업 상태 | Work Status | 作業状態 | 작업 상태 필터 |
| filter.date_search | 날짜 검색 | Date Search | 日付検索 | 날짜 필터 |
| filter.order_date | 주문일 | Order Date | 注文日 | 주문 날짜 |
| filter.today | 오늘 | Today | 今日 | 날짜 단축 |
| filter.week | 1 주 | 1 Week | 1週間 | 날짜 단축 |
| filter.month | 1 개월 | 1 Month | 1ヶ月 | 날짜 단축 |
| filter.three_month | 3 개월 | 3 Months | 3ヶ月 | 날짜 단축 |
| filter.keyword_search | 스토어 코드, 스토어 명, Order No | Store Code / Store Name / Order No Search | 店舗コード・店舗名・注文番号検索 | 키워드 검색 |
| button.search | 검색 | Search | 検索 | 검색 버튼 |
| section.work_status | 작업 현황 | Work Status | 作業状況 | 작업 현황 카드 영역 |
| tab.to_customer | 고객 배송 | To Customer | 顧客配送 | 고객 배송 탭 |
| tab.to_store | 매장 배송 | To Store | 店舗配送 | 매장 배송 탭 |
| label.total | 총 건수 | Total | 合計 | 총 주문 수 |
| button.outbound_registration | 출고 등록 | Outbound Registration | 出庫登録 | 출고 등록 |
| button.download | 다운로드 | Download | ダウンロード | 다운로드 |
| column.order_date | 주문일 | Order Date | 注文日 | 주문 생성일 |
| column.approval_date | 승인일 | Approval Date | 承認日 | 주문 승인일 |
| column.channel | 채널 | Channel | チャネル | 주문 채널 |
| column.order_number | 주문번호 | Order No | 注文番号 | 주문 번호 |
| column.order_type | 주문 유형 | Order Type | 注文タイプ | 주문 타입 |
| column.store_info | 매장 정보 | Store Info | 店舗情報 | 매장 코드 / 이름 |
| column.work_status | 작업 상태 | Work Status | 作業状態 | 작업 진행 상태 |
| column.work_type | 작업 유형 | Work Type | 作業タイプ | 작업 방식 |
| column.processing_period | 처리 기간 | Processing Period | 処理期間 | 작업 기간 |
| column.worker | 작업자 | Worker | 作業者 | 담당 작업자 |
| column.detail | 상세 | Detail | 詳細 | 상세보기 |
| column.invoice_print | 인보이스 출력 | Invoice Print | 請求書印刷 | 인보이스 출력 |
| column.lead_time | 리드타임 | Lead Time | リードタイム | 작업 소요 시간 |
| column.completion_date | 완료일 | Completion Date | 完了日 | 작업 완료 날짜 |
| button.detail | 상세 | Detail | 詳細 | 상세 버튼 |
| button.print | 출력 | Print | 印刷 | 출력 버튼 |
| button.work_print | 작업지 출력 | Work Print | 作業票印刷 | 작업지 인쇄 |
| button.invoice | 인보이스 | Invoice | 請求書 | 인보이스 출력 |
| button.label_print | 라벨 출력 | Label Print | ラベル印刷 | 배송 라벨 출력 |
| button.back_to_list | 목록으로 | Back to List | 一覧へ戻る | 리스트 이동 |
| section.member_info | 회원 정보 | Member Info | 会員情報 | 회원 정보 영역 |
| field.country | 국가 | Country | 国 | 국가 |
| field.membership | 멤버십 | Membership | 会員 | 멤버십 정보 |
| button.prescription_view | 처방전 보기 | Prescription View | 処方箋表示 | 처방전 확인 |
| section.work_status_management | 작업 상태 관리 | Work Status Management | 作業状態管理 | 상태 관리 |
| field.work_status | 작업 상태 | Work Status | 作業状態 | 상태 필드 |
| field.work_type | 작업 유형 | Work Type | 作業タイプ | 작업 유형 |
| field.work_processing_period | 작업 기간 | Work Processing Period | 作業期間 | 작업 기간 |
| section.delivery_tracking | 배송 추적 | Delivery Tracking | 配送追跡 | 배송 정보 |
| field.shipment_label_no | 송장 번호 | Shipment Label No | 送り状番号 | 송장 번호 |
| field.carrier | 배송사 | Carrier | 配送会社 | 배송사 |
| section.status_history | 상태 이력 | Status History | 状態履歴 | 상태 변경 이력 |
| section.order_info | 주문 정보 | Order Info | 注文情報 | 주문 정보 |
| column.product_type | 상품 유형 | Product Type | 商品タイプ | 상품 유형 |
| column.product_info | 상품 정보 | Product Info | 商品情報 | 상품 정보 |
| column.mapped_product | 매핑 상품 | Mapped Product | マッピング商品 | 매핑 상품 |
| column.qty | 수량 | Qty | 数量 | 수량 |
| column.total_price | 총 금액 | Total Price | 合計金額 | 금액 |
| column.cof | C.O.F | C.O.F | C.O.F | 비용 여부 |
| tab.prescription | 처방 정보 | Prescription | 処方情報 | 처방 정보 탭 |
| tab.additional_details | 추가 정보 | Additional Details | 追加情報 | 추가 정보 |
| tab.comment | 코멘트 | Comment | コメント | 코멘트 |
| section.prescription_values | 처방 값 | Prescription Values | 処方値 | 처방 정보 |
| field.left_eye | 좌안 | Left Eye | 左目 | 왼쪽 눈 |
| field.right_eye | 우안 | Right Eye | 右目 | 오른쪽 눈 |
| field.sph | 구면도수 | SPH | 球面度数 | SPH 값 |
| field.cyl | 난시도수 | CYL | 円柱度数 | CYL 값 |
| field.axis | 축 | Axis | 軸 | Axis |
| field.pd | 동공거리 | PD | PD | 동공 거리 |
| field.oc | 광학 중심 | OC | OC | Optical Center |
| section.attached_files | 첨부 파일 | Attached Files | 添付ファイル | 파일 첨부 |
| section.customer_info | 고객 정보 | Customer Info | 顧客情報 | 고객 정보 |
| field.name | 이름 | Name | 名前 | 고객 이름 |
| field.phone | 전화번호 | Phone | 電話番号 | 전화 |
| field.email | 이메일 | Email | メール | 이메일 |
| field.address | 주소 | Address | 住所 | 주소 |
| field.city | 도시 | City | 市区町村 | 도시 |
| field.state | 주 | State | 都道府県 | 주 |
| field.zip | 우편번호 | ZIP | 郵便番号 | 우편번호 |
| option.ship_to_address | 고객 배송 | Ship to Address | 顧客配送 | 고객 배송 |
| option.ship_to_store | 매장 배송 | Ship to Store | 店舗配送 | 매장 배송 |
| section.consent_policy | 동의 및 정책 | Consent & Agreement Policy | 同意およびポリシー | 동의 영역 |
| field.signature | 서명 | Signature | 署名 | 서명 |
| button.clear | 초기화 | Clear | クリア | 초기화 |
| button.save | 저장 | Save | 保存 | 저장 |

---

## 8. 테스트 케이스

| # | 위치 | 구분 | 시나리오 | 기대 결과 | 확인 |
| -- | ---- | ---- | -------- | --------- | ---- |
| 1 | - | 권한 | 계정별 메뉴 권한 설정 상이 | 조회 / 등록, 수정 / 삭제에 따른 권한에 대해 메뉴 및 버튼 노출 점검 | ☐ |
| 2 | 리스트 | 공통 | 기본 진입 시 데이터 조회 | 권한에 맞는 전체 리스트 조회 | ☐ |
| 3 | 리스트 | 검색 | 필터 적용 후 검색 | 필터 조건에 맞는 데이터 조회 | ☐ |
| 4 | 리스트 | 다운로드 | 주문 선택 후 라벨 출력 | 선택한 주문에 대한 라벨 정상 출력 | ☐ |
| 5 | 리스트 | 다운로드 | 리스트 전체 추출 | 필터 조건에 맞는 전체 데이터 출력 | ☐ |
| 6 | 리스트 | 테이블 | 정렬 변경 컬럼 선택 | 각 컬럼 기준 정렬 내림/오름차순 변경 | ☐ |
| 7 | 상세 | 진입 | 주문별 상세 버튼 선택 | 주문별 상세 정상 진입 | ☐ |
| 8 | 상세 | 공통 | 멤버십 파일 조회 | 화면 플로팅 처리되며 이미지 조회 | ☐ |
| 9 | 상세 | 공통 | 주문 정보 조회 | 실 주문 정보와 동일한 데이터 반영 점검 | ☐ |
| 10 | 상세 | 데이터 수정 | 상태 변경 | 히스토리 내역 정상 생성 | ☐ |
| 11 | 상세 | 데이터 수정 | 작업 구분 / 기간 변경 | 정상 변경 / 저장 | ☐ |
| 12 | 상세 | 인쇄 | 피킹 리스트 출력 | 주문에 대한 피킹리스트 정상 출력 | ☐ |
| 13 | 상세 | 인쇄 | 인보이스 출력 | 주문에 대한 인보이스 정상 출력 (온/오프라인 구분) | ☐ |
| 14 | 상세 | 인쇄 | 배송 라벨(송장) 출력 | **미결** | ☐ |

---

## 9. 미정 사항

| # | 내용 | 담당자 | 기한 |
| - | ---- | ------ | ---- |
| 1 | 출고 등록 ~ 송장 출력 프로세스 | 이우주, 미법인 담당자 | 26.03.14 |

---

## 10. 변경 이력

| 버전 | 날짜 | 작성자 | 내용 |
| ---- | ---- | ------ | ---- |
| v1.0 | 2026-03-13 | PM 이우주 | 최초 작성 |
