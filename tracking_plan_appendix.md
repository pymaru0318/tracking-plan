## 📚 부록

### 부록 A: 이벤트 & 파라미터 설계 가이드

이 부록은 실무에서 트래킹 플랜을 설계할 때 필요한 기술적 상세 내용을 다룹니다.

---

#### 1. 파라미터의 종류

파라미터는 이벤트 발생 시 함께 수집하는 추가 정보입니다.

**기본 개념:**
```
📌 이벤트 = 동사 (무엇을 했는가)
📦 파라미터 = 명사 (어떤 상태에서, 어떤 대상으로)
```

##### 1️⃣ 컨텍스트 파라미터 (Context Parameters)

이벤트가 발생한 환경과 상황 정보

```javascript
{
  platform: "iOS",                    // 플랫폼 (iOS, Android, Web)
  app_version: "2.3.1",              // 앱 버전
  os_version: "iOS 17.2",            // OS 버전
  device_model: "iPhone 15 Pro",     // 기기 모델
  screen_name: "product_detail",     // 현재 화면명
  referrer: "search_result",         // 유입 경로
  session_id: "sess_abc123",         // 세션 ID
  network_type: "wifi"               // 네트워크 타입
}
```

**실무 활용:**
- 플랫폼별 사용자 행동 차이 분석
- 버전별 버그 추적
- 디바이스별 성능 최적화

##### 2️⃣ 비즈니스 파라미터 (Business Parameters)

비즈니스 분석에 필요한 핵심 정보

```javascript
{
  product_id: "PROD-12345",          // 상품 고유 ID
  product_name: "무선 이어폰",        // 상품명
  category_depth1: "전자제품",       // 대분류
  category_depth2: "오디오",         // 중분류
  category_depth3: "이어폰",         // 소분류
  price: 89000,                      // 가격 (숫자)
  currency: "KRW",                   // 통화
  discount_rate: 0.15,               // 할인율
  inventory_count: 42,               // 재고 수량
  seller_id: "seller_999",           // 판매자 ID
  brand: "BrandX"                    // 브랜드
}
```

**실무 활용:**
- 상품별 전환율 분석
- 카테고리별 매출 분석
- 재고 부족 알림

##### 3️⃣ 사용자 파라미터 (User Parameters)

사용자 속성 정보

```javascript
{
  user_id: "user_hashed_98765",      // 해시된 사용자 ID
  membership_tier: "premium",        // 회원 등급 (free, premium, vip)
  signup_date: "2024-03-15",        // 가입일
  total_orders: 12,                  // 총 주문 횟수
  lifetime_value: 1250000,          // 생애 가치 (LTV)
  is_first_purchase: false,         // 첫 구매 여부
  user_segment: "high_value"        // 사용자 세그먼트
}
```

**주의사항:**
- 개인정보(이메일, 전화번호, 이름)는 절대 수집 금지
- user_id는 반드시 해싱/암호화 처리
- 가변적인 값(나이, 가입 후 일수)보다 불변값(생년월, 가입일) 사용

##### 4️⃣ 행동 파라미터 (Behavior Parameters)

사용자 행동의 세부 정보

```javascript
{
  click_position: 3,                 // 리스트에서 몇 번째 항목 클릭
  scroll_depth_percent: 75,          // 스크롤 깊이 (%)
  time_on_page_seconds: 45,         // 페이지 체류 시간 (초)
  session_duration_seconds: 180,     // 세션 지속 시간 (초)
  previous_screen: "home",          // 이전 화면
  search_query: "무선 이어폰",       // 검색어
  filter_applied: ["price_asc", "brand_apple"],  // 적용한 필터
  video_watch_percent: 80           // 영상 시청률
}
```

**실무 활용:**
- UX 개선 (어디서 이탈하는가?)
- 콘텐츠 최적화 (몇 %까지 봤는가?)
- 검색 기능 개선

---

#### 2. 파라미터 명명 규칙

##### 규칙 1: snake_case 사용

```javascript
✅ 좋은 예:
product_id
user_name
total_price
is_premium_member

❌ 나쁜 예:
productId          // camelCase
ProductID          // PascalCase
product-id         // kebab-case
PRODUCT_ID         // SCREAMING_SNAKE_CASE
```

**이유:** 일관성, 가독성, 다양한 플랫폼 호환성

##### 규칙 2: 명확한 단위 표기

```javascript
✅ 좋은 예:
price: 89000               // 숫자만
currency: "KRW"            // 통화는 별도
duration_seconds: 180      // 단위를 이름에 포함
weight_kg: 2.5            // 단위를 이름에 포함
timestamp: "2025-01-15T10:30:00Z"  // ISO 8601 형식

❌ 나쁜 예:
price: "89,000원"         // 문자열 + 단위
time: "3분"               // 분석 불가능
date: "2025/1/15"         // 비표준 형식
```

##### 규칙 3: 일관된 데이터 타입

```javascript
✅ 좋은 예:
is_premium: true           // Boolean
quantity: 3                // Integer
price: 89000               // Integer (원화는 소수점 없음)
discount_rate: 0.15        // Float (15%)
product_id: "PROD-12345"   // String

❌ 나쁜 예:
is_premium: "Y"            // String (Boolean이어야 함)
quantity: "3개"            // String (Integer여야 함)
price: "89000"             // String (숫자여야 함)
discount_rate: "15%"       // String (Float이어야 함)
```

##### 규칙 4: 계층 구조 표현

```javascript
✅ 좋은 예 - 분리된 필드:
category_depth1: "패션"
category_depth2: "남성의류"
category_depth3: "아우터"

address_city: "서울"
address_district: "강남구"
address_detail: "테헤란로"

❌ 나쁜 예 - 결합된 값:
category: "패션 > 남성의류 > 아우터"    // 분석 어려움
address: "서울 강남구 테헤란로"         // 파싱 필요
```

**이유:**
- 필터링 용이 (예: "패션" 카테고리 전체 분석)
- 집계 용이 (예: 지역별 매출)
- 데이터 정합성

##### 규칙 5: 약어 사용 최소화

```javascript
✅ 좋은 예:
product_id
user_id
referrer_url
category

❌ 나쁜 예:
prod_id
usr_id
ref_url
cat
```

**예외:** 업계 표준 약어는 사용 가능
- `url` (Uniform Resource Locator)
- `id` (Identifier)
- `utm` (Urchin Tracking Module)

---

#### 3. 실전 예시: 이커머스 구매 플로우

##### 이벤트 1: 상품 조회

```javascript
{
  event_name: "product_view",
  timestamp: "2025-01-15T10:00:00Z",

  // 상품 정보
  product_id: "PROD-12345",
  product_name: "무선 이어폰 프리미엄",
  category_depth1: "전자제품",
  category_depth2: "오디오",
  category_depth3: "이어폰",
  price: 89000,
  currency: "KRW",
  discount_rate: 0.15,
  final_price: 75650,
  brand: "BrandX",

  // 사용자 정보
  user_id: "user_hashed_abc123",
  membership_tier: "premium",

  // 컨텍스트
  platform: "iOS",
  device_model: "iPhone 15",
  screen_name: "product_detail",
  referrer: "search_result",
  search_query: "무선 이어폰",

  // 행동
  view_source: "search",      // 어떻게 도달했는가
  position_in_list: 3        // 검색 결과에서 몇 번째
}
```

##### 이벤트 2: 장바구니 추가

```javascript
{
  event_name: "add_to_cart",
  timestamp: "2025-01-15T10:02:30Z",

  // 상품 정보
  product_id: "PROD-12345",
  quantity: 1,
  price: 89000,
  final_price: 75650,

  // 장바구니 상태
  cart_total_items: 3,        // 추가 후 장바구니 총 상품 수
  cart_total_value: 215000,   // 추가 후 장바구니 총 금액

  // 사용자
  user_id: "user_hashed_abc123",

  // 컨텍스트
  platform: "iOS",
  screen_name: "product_detail",

  // 행동
  time_on_page_seconds: 150   // 상품 페이지 체류 시간
}
```

##### 이벤트 3: 결제 시작

```javascript
{
  event_name: "checkout_start",
  timestamp: "2025-01-15T10:05:00Z",

  // 주문 정보
  cart_total_items: 3,
  cart_total_value: 215000,
  shipping_fee: 3000,
  discount_amount: 10000,
  final_amount: 208000,

  // 쿠폰
  coupon_applied: true,
  coupon_code: "WELCOME2025",
  coupon_discount: 10000,

  // 사용자
  user_id: "user_hashed_abc123",
  membership_tier: "premium",

  // 컨텍스트
  platform: "iOS"
}
```

##### 이벤트 4: 결제 완료

```javascript
{
  event_name: "purchase_complete",
  timestamp: "2025-01-15T10:07:45Z",

  // 주문 정보
  order_id: "ORD-2025-001234",
  revenue: 208000,             // 실제 결제 금액
  shipping_fee: 3000,
  tax: 0,

  // 결제 정보
  payment_method: "card",      // card, bank_transfer, mobile
  payment_provider: "kakaopay",
  installment_months: 0,       // 할부 개월 (0 = 일시불)

  // 배송 정보
  shipping_method: "express",  // standard, express, pickup
  estimated_delivery_date: "2025-01-17",

  // 상품 목록 (배열)
  products: [
    {
      product_id: "PROD-12345",
      quantity: 1,
      price: 75650
    },
    // ... 기타 상품
  ],

  // 사용자
  user_id: "user_hashed_abc123",
  is_first_purchase: false,

  // 컨텍스트
  platform: "iOS",

  // 퍼널 분석용
  time_from_view_to_purchase_seconds: 465  // 상품 조회부터 구매까지 시간
}
```

---

#### 4. 자주 하는 실수와 해결책

##### 실수 1: 개인정보 수집

```javascript
❌ 절대 금지:
{
  email: "user@example.com",
  phone: "010-1234-5678",
  name: "홍길동",
  address: "서울시 강남구...",
  birth_date: "1990-05-15",
  card_number: "1234-****-****-5678"
}

✅ 올바른 방법:
{
  user_id: "user_hashed_abc123",  // 해싱된 ID
  age_group: "30s",                // 연령대
  region: "seoul",                 // 지역 (구체적 주소 X)
  payment_method: "card"           // 카드번호 X, 결제수단만
}
```

**법적 근거:** 개인정보보호법, GDPR

##### 실수 2: 불필요한 중복

```javascript
❌ 나쁜 예:
{
  event_name: "product_click",
  product_id: "12345",
  product_name: "무선 이어폰",
  product_price: 89000,
  product_category: "전자제품",
  product_brand: "BrandX"
}

✅ 좋은 예:
{
  event_name: "product_click",
  id: "12345",
  name: "무선 이어폰",
  price: 89000,
  category: "전자제품",
  brand: "BrandX"
}
```

**이유:** `product_` 접두사는 이벤트명에서 이미 명확함

##### 실수 3: 가변적인 값 저장

```javascript
❌ 나쁜 예:
{
  user_age: 35,                    // 1년 후 잘못된 값
  days_since_signup: 120,          // 매일 변함
  hours_since_last_login: 5        // 시간마다 변함
}

✅ 좋은 예:
{
  birth_year: 1990,                // 불변
  signup_date: "2024-09-01",       // 불변
  last_login_timestamp: "2025-01-15T05:00:00Z"  // 절대 시간
}
```

**이유:** 분석 시점에 따라 결과가 달라지면 안 됨

##### 실수 4: 문자열에 숫자 섞기

```javascript
❌ 나쁜 예:
{
  price: "89,000원",
  quantity: "3개",
  discount: "15%",
  duration: "2시간 30분"
}

✅ 좋은 예:
{
  price: 89000,
  currency: "KRW",
  quantity: 3,
  discount_rate: 0.15,
  duration_minutes: 150
}
```

**이유:** 집계, 필터링, 정렬 불가능

##### 실수 5: 불명확한 Boolean 표현

```javascript
❌ 나쁜 예:
{
  is_premium: "Y",
  has_discount: "true",
  stock_available: 1,
  newsletter_subscribed: "yes"
}

✅ 좋은 예:
{
  is_premium: true,
  has_discount: true,
  stock_available: true,
  newsletter_subscribed: true
}
```

---

#### 5. 고급 패턴

##### 패턴 1: 배열 파라미터

상품 목록, 필터 목록 등

```javascript
{
  event_name: "purchase_complete",
  order_id: "ORD-001",

  // 상품 배열
  products: [
    {
      id: "PROD-123",
      name: "무선 이어폰",
      quantity: 1,
      price: 89000
    },
    {
      id: "PROD-456",
      name: "스마트워치",
      quantity: 2,
      price: 250000
    }
  ],

  // 필터 배열
  applied_filters: ["price_asc", "brand_apple", "rating_4plus"]
}
```

**주의:** 배열은 분석이 복잡하므로 꼭 필요한 경우에만 사용

##### 패턴 2: 조건부 파라미터

특정 조건에서만 수집

```javascript
// 검색 이벤트
{
  event_name: "search",
  query: "무선 이어폰",
  results_count: 42,

  // 검색 결과가 0일 때만
  is_zero_results: true,           // results_count가 0인 경우에만
  suggested_query: "블루투스 이어폰"  // 0 results일 때 제안한 검색어
}

// 결제 이벤트
{
  event_name: "purchase_complete",
  order_id: "ORD-001",

  // 쿠폰 사용 시에만
  coupon_applied: true,
  coupon_code: "WELCOME2025",      // coupon_applied가 true일 때만
  coupon_discount: 10000           // coupon_applied가 true일 때만
}
```

##### 패턴 3: 플랫폼별 파라미터

```javascript
// iOS에서만
{
  event_name: "app_open",
  platform: "iOS",
  idfa: "hashed_idfa_value",       // iOS만
  att_status: "authorized"         // iOS 14+ 추적 권한
}

// Android에서만
{
  event_name: "app_open",
  platform: "Android",
  gaid: "hashed_gaid_value",       // Android만
  play_install_referrer: "..."     // Android만
}

// Web에서만
{
  event_name: "page_view",
  platform: "Web",
  url: "https://example.com/products/123",
  referrer_url: "https://google.com",
  utm_source: "google",
  utm_medium: "cpc"
}
```

---

이것으로 부록 A를 마칩니다. 실무에서 파라미터를 설계할 때 이 가이드를 참고하세요.

---

### 부록 B: 실무 데이터 파이프라인 이해하기

이 부록은 트래킹 플랜으로 설계한 이벤트가 실제로 어떻게 수집되고, 저장되고, 분석되는지 전체 흐름을 다룹니다.

---

#### 1. 데이터 생성부터 분석까지 전체 여정

```
┌─────────────┐     ┌─────────────┐     ┌─────────────┐     ┌─────────────┐
│  1. 생성    │ --> │  2. 전송    │ --> │  3. 저장    │ --> │  4. 분석    │
│  (앱/웹)    │     │  (SDK)      │     │ (데이터베이스)│     │ (SQL/Python)│
└─────────────┘     └─────────────┘     └─────────────┘     └─────────────┘
```

##### 단계 1: 데이터 생성 (앱/웹)

사용자가 앱이나 웹사이트에서 행동을 하면 이벤트가 발생합니다.

```javascript
// 사용자가 "구매하기" 버튼을 클릭
button.onClick(() => {
  // 이벤트 전송
  analytics.track('purchase_button_click', {
    product_id: 'PROD-12345',
    price: 89000,
    platform: 'iOS'
  });
});
```

##### 단계 2: 데이터 전송 (SDK)

SDK(Software Development Kit)가 이벤트를 서버로 전송합니다.

**주요 SDK:**
- Google Analytics (GA4)
- Amplitude
- Mixpanel
- Segment (여러 분석 도구를 통합)
- Firebase Analytics
- 자체 개발 SDK

**전송 방식:**
- HTTP POST 요청
- 배치 처리 (여러 이벤트를 모아서 한 번에 전송)
- 실시간 전송 vs 지연 전송 (네트워크 상황에 따라)

##### 단계 3: 데이터 저장

수집된 이벤트는 다양한 형태로 저장됩니다.

**저장 위치:**
- 로그 파일 (서버 파일 시스템)
- 관계형 데이터베이스 (MySQL, PostgreSQL)
- NoSQL 데이터베이스 (MongoDB)
- 데이터 웨어하우스 (BigQuery, Redshift, Snowflake)
- 분석 플랫폼 (Amplitude, Mixpanel)

##### 단계 4: 데이터 분석

저장된 데이터를 SQL, Python 등으로 분석합니다.

```sql
-- BigQuery에서 일별 매출 집계
SELECT
  DATE(timestamp) as date,
  COUNT(*) as purchase_count,
  SUM(revenue) as total_revenue
FROM events
WHERE event_name = 'purchase_complete'
GROUP BY date
ORDER BY date DESC;
```

---

#### 2. 실무에서 만나는 데이터 형식

##### 형식 1: CSV (Comma-Separated Values)

**언제 사용하나요?**
- 소규모 데이터 분석
- BI 도구에서 다운로드
- Excel로 간단히 확인할 때
- 데이터 공유 (이메일, 슬랙)

**실제 파일 예시:**

`events.csv`
```csv
event_name,user_id,product_id,price,timestamp
product_view,user_001,PROD-123,89000,2025-01-15T10:00:00Z
add_to_cart,user_001,PROD-123,89000,2025-01-15T10:02:30Z
purchase_complete,user_001,PROD-123,89000,2025-01-15T10:05:45Z
product_view,user_002,PROD-456,150000,2025-01-15T10:01:00Z
```

**장점:**
- 사람이 읽기 쉬움
- Excel, Google Sheets에서 바로 열림
- 모든 도구에서 지원

**단점:**
- 큰 파일 크기 (압축 안 됨)
- 복잡한 구조 표현 어려움 (중첩 객체, 배열)
- 데이터 타입 정보 없음 (모든 것이 문자열)
- 대용량 처리 느림

**실무 사용 예:**
```
사용 사례: Amplitude에서 "지난 7일 구매 데이터" CSV 다운로드
↓
Excel에서 열어서 간단한 피벗 테이블 생성
↓
주간 리포트에 첨부
```

##### 형식 2: JSON Lines (.jsonl)

**언제 사용하나요?**
- 로그 파일 저장
- 스트리밍 데이터
- 서버 → 서버 데이터 전송
- 복잡한 데이터 구조 (중첩 객체, 배열)

**실제 파일 예시:**

`events.jsonl`
```json
{"event_name":"product_view","user_id":"user_001","product_id":"PROD-123","price":89000,"timestamp":"2025-01-15T10:00:00Z","metadata":{"platform":"iOS","device":"iPhone 15"}}
{"event_name":"add_to_cart","user_id":"user_001","product_id":"PROD-123","quantity":1,"price":89000,"timestamp":"2025-01-15T10:02:30Z"}
{"event_name":"purchase_complete","user_id":"user_001","order_id":"ORD-001","revenue":89000,"products":[{"id":"PROD-123","quantity":1,"price":89000}],"timestamp":"2025-01-15T10:05:45Z"}
```

**특징:**
- 한 줄에 하나의 이벤트 (JSON 객체)
- 스트리밍 처리 가능 (줄 단위로 읽기)
- 복잡한 구조 표현 가능

**장점:**
- 복잡한 데이터 구조 지원 (배열, 중첩 객체)
- 데이터 타입 보존 (숫자, 불린, null)
- 스트리밍 가능 (메모리 절약)
- 사람이 읽기 쉬움 (디버깅 용이)

**단점:**
- CSV보다 파일 크기 큼
- 처리 속도 느림 (파싱 필요)

**실무 사용 예:**
```
사용 사례: 서버 로그 파일
/var/log/events/2025-01-15.jsonl  (하루치 이벤트)
↓
Kafka로 실시간 스트리밍
↓
BigQuery로 적재
```

##### 형식 3: Parquet

**언제 사용하나요?**
- 대용량 데이터 저장
- 클라우드 저장소 (S3, GCS)
- 데이터 웨어하우스 (BigQuery, Redshift)
- 빠른 집계 분석

**특징:**
- 컬럼 기반 저장 (Column-oriented)
- 압축률 높음 (CSV 대비 10~100배 작음)
- 데이터 타입 보존
- 사람이 직접 읽을 수 없음 (바이너리 형식)

**장점:**
- 매우 작은 파일 크기 (압축)
- 빠른 집계 (컬럼 단위 읽기)
- 데이터 타입, 스키마 포함
- 클라우드 최적화

**단점:**
- 바이너리 형식 (사람이 읽을 수 없음)
- 특수 도구 필요 (Pandas, Spark, BigQuery)
- 행 추가 어려움 (읽기 전용에 가까움)

**실무 사용 예:**
```
사용 사례: S3에 월별 이벤트 저장
s3://bucket/events/year=2025/month=01/events.parquet
↓
Athena 또는 BigQuery로 쿼리
↓
Tableau로 시각화
```

**파일 크기 비교 (1백만 건 이벤트):**
```
CSV:       1.2 GB
JSON:      1.8 GB
JSONL:     1.5 GB
Parquet:   120 MB  (10배 작음)
```

##### 형식 4: Google Analytics 4 (GA4) BigQuery Export

**언제 사용하나요?**
- GA4를 사용하는 웹/앱
- BigQuery로 원본 데이터 분석
- 커스텀 리포트 필요

**실제 데이터 구조:**

BigQuery 테이블: `analytics_123456789.events_20250115`

```sql
SELECT
  event_date,              -- 20250115 (YYYYMMDD)
  event_timestamp,         -- 1736928000000000 (마이크로초)
  event_name,              -- 'purchase'
  user_pseudo_id,          -- 'abc123...'

  -- 이벤트 파라미터 (배열)
  (SELECT value.string_value
   FROM UNNEST(event_params)
   WHERE key = 'product_id') as product_id,

  (SELECT value.int_value
   FROM UNNEST(event_params)
   WHERE key = 'price') as price

FROM `analytics_123456789.events_*`
WHERE _TABLE_SUFFIX = '20250115'
LIMIT 10;
```

**특징:**
- 날짜별 파티션 테이블 (`events_20250115`, `events_20250116`, ...)
- 중첩 구조 (event_params는 배열)
- UNNEST 필요 (파라미터 추출)

**장점:**
- GA4의 모든 원본 데이터 접근
- BigQuery의 강력한 분석 기능
- 다른 데이터와 JOIN 가능

**단점:**
- 복잡한 쿼리 구조 (UNNEST)
- 학습 곡선 높음
- BigQuery 비용 발생

##### 형식 5: Amplitude Export

**언제 사용하나요?**
- Amplitude 사용 중
- 원본 데이터 추출 필요
- 커스텀 분석, 머신러닝

**실제 데이터 형식:**

Export API 응답 (JSON):
```json
{
  "data": [
    {
      "event_type": "purchase_complete",
      "user_id": "user_abc123",
      "event_time": "2025-01-15 10:05:45.123",
      "event_properties": {
        "product_id": "PROD-123",
        "price": 89000,
        "currency": "KRW"
      },
      "user_properties": {
        "membership_tier": "premium",
        "total_orders": 12
      },
      "platform": "iOS",
      "device_model": "iPhone 15"
    }
  ]
}
```

**특징:**
- REST API로 데이터 추출
- JSON 형식
- 페이지네이션 (대용량 데이터)

---

#### 3. 데이터 형식 비교표

| 형식 | 사용 시기 | 장점 | 단점 | 파일 크기 |
|------|----------|------|------|-----------|
| **CSV** | 소규모, 공유, Excel 분석 | 범용성, 가독성 | 큰 크기, 타입 없음 | ⭐⭐⭐⭐⭐ |
| **JSONL** | 로그, 스트리밍, 복잡한 구조 | 구조 표현, 타입 보존 | 큰 크기, 느린 처리 | ⭐⭐⭐⭐ |
| **Parquet** | 대용량, 클라우드, 빠른 집계 | 작은 크기, 빠른 속도 | 바이너리, 도구 필요 | ⭐ |
| **GA4** | GA4 사용자, BigQuery 분석 | 원본 데이터, 강력한 분석 | 복잡한 쿼리, 비용 | N/A |
| **Amplitude** | Amplitude 사용자, API 추출 | 구조화된 데이터 | API 제한, 페이지네이션 | N/A |

---

#### 4. 실무 시나리오별 데이터 흐름

##### 시나리오 1: 스타트업 (월 10만 이벤트)

```
앱/웹
  ↓
Google Analytics 4 (무료)
  ↓
BigQuery Export (무료 티어)
  ↓
Looker Studio (무료)
```

**데이터 형식:**
- GA4 내부: GA4 전용 형식
- BigQuery: Parquet (자동)
- 분석: SQL

**비용:** 거의 무료 (BigQuery 무료 티어 1TB/월)

##### 시나리오 2: 중소기업 (월 1000만 이벤트)

```
앱/웹
  ↓
Segment (통합 SDK)
  ↓
├─ Amplitude (행동 분석)
├─ BigQuery (원본 데이터)
└─ S3 (백업)
  ↓
Tableau / Looker
```

**데이터 형식:**
- Segment → Amplitude: JSON (API)
- Segment → BigQuery: Parquet
- BigQuery → S3: Parquet
- 백업: JSONL (S3)

**비용:** 월 수백만 원 (Segment, Amplitude, BigQuery)

##### 시나리오 3: 대기업 (월 10억 이벤트)

```
앱/웹
  ↓
자체 SDK
  ↓
Kafka (실시간 스트리밍)
  ↓
├─ BigQuery (실시간 분석)
├─ Redshift (배치 분석)
└─ S3 (원본 저장)
  ↓
Looker / Tableau / 자체 대시보드
```

**데이터 형식:**
- Kafka: JSONL (스트리밍)
- S3: Parquet (파티션)
- BigQuery: Parquet (자동)
- 백업: Parquet (압축)

**비용:** 월 수천만 원 (인프라, 엔지니어링)

---

#### 5. 데이터 확인 방법

##### 방법 1: 로그 파일 직접 확인 (개발자)

```bash
# 서버에 SSH 접속
ssh user@server

# 오늘 로그 확인
tail -f /var/log/events/2025-01-15.jsonl

# 특정 이벤트 검색
grep "purchase_complete" /var/log/events/2025-01-15.jsonl | head -5

# 이벤트 개수 세기
wc -l /var/log/events/2025-01-15.jsonl
```

##### 방법 2: 데이터베이스 쿼리 (데이터 분석가)

```sql
-- MySQL
SELECT
  event_name,
  COUNT(*) as count
FROM events
WHERE DATE(timestamp) = '2025-01-15'
GROUP BY event_name;

-- BigQuery
SELECT
  event_name,
  COUNT(*) as count
FROM `project.dataset.events_*`
WHERE _TABLE_SUFFIX = '20250115'
GROUP BY event_name;
```

##### 방법 3: 분석 플랫폼 대시보드 (비개발자)

```
Amplitude 접속
  ↓
"Events" 메뉴
  ↓
날짜 선택: 2025-01-15
  ↓
이벤트별 집계 확인
  ↓
필요 시 CSV 다운로드
```

##### 방법 4: Python으로 파일 읽기

```python
import pandas as pd
import json

# CSV 읽기
df_csv = pd.read_csv('events.csv')
print(df_csv.head())

# JSONL 읽기
df_jsonl = pd.read_json('events.jsonl', lines=True)
print(df_jsonl.head())

# Parquet 읽기
df_parquet = pd.read_parquet('events.parquet')
print(df_parquet.head())

# 기본 분석
print(df_parquet['event_name'].value_counts())
```

---

#### 6. 실무 팁

##### 팁 1: 데이터 형식 선택 가이드

```
선택 기준:

데이터 크기가 작다 (< 100MB)
  → CSV (Excel로 열기 쉬움)

복잡한 구조 (배열, 중첩 객체)
  → JSONL

대용량 (> 1GB)
  → Parquet

실시간 스트리밍
  → JSONL (Kafka)

장기 보관
  → Parquet (압축률 높음)

공유/협업
  → CSV (범용성)
```

##### 팁 2: 파티셔닝

대용량 데이터는 날짜별로 분할 저장:

```
# 좋은 예
s3://bucket/events/year=2025/month=01/day=15/data.parquet
s3://bucket/events/year=2025/month=01/day=16/data.parquet

# 나쁜 예
s3://bucket/events/all_data.parquet  (수 TB 크기)
```

**이유:**
- 특정 날짜만 읽기 (비용 절감)
- 병렬 처리 가능
- 관리 용이

##### 팁 3: 압축 사용

```
# JSONL 압축
gzip events.jsonl
→ events.jsonl.gz (70% 크기 감소)

# Parquet은 자동 압축
→ 별도 압축 불필요
```

##### 팁 4: 스키마 정의

데이터 형식에 스키마(구조) 정의 저장:

```json
// events_schema.json
{
  "event_name": "string",
  "user_id": "string",
  "product_id": "string",
  "price": "integer",
  "timestamp": "timestamp"
}
```

**이유:**
- 데이터 타입 검증
- 문서 역할
- 자동 테이블 생성

---

이것으로 부록 B를 마칩니다. 이제 실무에서 어떤 형식의 데이터를 만나도 당황하지 않을 것입니다.

---

## 부록 C-1: Python (Pandas) 실습 가이드

> **목표:** 실제 데이터를 Python으로 읽고, 정제하고, 분석하는 전체 과정을 실습합니다.

### 1. 환경 준비

#### 필수 라이브러리 설치

```bash
# 기본 분석 도구
pip install pandas numpy

# 데이터 읽기/쓰기
pip install pyarrow fastparquet

# 시각화
pip install matplotlib seaborn plotly

# Jupyter Notebook (선택)
pip install jupyter
```

#### 작업 디렉토리 구조

```
project/
├── data/
│   ├── events.csv
│   ├── events.jsonl
│   └── events.parquet
├── notebooks/
│   └── analysis.ipynb
└── output/
    └── results.csv
```

---

### 2. 데이터 읽기 (3가지 형식)

#### 2-1. CSV 읽기

```python
import pandas as pd

# 기본 읽기
df_csv = pd.read_csv('data/events.csv')

# 옵션 적용
df_csv = pd.read_csv(
    'data/events.csv',
    parse_dates=['timestamp'],  # 날짜 자동 파싱
    dtype={
        'user_id': 'string',
        'product_id': 'string',
        'price': 'int64'
    }
)

# 데이터 확인
print(df_csv.head())
print(df_csv.info())
```

**출력 예시:**
```
   event_name  user_id product_id   price           timestamp
0 product_view user_001  PROD-123   89000 2025-01-15 10:00:00
1  add_to_cart user_001  PROD-123   89000 2025-01-15 10:02:30
2     purchase user_001  PROD-123   89000 2025-01-15 10:05:45
```

#### 2-2. JSONL 읽기

```python
# 기본 읽기
df_jsonl = pd.read_json('data/events.jsonl', lines=True)

# 중첩된 JSON 구조 펼치기
df_jsonl = pd.read_json('data/events.jsonl', lines=True)

# metadata 컬럼이 딕셔너리인 경우
if 'metadata' in df_jsonl.columns:
    # 딕셔너리를 별도 컬럼으로 확장
    metadata_df = pd.json_normalize(df_jsonl['metadata'])
    df_jsonl = pd.concat([df_jsonl.drop('metadata', axis=1), metadata_df], axis=1)

print(df_jsonl.head())
```

**출력 예시:**
```
   event_name  user_id product_id   price platform    device
0 product_view user_001  PROD-123   89000      iOS iPhone 15
1  add_to_cart user_001  PROD-123   89000      iOS iPhone 15
```

#### 2-3. Parquet 읽기 (가장 빠름)

```python
# 기본 읽기
df_parquet = pd.read_parquet('data/events.parquet')

# 특정 컬럼만 읽기 (메모리 절약)
df_parquet = pd.read_parquet(
    'data/events.parquet',
    columns=['event_name', 'user_id', 'timestamp', 'price']
)

# 필터링하며 읽기
df_parquet = pd.read_parquet(
    'data/events.parquet',
    filters=[('event_name', '==', 'purchase_complete')]
)

print(df_parquet.head())
```

---

### 3. 데이터 전처리 (Preprocessing)

#### 3-1. 기본 정보 확인

```python
# 데이터 형태 확인
print(f"행 개수: {len(df)}")
print(f"컬럼 개수: {len(df.columns)}")
print(f"메모리 사용량: {df.memory_usage(deep=True).sum() / 1024**2:.2f} MB")

# 결측치 확인
print(df.isnull().sum())

# 중복 확인
print(f"중복 행: {df.duplicated().sum()}")

# 데이터 타입 확인
print(df.dtypes)
```

#### 3-2. 날짜/시간 처리

```python
# timestamp를 datetime으로 변환
df['timestamp'] = pd.to_datetime(df['timestamp'])

# 날짜 관련 컬럼 추가
df['date'] = df['timestamp'].dt.date
df['hour'] = df['timestamp'].dt.hour
df['day_of_week'] = df['timestamp'].dt.day_name()

# 시간 차이 계산
df = df.sort_values(['user_id', 'timestamp'])
df['time_diff_seconds'] = df.groupby('user_id')['timestamp'].diff().dt.total_seconds()

print(df[['user_id', 'event_name', 'timestamp', 'time_diff_seconds']].head(10))
```

#### 3-3. 결측치 처리

```python
# 결측치 확인
missing = df.isnull().sum()
print(missing[missing > 0])

# 결측치 처리 전략
# 1) 삭제
df_clean = df.dropna(subset=['user_id', 'event_name'])

# 2) 기본값 채우기
df['product_id'] = df['product_id'].fillna('UNKNOWN')
df['price'] = df['price'].fillna(0)

# 3) 앞/뒤 값으로 채우기
df['session_id'] = df.groupby('user_id')['session_id'].ffill()
```

#### 3-4. 중복 제거

```python
# 완전 중복 제거
df = df.drop_duplicates()

# 특정 컬럼 기준 중복 제거 (첫 번째만 유지)
df = df.drop_duplicates(subset=['user_id', 'event_name', 'timestamp'], keep='first')

print(f"중복 제거 후: {len(df)} 행")
```

---

### 4. 기본 분석 (Basic Analysis)

#### 4-1. 이벤트 집계

```python
# 이벤트별 발생 횟수
event_counts = df['event_name'].value_counts()
print(event_counts)

# 비율 계산
event_ratio = df['event_name'].value_counts(normalize=True) * 100
print(event_ratio.round(2))

# 날짜별 이벤트 수
daily_events = df.groupby('date')['event_name'].count()
print(daily_events)
```

**출력 예시:**
```
product_view         4250
add_to_cart          1820
checkout_start        950
purchase_complete     385
```

#### 4-2. 사용자 분석

```python
# 활성 사용자 수
unique_users = df['user_id'].nunique()
print(f"전체 사용자: {unique_users}명")

# 사용자당 평균 이벤트 수
events_per_user = df.groupby('user_id').size()
print(f"사용자당 평균 이벤트: {events_per_user.mean():.2f}회")

# 가장 활발한 사용자 TOP 10
top_users = events_per_user.sort_values(ascending=False).head(10)
print(top_users)

# 신규 vs 재방문 사용자
purchase_users = df[df['event_name'] == 'purchase_complete']
first_purchase = purchase_users.groupby('user_id')['timestamp'].min()
purchase_users = purchase_users.merge(
    first_purchase.rename('first_purchase_time'),
    left_on='user_id',
    right_index=True
)
purchase_users['is_repeat'] = purchase_users['timestamp'] > purchase_users['first_purchase_time']
repeat_rate = purchase_users['is_repeat'].mean() * 100
print(f"재구매율: {repeat_rate:.2f}%")
```

#### 4-3. 매출 분석

```python
# 구매 이벤트만 필터링
purchases = df[df['event_name'] == 'purchase_complete'].copy()

# 총 매출
total_revenue = purchases['price'].sum()
print(f"총 매출: {total_revenue:,}원")

# 평균 객단가 (Average Order Value)
avg_order_value = purchases['price'].mean()
print(f"평균 객단가: {avg_order_value:,.0f}원")

# 일별 매출
daily_revenue = purchases.groupby('date')['price'].sum()
print(daily_revenue)

# 매출 분포
print(purchases['price'].describe())

# 상위 10% 고객의 매출 기여도
user_revenue = purchases.groupby('user_id')['price'].sum().sort_values(ascending=False)
top_10pct_count = int(len(user_revenue) * 0.1)
top_10pct_revenue = user_revenue.head(top_10pct_count).sum()
contribution = (top_10pct_revenue / total_revenue) * 100
print(f"상위 10% 고객 매출 기여도: {contribution:.1f}%")
```

---

### 5. 퍼널 분석 (Funnel Analysis)

#### 5-1. 기본 퍼널

```python
# 퍼널 단계 정의
funnel_steps = ['product_view', 'add_to_cart', 'checkout_start', 'purchase_complete']

# 각 단계별 사용자 수
funnel_data = []
for step in funnel_steps:
    user_count = df[df['event_name'] == step]['user_id'].nunique()
    funnel_data.append({'step': step, 'users': user_count})

funnel_df = pd.DataFrame(funnel_data)

# 전환율 계산
funnel_df['conversion_rate'] = (funnel_df['users'] / funnel_df['users'].iloc[0] * 100).round(2)
funnel_df['step_conversion'] = (funnel_df['users'] / funnel_df['users'].shift(1) * 100).round(2)

print(funnel_df)
```

**출력 예시:**
```
               step  users  conversion_rate  step_conversion
0     product_view    500            100.00              NaN
1     add_to_cart    215             43.00            43.00
2   checkout_start    108             21.60            50.23
3 purchase_complete     48              9.60            44.44
```

#### 5-2. 사용자별 퍼널 달성도

```python
# 각 사용자가 어느 단계까지 도달했는지
user_funnel = df[df['event_name'].isin(funnel_steps)].copy()
user_funnel['step_order'] = user_funnel['event_name'].map({
    'product_view': 1,
    'add_to_cart': 2,
    'checkout_start': 3,
    'purchase_complete': 4
})

# 사용자별 최대 단계
user_max_step = user_funnel.groupby('user_id')['step_order'].max()

# 단계별 이탈 분석
dropout_analysis = user_max_step.value_counts().sort_index()
dropout_df = pd.DataFrame({
    'max_step': dropout_analysis.index,
    'user_count': dropout_analysis.values,
    'step_name': [funnel_steps[i-1] for i in dropout_analysis.index]
})
dropout_df['dropout_rate'] = (dropout_df['user_count'] / dropout_df['user_count'].sum() * 100).round(2)

print(dropout_df)
```

#### 5-3. 시간대별 퍼널 비교

```python
# 시간대 추가
df['hour'] = df['timestamp'].dt.hour

# 오전(6-12시) vs 오후(12-18시) vs 저녁(18-24시)
def time_period(hour):
    if 6 <= hour < 12:
        return '오전'
    elif 12 <= hour < 18:
        return '오후'
    elif 18 <= hour < 24:
        return '저녁'
    else:
        return '새벽'

df['time_period'] = df['hour'].apply(time_period)

# 시간대별 전환율
time_funnel = []
for period in ['오전', '오후', '저녁', '새벽']:
    period_df = df[df['time_period'] == period]
    views = period_df[period_df['event_name'] == 'product_view']['user_id'].nunique()
    purchases = period_df[period_df['event_name'] == 'purchase_complete']['user_id'].nunique()
    conversion = (purchases / views * 100) if views > 0 else 0
    time_funnel.append({
        'time_period': period,
        'views': views,
        'purchases': purchases,
        'conversion_rate': round(conversion, 2)
    })

time_funnel_df = pd.DataFrame(time_funnel)
print(time_funnel_df)
```

---

### 6. 코호트 분석 (Cohort Analysis)

#### 6-1. 가입 월별 코호트

```python
# 각 사용자의 첫 이벤트 날짜 (가입일로 간주)
user_first_event = df.groupby('user_id')['timestamp'].min().reset_index()
user_first_event.columns = ['user_id', 'cohort_date']
user_first_event['cohort_month'] = user_first_event['cohort_date'].dt.to_period('M')

# 원본 데이터에 코호트 정보 추가
df = df.merge(user_first_event[['user_id', 'cohort_month']], on='user_id')
df['event_month'] = df['timestamp'].dt.to_period('M')

# 코호트별 월별 활성 사용자 수
cohort_data = df.groupby(['cohort_month', 'event_month'])['user_id'].nunique().reset_index()
cohort_data.columns = ['cohort_month', 'event_month', 'active_users']

# 피벗 테이블로 변환
cohort_pivot = cohort_data.pivot_table(
    index='cohort_month',
    columns='event_month',
    values='active_users'
)

print(cohort_pivot)
```

#### 6-2. 리텐션 (Retention) 계산

```python
# 코호트 크기 (첫 달 사용자 수)
cohort_sizes = cohort_pivot.iloc[:, 0]

# 리텐션율 계산
retention = cohort_pivot.divide(cohort_sizes, axis=0) * 100
retention = retention.round(2)

print("리텐션율 (%):")
print(retention)
```

**출력 예시:**
```
리텐션율 (%):
event_month    2025-01  2025-02  2025-03
cohort_month
2025-01         100.00    45.20    32.10
2025-02           NaN   100.00    48.50
2025-03           NaN      NaN   100.00
```

---

### 7. 세그먼트 분석 (Segmentation)

#### 7-1. RFM 분석 (Recency, Frequency, Monetary)

```python
# 구매 이벤트만 추출
purchases = df[df['event_name'] == 'purchase_complete'].copy()

# 분석 기준일 (가장 최근 날짜)
analysis_date = purchases['timestamp'].max()

# 사용자별 RFM 계산
rfm = purchases.groupby('user_id').agg({
    'timestamp': lambda x: (analysis_date - x.max()).days,  # Recency
    'event_name': 'count',  # Frequency
    'price': 'sum'  # Monetary
}).reset_index()

rfm.columns = ['user_id', 'recency', 'frequency', 'monetary']

# RFM 점수 계산 (1-5점)
rfm['r_score'] = pd.qcut(rfm['recency'], 5, labels=[5, 4, 3, 2, 1])
rfm['f_score'] = pd.qcut(rfm['frequency'].rank(method='first'), 5, labels=[1, 2, 3, 4, 5])
rfm['m_score'] = pd.qcut(rfm['monetary'], 5, labels=[1, 2, 3, 4, 5])

# RFM 총점
rfm['rfm_score'] = rfm['r_score'].astype(int) + rfm['f_score'].astype(int) + rfm['m_score'].astype(int)

# 세그먼트 분류
def rfm_segment(row):
    if row['rfm_score'] >= 12:
        return 'Champions'
    elif row['rfm_score'] >= 9:
        return 'Loyal Customers'
    elif row['rfm_score'] >= 6:
        return 'Potential'
    else:
        return 'At Risk'

rfm['segment'] = rfm.apply(rfm_segment, axis=1)

# 세그먼트별 통계
segment_stats = rfm.groupby('segment').agg({
    'user_id': 'count',
    'recency': 'mean',
    'frequency': 'mean',
    'monetary': 'mean'
}).round(2)

print(segment_stats)
```

**출력 예시:**
```
                  user_id  recency  frequency    monetary
segment
Champions              45     5.20       8.40  1250000.00
Loyal Customers        82    12.30       4.20   680000.00
Potential             120    25.40       2.10   320000.00
At Risk                53    48.20       1.30   150000.00
```

---

### 8. 시각화 (Visualization)

#### 8-1. 이벤트 분포 (막대 그래프)

```python
import matplotlib.pyplot as plt
import seaborn as sns

# 한글 폰트 설정 (Mac)
plt.rcParams['font.family'] = 'AppleGothic'
plt.rcParams['axes.unicode_minus'] = False

# 이벤트별 발생 횟수
event_counts = df['event_name'].value_counts()

plt.figure(figsize=(10, 6))
event_counts.plot(kind='bar', color='skyblue')
plt.title('이벤트별 발생 횟수', fontsize=16)
plt.xlabel('이벤트', fontsize=12)
plt.ylabel('횟수', fontsize=12)
plt.xticks(rotation=45)
plt.tight_layout()
plt.savefig('output/event_distribution.png', dpi=300)
plt.show()
```

#### 8-2. 일별 트렌드 (선 그래프)

```python
# 일별 이벤트 수
daily_events = df.groupby(['date', 'event_name']).size().reset_index(name='count')

plt.figure(figsize=(12, 6))
for event in funnel_steps:
    event_data = daily_events[daily_events['event_name'] == event]
    plt.plot(event_data['date'], event_data['count'], marker='o', label=event)

plt.title('일별 이벤트 발생 추이', fontsize=16)
plt.xlabel('날짜', fontsize=12)
plt.ylabel('이벤트 수', fontsize=12)
plt.legend()
plt.grid(True, alpha=0.3)
plt.xticks(rotation=45)
plt.tight_layout()
plt.savefig('output/daily_trend.png', dpi=300)
plt.show()
```

#### 8-3. 퍼널 시각화

```python
# 퍼널 데이터 (이전에 생성한 funnel_df 사용)
plt.figure(figsize=(10, 8))

# 퍼널 차트
steps = funnel_df['step'].tolist()
users = funnel_df['users'].tolist()
colors = ['#1f77b4', '#ff7f0e', '#2ca02c', '#d62728']

for i, (step, user_count, color) in enumerate(zip(steps, users, colors)):
    width = user_count / users[0]  # 첫 단계 대비 비율
    plt.barh(i, width, height=0.8, color=color, alpha=0.7)

    # 라벨 추가
    conversion = funnel_df.loc[i, 'conversion_rate']
    plt.text(width/2, i, f'{step}\n{user_count}명 ({conversion}%)',
             ha='center', va='center', fontsize=11, fontweight='bold')

plt.yticks([])
plt.xlim(0, 1)
plt.title('구매 퍼널 전환율', fontsize=16, fontweight='bold')
plt.xlabel('전환율 (%)', fontsize=12)
plt.tight_layout()
plt.savefig('output/funnel_chart.png', dpi=300)
plt.show()
```

#### 8-4. RFM 히트맵

```python
# RFM 세그먼트별 사용자 수
segment_counts = rfm['segment'].value_counts()

plt.figure(figsize=(8, 6))
plt.pie(segment_counts, labels=segment_counts.index, autopct='%1.1f%%',
        startangle=90, colors=sns.color_palette('pastel'))
plt.title('RFM 세그먼트 분포', fontsize=16)
plt.tight_layout()
plt.savefig('output/rfm_segments.png', dpi=300)
plt.show()
```

---

### 9. 데이터 저장 (Export)

#### 9-1. CSV로 저장

```python
# 분석 결과 저장
funnel_df.to_csv('output/funnel_analysis.csv', index=False, encoding='utf-8-sig')
rfm.to_csv('output/rfm_analysis.csv', index=False, encoding='utf-8-sig')

print("분석 결과가 저장되었습니다.")
```

#### 9-2. Excel로 저장 (여러 시트)

```python
# Excel 파일로 저장
with pd.ExcelWriter('output/analysis_report.xlsx', engine='openpyxl') as writer:
    funnel_df.to_excel(writer, sheet_name='퍼널분석', index=False)
    rfm.to_excel(writer, sheet_name='RFM분석', index=False)
    segment_stats.to_excel(writer, sheet_name='세그먼트통계')
    daily_events.to_excel(writer, sheet_name='일별트렌드', index=False)

print("Excel 보고서가 생성되었습니다: output/analysis_report.xlsx")
```

#### 9-3. Parquet로 저장 (빠른 재사용)

```python
# 전처리된 데이터 저장
df.to_parquet('output/processed_events.parquet', compression='snappy', index=False)

# 나중에 빠르게 불러오기
df_reload = pd.read_parquet('output/processed_events.parquet')
print(f"데이터 로드 완료: {len(df_reload)} 행")
```

---

### 10. 전체 워크플로우 (Complete Workflow)

아래는 전체 분석 과정을 하나의 스크립트로 정리한 것입니다:

```python
#!/usr/bin/env python3
# -*- coding: utf-8 -*-
"""
트래킹 데이터 분석 전체 워크플로우
"""

import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns
from datetime import datetime

# 한글 폰트 설정
plt.rcParams['font.family'] = 'AppleGothic'
plt.rcParams['axes.unicode_minus'] = False

def load_data(file_path, file_type='parquet'):
    """데이터 로드"""
    if file_type == 'csv':
        df = pd.read_csv(file_path, parse_dates=['timestamp'])
    elif file_type == 'jsonl':
        df = pd.read_json(file_path, lines=True)
    elif file_type == 'parquet':
        df = pd.read_parquet(file_path)

    print(f"데이터 로드 완료: {len(df)} 행, {len(df.columns)} 컬럼")
    return df

def preprocess_data(df):
    """데이터 전처리"""
    # 날짜 변환
    df['timestamp'] = pd.to_datetime(df['timestamp'])
    df['date'] = df['timestamp'].dt.date
    df['hour'] = df['timestamp'].dt.hour

    # 결측치 처리
    df = df.dropna(subset=['user_id', 'event_name'])

    # 중복 제거
    df = df.drop_duplicates()

    print(f"전처리 완료: {len(df)} 행")
    return df

def analyze_funnel(df, funnel_steps):
    """퍼널 분석"""
    funnel_data = []
    for step in funnel_steps:
        user_count = df[df['event_name'] == step]['user_id'].nunique()
        funnel_data.append({'step': step, 'users': user_count})

    funnel_df = pd.DataFrame(funnel_data)
    funnel_df['conversion_rate'] = (funnel_df['users'] / funnel_df['users'].iloc[0] * 100).round(2)

    return funnel_df

def analyze_rfm(df, analysis_date=None):
    """RFM 분석"""
    purchases = df[df['event_name'] == 'purchase_complete'].copy()

    if analysis_date is None:
        analysis_date = purchases['timestamp'].max()

    rfm = purchases.groupby('user_id').agg({
        'timestamp': lambda x: (analysis_date - x.max()).days,
        'event_name': 'count',
        'price': 'sum'
    }).reset_index()

    rfm.columns = ['user_id', 'recency', 'frequency', 'monetary']

    # RFM 점수
    rfm['r_score'] = pd.qcut(rfm['recency'], 5, labels=[5, 4, 3, 2, 1])
    rfm['f_score'] = pd.qcut(rfm['frequency'].rank(method='first'), 5, labels=[1, 2, 3, 4, 5])
    rfm['m_score'] = pd.qcut(rfm['monetary'], 5, labels=[1, 2, 3, 4, 5])

    return rfm

def main():
    """메인 실행 함수"""
    # 1. 데이터 로드
    df = load_data('data/events.parquet', file_type='parquet')

    # 2. 전처리
    df = preprocess_data(df)

    # 3. 기본 통계
    print("\n=== 기본 통계 ===")
    print(f"전체 이벤트 수: {len(df):,}")
    print(f"전체 사용자 수: {df['user_id'].nunique():,}")
    print(f"분석 기간: {df['date'].min()} ~ {df['date'].max()}")

    # 4. 퍼널 분석
    print("\n=== 퍼널 분석 ===")
    funnel_steps = ['product_view', 'add_to_cart', 'checkout_start', 'purchase_complete']
    funnel_df = analyze_funnel(df, funnel_steps)
    print(funnel_df)

    # 5. RFM 분석
    print("\n=== RFM 분석 ===")
    rfm = analyze_rfm(df)
    print(rfm.head(10))

    # 6. 결과 저장
    funnel_df.to_csv('output/funnel_analysis.csv', index=False, encoding='utf-8-sig')
    rfm.to_csv('output/rfm_analysis.csv', index=False, encoding='utf-8-sig')

    print("\n분석 완료! 결과가 output/ 폴더에 저장되었습니다.")

if __name__ == '__main__':
    main()
```

**실행 방법:**

```bash
# Jupyter Notebook으로 실행
jupyter notebook notebooks/analysis.ipynb

# Python 스크립트로 실행
python analysis.py
```

---

### 11. 실무 팁

#### 팁 1: 메모리 최적화

큰 데이터셋을 다룰 때는 메모리를 절약해야 합니다:

```python
# 데이터 타입 최적화
df['user_id'] = df['user_id'].astype('category')
df['event_name'] = df['event_name'].astype('category')
df['price'] = df['price'].astype('int32')  # int64 대신 int32

# 청크 단위로 읽기
chunk_size = 100000
chunks = []
for chunk in pd.read_csv('data/large_events.csv', chunksize=chunk_size):
    # 각 청크 처리
    chunk_processed = chunk[chunk['event_name'] == 'purchase_complete']
    chunks.append(chunk_processed)

df = pd.concat(chunks, ignore_index=True)
```

#### 팁 2: 성능 측정

```python
import time

start = time.time()

# 분석 코드
df = pd.read_parquet('data/events.parquet')
result = df.groupby('event_name').size()

elapsed = time.time() - start
print(f"실행 시간: {elapsed:.2f}초")
```

#### 팁 3: 재사용 가능한 함수 만들기

```python
def calculate_conversion_rate(df, from_event, to_event):
    """두 이벤트 간 전환율 계산"""
    from_users = df[df['event_name'] == from_event]['user_id'].nunique()
    to_users = df[df['event_name'] == to_event]['user_id'].nunique()

    if from_users == 0:
        return 0

    conversion = (to_users / from_users) * 100
    return round(conversion, 2)

# 사용 예시
view_to_cart = calculate_conversion_rate(df, 'product_view', 'add_to_cart')
cart_to_purchase = calculate_conversion_rate(df, 'add_to_cart', 'purchase_complete')

print(f"조회→장바구니: {view_to_cart}%")
print(f"장바구니→구매: {cart_to_purchase}%")
```

---

이것으로 부록 C-1을 마칩니다. 이제 Python과 Pandas로 실제 데이터를 분석할 수 있습니다!

---

## 부록 C-2: SQL (MySQL) 실습 가이드

> **목표:** SQL로 트래킹 데이터를 저장하고 조회하며, 실무에서 자주 사용하는 분석 쿼리를 익힙니다.

### 1. 환경 준비

#### MySQL 설치 (Mac)

```bash
# Homebrew로 MySQL 설치
brew install mysql

# MySQL 서비스 시작
brew services start mysql

# MySQL 접속 (초기 비밀번호 없음)
mysql -u root

# root 비밀번호 설정
ALTER USER 'root'@'localhost' IDENTIFIED BY 'your_password';
```

#### MySQL 접속

```bash
# 비밀번호로 접속
mysql -u root -p

# 또는 특정 데이터베이스 접속
mysql -u root -p tracking_db
```

#### MySQL Workbench (GUI 도구)

```bash
# GUI 도구 설치 (선택)
brew install --cask mysqlworkbench
```

---

### 2. 데이터베이스 및 테이블 생성

#### 2-1. 데이터베이스 생성

```sql
-- 데이터베이스 생성
CREATE DATABASE tracking_db
  DEFAULT CHARACTER SET utf8mb4
  DEFAULT COLLATE utf8mb4_unicode_ci;

-- 데이터베이스 선택
USE tracking_db;

-- 데이터베이스 목록 확인
SHOW DATABASES;
```

#### 2-2. 이벤트 테이블 생성

```sql
-- 이벤트 테이블
CREATE TABLE events (
    id BIGINT AUTO_INCREMENT PRIMARY KEY,
    event_name VARCHAR(100) NOT NULL,
    user_id VARCHAR(100) NOT NULL,
    session_id VARCHAR(100),
    timestamp DATETIME NOT NULL,
    platform VARCHAR(50),
    device_type VARCHAR(50),
    os_version VARCHAR(50),
    app_version VARCHAR(50),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    INDEX idx_event_name (event_name),
    INDEX idx_user_id (user_id),
    INDEX idx_timestamp (timestamp)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;

-- 테이블 구조 확인
DESCRIBE events;
```

**인덱스 설명:**
- `idx_event_name`: 이벤트 종류별 조회 최적화
- `idx_user_id`: 사용자별 조회 최적화
- `idx_timestamp`: 날짜 범위 조회 최적화

#### 2-3. 구매 이벤트 테이블 (상세 정보)

```sql
-- 구매 이벤트 상세 테이블
CREATE TABLE purchase_events (
    id BIGINT AUTO_INCREMENT PRIMARY KEY,
    event_id BIGINT NOT NULL,
    user_id VARCHAR(100) NOT NULL,
    order_id VARCHAR(100) NOT NULL,
    product_id VARCHAR(100),
    product_name VARCHAR(255),
    category VARCHAR(100),
    price DECIMAL(10, 2) NOT NULL,
    quantity INT DEFAULT 1,
    total_amount DECIMAL(10, 2) NOT NULL,
    payment_method VARCHAR(50),
    timestamp DATETIME NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (event_id) REFERENCES events(id) ON DELETE CASCADE,
    INDEX idx_user_id (user_id),
    INDEX idx_order_id (order_id),
    INDEX idx_timestamp (timestamp)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;
```

#### 2-4. 사용자 테이블

```sql
-- 사용자 정보 테이블
CREATE TABLE users (
    user_id VARCHAR(100) PRIMARY KEY,
    first_seen DATETIME NOT NULL,
    last_seen DATETIME NOT NULL,
    total_events INT DEFAULT 0,
    total_purchases INT DEFAULT 0,
    total_revenue DECIMAL(10, 2) DEFAULT 0,
    platform VARCHAR(50),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    INDEX idx_first_seen (first_seen),
    INDEX idx_last_seen (last_seen)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;
```

---

### 3. 데이터 입력 (INSERT)

#### 3-1. 단일 이벤트 입력

```sql
-- 이벤트 삽입
INSERT INTO events (event_name, user_id, session_id, timestamp, platform, device_type)
VALUES ('product_view', 'user_001', 'sess_abc123', '2025-01-15 10:00:00', 'iOS', 'mobile');

-- 입력 확인
SELECT * FROM events ORDER BY id DESC LIMIT 1;
```

#### 3-2. 여러 이벤트 한 번에 입력

```sql
-- 여러 이벤트 동시 삽입
INSERT INTO events (event_name, user_id, session_id, timestamp, platform, device_type)
VALUES
    ('product_view', 'user_001', 'sess_abc123', '2025-01-15 10:00:00', 'iOS', 'mobile'),
    ('add_to_cart', 'user_001', 'sess_abc123', '2025-01-15 10:02:30', 'iOS', 'mobile'),
    ('checkout_start', 'user_001', 'sess_abc123', '2025-01-15 10:05:00', 'iOS', 'mobile'),
    ('purchase_complete', 'user_001', 'sess_abc123', '2025-01-15 10:07:45', 'iOS', 'mobile');

-- 입력 확인
SELECT * FROM events WHERE user_id = 'user_001' ORDER BY timestamp;
```

#### 3-3. CSV 파일에서 데이터 가져오기

```sql
-- CSV 파일 로드 (MySQL의 LOAD DATA 사용)
LOAD DATA LOCAL INFILE '/path/to/events.csv'
INTO TABLE events
FIELDS TERMINATED BY ','
ENCLOSED BY '"'
LINES TERMINATED BY '\n'
IGNORE 1 ROWS
(event_name, user_id, session_id, timestamp, platform, device_type);

-- 데이터 개수 확인
SELECT COUNT(*) FROM events;
```

#### 3-4. Python에서 데이터 입력

```python
import mysql.connector
import pandas as pd

# MySQL 연결
conn = mysql.connector.connect(
    host='localhost',
    user='root',
    password='your_password',
    database='tracking_db'
)

cursor = conn.cursor()

# DataFrame에서 데이터 입력
df = pd.read_csv('events.csv')

insert_query = """
INSERT INTO events (event_name, user_id, session_id, timestamp, platform, device_type)
VALUES (%s, %s, %s, %s, %s, %s)
"""

for _, row in df.iterrows():
    cursor.execute(insert_query, (
        row['event_name'],
        row['user_id'],
        row['session_id'],
        row['timestamp'],
        row['platform'],
        row['device_type']
    ))

conn.commit()
cursor.close()
conn.close()

print(f"{len(df)} 행 입력 완료")
```

---

### 4. 기본 조회 (SELECT)

#### 4-1. 전체 데이터 조회

```sql
-- 모든 이벤트 조회 (최신 10개)
SELECT * FROM events
ORDER BY timestamp DESC
LIMIT 10;

-- 특정 컬럼만 조회
SELECT event_name, user_id, timestamp
FROM events
LIMIT 10;
```

#### 4-2. 조건부 조회 (WHERE)

```sql
-- 특정 이벤트만 조회
SELECT * FROM events
WHERE event_name = 'purchase_complete'
ORDER BY timestamp DESC;

-- 특정 날짜 범위
SELECT * FROM events
WHERE timestamp BETWEEN '2025-01-01' AND '2025-01-31'
ORDER BY timestamp DESC;

-- 특정 사용자의 이벤트
SELECT event_name, timestamp
FROM events
WHERE user_id = 'user_001'
ORDER BY timestamp;

-- 여러 조건 (AND, OR)
SELECT * FROM events
WHERE event_name = 'purchase_complete'
  AND platform = 'iOS'
  AND timestamp >= '2025-01-01'
ORDER BY timestamp DESC;
```

#### 4-3. 집계 함수 (COUNT, SUM, AVG)

```sql
-- 전체 이벤트 수
SELECT COUNT(*) AS total_events FROM events;

-- 이벤트별 발생 횟수
SELECT event_name, COUNT(*) AS count
FROM events
GROUP BY event_name
ORDER BY count DESC;

-- 사용자 수
SELECT COUNT(DISTINCT user_id) AS total_users FROM events;

-- 일별 이벤트 수
SELECT DATE(timestamp) AS date, COUNT(*) AS event_count
FROM events
GROUP BY DATE(timestamp)
ORDER BY date DESC;
```

---

### 5. 퍼널 분석 (Funnel Analysis)

#### 5-1. 기본 퍼널

```sql
-- 각 단계별 사용자 수
SELECT
    SUM(CASE WHEN event_name = 'product_view' THEN 1 ELSE 0 END) AS product_view,
    SUM(CASE WHEN event_name = 'add_to_cart' THEN 1 ELSE 0 END) AS add_to_cart,
    SUM(CASE WHEN event_name = 'checkout_start' THEN 1 ELSE 0 END) AS checkout_start,
    SUM(CASE WHEN event_name = 'purchase_complete' THEN 1 ELSE 0 END) AS purchase_complete
FROM events;

-- 각 단계별 고유 사용자 수
SELECT
    COUNT(DISTINCT CASE WHEN event_name = 'product_view' THEN user_id END) AS product_view_users,
    COUNT(DISTINCT CASE WHEN event_name = 'add_to_cart' THEN user_id END) AS add_to_cart_users,
    COUNT(DISTINCT CASE WHEN event_name = 'checkout_start' THEN user_id END) AS checkout_start_users,
    COUNT(DISTINCT CASE WHEN event_name = 'purchase_complete' THEN user_id END) AS purchase_users
FROM events;
```

**출력 예시:**
```
product_view_users | add_to_cart_users | checkout_start_users | purchase_users
        500        |        215        |         108         |       48
```

#### 5-2. 전환율 계산

```sql
-- 전환율 계산 (서브쿼리 사용)
SELECT
    product_view_users,
    add_to_cart_users,
    checkout_start_users,
    purchase_users,
    ROUND(add_to_cart_users / product_view_users * 100, 2) AS view_to_cart_rate,
    ROUND(checkout_start_users / add_to_cart_users * 100, 2) AS cart_to_checkout_rate,
    ROUND(purchase_users / checkout_start_users * 100, 2) AS checkout_to_purchase_rate,
    ROUND(purchase_users / product_view_users * 100, 2) AS overall_conversion_rate
FROM (
    SELECT
        COUNT(DISTINCT CASE WHEN event_name = 'product_view' THEN user_id END) AS product_view_users,
        COUNT(DISTINCT CASE WHEN event_name = 'add_to_cart' THEN user_id END) AS add_to_cart_users,
        COUNT(DISTINCT CASE WHEN event_name = 'checkout_start' THEN user_id END) AS checkout_start_users,
        COUNT(DISTINCT CASE WHEN event_name = 'purchase_complete' THEN user_id END) AS purchase_users
    FROM events
) AS funnel_data;
```

**출력 예시:**
```
view_to_cart_rate | cart_to_checkout_rate | checkout_to_purchase_rate | overall_conversion_rate
      43.00       |        50.23          |           44.44           |         9.60
```

#### 5-3. 일별 퍼널 전환율

```sql
-- 일별 퍼널 분석
SELECT
    DATE(timestamp) AS date,
    COUNT(DISTINCT CASE WHEN event_name = 'product_view' THEN user_id END) AS views,
    COUNT(DISTINCT CASE WHEN event_name = 'purchase_complete' THEN user_id END) AS purchases,
    ROUND(
        COUNT(DISTINCT CASE WHEN event_name = 'purchase_complete' THEN user_id END) /
        COUNT(DISTINCT CASE WHEN event_name = 'product_view' THEN user_id END) * 100,
        2
    ) AS conversion_rate
FROM events
GROUP BY DATE(timestamp)
ORDER BY date DESC;
```

---

### 6. 사용자 행동 분석

#### 6-1. 사용자별 이벤트 수

```sql
-- 사용자별 이벤트 발생 횟수
SELECT
    user_id,
    COUNT(*) AS total_events,
    COUNT(DISTINCT event_name) AS unique_events,
    MIN(timestamp) AS first_event,
    MAX(timestamp) AS last_event
FROM events
GROUP BY user_id
ORDER BY total_events DESC
LIMIT 20;
```

#### 6-2. 사용자 여정 추적

```sql
-- 특정 사용자의 전체 여정
SELECT
    user_id,
    event_name,
    timestamp,
    LAG(event_name) OVER (PARTITION BY user_id ORDER BY timestamp) AS previous_event,
    TIMESTAMPDIFF(SECOND,
        LAG(timestamp) OVER (PARTITION BY user_id ORDER BY timestamp),
        timestamp
    ) AS seconds_from_previous
FROM events
WHERE user_id = 'user_001'
ORDER BY timestamp;
```

**출력 예시:**
```
user_id  | event_name        | timestamp           | previous_event   | seconds_from_previous
user_001 | product_view      | 2025-01-15 10:00:00 | NULL            | NULL
user_001 | add_to_cart       | 2025-01-15 10:02:30 | product_view    | 150
user_001 | checkout_start    | 2025-01-15 10:05:00 | add_to_cart     | 150
user_001 | purchase_complete | 2025-01-15 10:07:45 | checkout_start  | 165
```

#### 6-3. 평균 구매 시간

```sql
-- 첫 조회부터 구매까지 평균 시간
SELECT
    AVG(time_to_purchase) / 60 AS avg_minutes_to_purchase
FROM (
    SELECT
        user_id,
        TIMESTAMPDIFF(SECOND,
            MIN(CASE WHEN event_name = 'product_view' THEN timestamp END),
            MIN(CASE WHEN event_name = 'purchase_complete' THEN timestamp END)
        ) AS time_to_purchase
    FROM events
    GROUP BY user_id
    HAVING time_to_purchase IS NOT NULL
) AS purchase_times;
```

---

### 7. 매출 분석

#### 7-1. 일별 매출

```sql
-- 일별 총 매출 (purchase_events 테이블 사용)
SELECT
    DATE(timestamp) AS date,
    COUNT(DISTINCT order_id) AS total_orders,
    SUM(total_amount) AS daily_revenue,
    AVG(total_amount) AS avg_order_value,
    COUNT(DISTINCT user_id) AS purchasing_users
FROM purchase_events
GROUP BY DATE(timestamp)
ORDER BY date DESC;
```

#### 7-2. 상품별 매출

```sql
-- 상품별 매출 순위
SELECT
    product_id,
    product_name,
    COUNT(*) AS purchase_count,
    SUM(quantity) AS total_quantity,
    SUM(total_amount) AS total_revenue,
    AVG(price) AS avg_price
FROM purchase_events
GROUP BY product_id, product_name
ORDER BY total_revenue DESC
LIMIT 10;
```

#### 7-3. 사용자별 총 구매액 (LTV)

```sql
-- 사용자별 생애 가치 (Lifetime Value)
SELECT
    user_id,
    COUNT(DISTINCT order_id) AS purchase_count,
    SUM(total_amount) AS lifetime_value,
    AVG(total_amount) AS avg_order_value,
    MIN(timestamp) AS first_purchase,
    MAX(timestamp) AS last_purchase,
    DATEDIFF(MAX(timestamp), MIN(timestamp)) AS customer_lifespan_days
FROM purchase_events
GROUP BY user_id
ORDER BY lifetime_value DESC
LIMIT 20;
```

---

### 8. 고급 쿼리 (Advanced Queries)

#### 8-1. 코호트 분석 (Cohort Analysis)

```sql
-- 가입 월별 코호트 리텐션
WITH user_cohorts AS (
    SELECT
        user_id,
        DATE_FORMAT(MIN(timestamp), '%Y-%m') AS cohort_month
    FROM events
    GROUP BY user_id
),
cohort_activity AS (
    SELECT
        uc.cohort_month,
        DATE_FORMAT(e.timestamp, '%Y-%m') AS activity_month,
        COUNT(DISTINCT e.user_id) AS active_users
    FROM user_cohorts uc
    JOIN events e ON uc.user_id = e.user_id
    GROUP BY uc.cohort_month, DATE_FORMAT(e.timestamp, '%Y-%m')
)
SELECT
    cohort_month,
    activity_month,
    active_users
FROM cohort_activity
ORDER BY cohort_month, activity_month;
```

#### 8-2. RFM 분석

```sql
-- RFM (Recency, Frequency, Monetary) 계산
SELECT
    user_id,
    DATEDIFF(NOW(), MAX(timestamp)) AS recency_days,
    COUNT(DISTINCT order_id) AS frequency,
    SUM(total_amount) AS monetary
FROM purchase_events
GROUP BY user_id
ORDER BY monetary DESC
LIMIT 20;
```

#### 8-3. 세션 분석

```sql
-- 세션당 평균 이벤트 수
SELECT
    session_id,
    COUNT(*) AS events_in_session,
    COUNT(DISTINCT event_name) AS unique_events,
    TIMESTAMPDIFF(MINUTE, MIN(timestamp), MAX(timestamp)) AS session_duration_minutes
FROM events
WHERE session_id IS NOT NULL
GROUP BY session_id
HAVING session_duration_minutes > 0
ORDER BY events_in_session DESC
LIMIT 20;
```

---

### 9. 데이터 업데이트 및 삭제

#### 9-1. 데이터 업데이트 (UPDATE)

```sql
-- 특정 사용자의 플랫폼 정보 수정
UPDATE events
SET platform = 'Android'
WHERE user_id = 'user_001' AND platform IS NULL;

-- 여러 컬럼 동시 업데이트
UPDATE events
SET
    platform = 'iOS',
    device_type = 'mobile'
WHERE user_id = 'user_002';

-- 조건부 업데이트
UPDATE users
SET total_events = (
    SELECT COUNT(*) FROM events WHERE events.user_id = users.user_id
);
```

#### 9-2. 데이터 삭제 (DELETE)

```sql
-- 특정 조건의 데이터 삭제
DELETE FROM events
WHERE event_name = 'test_event';

-- 오래된 데이터 삭제 (90일 이상)
DELETE FROM events
WHERE timestamp < DATE_SUB(NOW(), INTERVAL 90 DAY);

-- 안전한 삭제 (트랜잭션 사용)
START TRANSACTION;
DELETE FROM events WHERE id = 12345;
-- 확인 후 커밋 또는 롤백
COMMIT;
-- 또는
ROLLBACK;
```

---

### 10. 성능 최적화

#### 10-1. 인덱스 추가

```sql
-- 복합 인덱스 생성
CREATE INDEX idx_user_event_time
ON events (user_id, event_name, timestamp);

-- 인덱스 확인
SHOW INDEX FROM events;

-- 인덱스 삭제
DROP INDEX idx_user_event_time ON events;
```

#### 10-2. 쿼리 실행 계획 확인

```sql
-- EXPLAIN으로 쿼리 성능 분석
EXPLAIN SELECT * FROM events
WHERE user_id = 'user_001'
  AND timestamp >= '2025-01-01'
ORDER BY timestamp DESC;
```

#### 10-3. 파티셔닝 (대용량 데이터)

```sql
-- 날짜 기준 파티션 테이블 생성
CREATE TABLE events_partitioned (
    id BIGINT AUTO_INCREMENT,
    event_name VARCHAR(100) NOT NULL,
    user_id VARCHAR(100) NOT NULL,
    timestamp DATETIME NOT NULL,
    platform VARCHAR(50),
    PRIMARY KEY (id, timestamp),
    INDEX idx_user_id (user_id),
    INDEX idx_event_name (event_name)
) ENGINE=InnoDB
PARTITION BY RANGE (YEAR(timestamp) * 100 + MONTH(timestamp)) (
    PARTITION p202501 VALUES LESS THAN (202502),
    PARTITION p202502 VALUES LESS THAN (202503),
    PARTITION p202503 VALUES LESS THAN (202504),
    PARTITION p_future VALUES LESS THAN MAXVALUE
);
```

---

### 11. 실무 활용 예시

#### 예시 1: 일일 대시보드 쿼리

```sql
-- 오늘의 주요 지표
SELECT
    COUNT(*) AS total_events,
    COUNT(DISTINCT user_id) AS active_users,
    COUNT(DISTINCT CASE WHEN event_name = 'purchase_complete' THEN user_id END) AS purchasers,
    ROUND(
        COUNT(DISTINCT CASE WHEN event_name = 'purchase_complete' THEN user_id END) /
        COUNT(DISTINCT user_id) * 100,
        2
    ) AS conversion_rate
FROM events
WHERE DATE(timestamp) = CURDATE();
```

#### 예시 2: 주간 트렌드 리포트

```sql
-- 최근 7일간 일별 추이
SELECT
    DATE(timestamp) AS date,
    COUNT(*) AS events,
    COUNT(DISTINCT user_id) AS users,
    COUNT(DISTINCT CASE WHEN event_name = 'purchase_complete' THEN user_id END) AS purchasers
FROM events
WHERE timestamp >= DATE_SUB(CURDATE(), INTERVAL 7 DAY)
GROUP BY DATE(timestamp)
ORDER BY date;
```

#### 예시 3: 이탈 사용자 찾기

```sql
-- 30일 이상 활동이 없는 사용자
SELECT
    user_id,
    MAX(timestamp) AS last_seen,
    DATEDIFF(NOW(), MAX(timestamp)) AS days_inactive,
    COUNT(*) AS total_events
FROM events
GROUP BY user_id
HAVING days_inactive >= 30
ORDER BY days_inactive DESC;
```

---

### 12. 백업 및 복원

#### 데이터 백업

```bash
# 전체 데이터베이스 백업
mysqldump -u root -p tracking_db > backup_tracking_db.sql

# 특정 테이블만 백업
mysqldump -u root -p tracking_db events > backup_events.sql

# 데이터만 백업 (구조 제외)
mysqldump -u root -p --no-create-info tracking_db events > backup_events_data.sql
```

#### 데이터 복원

```bash
# 백업 파일로부터 복원
mysql -u root -p tracking_db < backup_tracking_db.sql

# 새 데이터베이스에 복원
mysql -u root -p -e "CREATE DATABASE tracking_db_restored"
mysql -u root -p tracking_db_restored < backup_tracking_db.sql
```

---

### 13. 실무 팁

#### 팁 1: 트랜잭션 사용

중요한 작업은 트랜잭션으로 안전하게 처리하세요:

```sql
START TRANSACTION;

-- 여러 작업 수행
INSERT INTO events (...) VALUES (...);
UPDATE users SET total_events = total_events + 1 WHERE user_id = 'user_001';

-- 문제 없으면 커밋
COMMIT;

-- 문제 발생 시 롤백
ROLLBACK;
```

#### 팁 2: 느린 쿼리 로깅

```sql
-- 느린 쿼리 로그 활성화
SET GLOBAL slow_query_log = 'ON';
SET GLOBAL long_query_time = 2;  -- 2초 이상 걸리는 쿼리 기록

-- 느린 쿼리 로그 위치 확인
SHOW VARIABLES LIKE 'slow_query_log_file';
```

#### 팁 3: 정기적인 테이블 최적화

```sql
-- 테이블 최적화
OPTIMIZE TABLE events;

-- 테이블 분석 (통계 업데이트)
ANALYZE TABLE events;
```

---

이것으로 부록 C-2를 마칩니다. 이제 SQL로 트래킹 데이터를 저장하고 분석할 수 있습니다!

---

## 부록 C-3: BigQuery 실습 가이드

> **목표:** Google BigQuery로 대용량 트래킹 데이터를 빠르게 분석하고, GA4/Amplitude 데이터를 실무에서 활용합니다.

### 1. BigQuery 시작하기

#### 1-1. BigQuery란?

BigQuery는 Google Cloud Platform의 서버리스 데이터 웨어하우스입니다:

**특징:**
- **빠른 속도**: 테라바이트급 데이터도 초 단위 조회
- **자동 확장**: 인프라 관리 불필요
- **표준 SQL**: 익숙한 SQL 문법 사용
- **비용 효율**: 쿼리한 데이터양만큼만 과금

**실무 활용:**
- Google Analytics 4 데이터 분석
- Amplitude Export 데이터 분석
- 대규모 트래킹 로그 분석

#### 1-2. GCP 프로젝트 생성

```bash
# 1. https://console.cloud.google.com 접속
# 2. 새 프로젝트 생성 (예: tracking-analytics)
# 3. 결제 계정 연결 (무료 크레딧 $300 제공)
# 4. BigQuery API 활성화
```

#### 1-3. 접속 방법

**웹 콘솔:**
- https://console.cloud.google.com/bigquery

**gcloud CLI 설치:**
```bash
# macOS
brew install --cask google-cloud-sdk

# 인증
gcloud auth login
gcloud config set project tracking-analytics
```

**Python 라이브러리:**
```bash
pip install google-cloud-bigquery pandas-gbq
```

---

### 2. 데이터셋 및 테이블 생성

#### 2-1. 데이터셋 생성

```sql
-- 웹 콘솔에서 생성하거나 SQL로 생성
CREATE SCHEMA IF NOT EXISTS `tracking-analytics.tracking_data`
OPTIONS(
  location="us",
  description="트래킹 데이터 저장소"
);
```

#### 2-2. 이벤트 테이블 생성

```sql
-- 기본 이벤트 테이블
CREATE OR REPLACE TABLE `tracking-analytics.tracking_data.events` (
  event_id STRING,
  event_name STRING NOT NULL,
  user_id STRING NOT NULL,
  session_id STRING,
  timestamp TIMESTAMP NOT NULL,

  -- 컨텍스트 정보
  platform STRING,
  device_type STRING,
  os_version STRING,
  app_version STRING,

  -- 사용자 속성 (STRUCT)
  user_properties STRUCT<
    membership_tier STRING,
    signup_date DATE,
    country STRING
  >,

  -- 이벤트 파라미터 (ARRAY)
  event_params ARRAY<STRUCT<
    key STRING,
    value STRUCT<
      string_value STRING,
      int_value INT64,
      float_value FLOAT64,
      double_value FLOAT64
    >
  >>
)
PARTITION BY DATE(timestamp)
CLUSTER BY event_name, user_id
OPTIONS(
  description="사용자 이벤트 로그",
  require_partition_filter=true
);
```

**중요 개념:**
- `PARTITION BY DATE(timestamp)`: 날짜별로 데이터 분할 → 쿼리 비용 절감
- `CLUSTER BY`: 특정 컬럼으로 정렬 → 쿼리 성능 향상
- `require_partition_filter=true`: 파티션 필터 필수 → 전체 스캔 방지

#### 2-3. 구매 이벤트 테이블

```sql
-- 구매 전용 테이블 (플랫 구조)
CREATE OR REPLACE TABLE `tracking-analytics.tracking_data.purchases` (
  purchase_id STRING,
  user_id STRING NOT NULL,
  order_id STRING NOT NULL,
  timestamp TIMESTAMP NOT NULL,

  -- 상품 정보
  product_id STRING,
  product_name STRING,
  category STRING,

  -- 금액 정보
  price NUMERIC(10, 2),
  quantity INT64,
  total_amount NUMERIC(10, 2),
  currency STRING DEFAULT 'KRW',

  -- 결제 정보
  payment_method STRING,
  payment_provider STRING
)
PARTITION BY DATE(timestamp)
CLUSTER BY user_id, product_id;
```

---

### 3. 데이터 입력

#### 3-1. SQL로 직접 입력

```sql
-- 단일 행 삽입
INSERT INTO `tracking-analytics.tracking_data.events`
  (event_id, event_name, user_id, session_id, timestamp, platform, device_type, event_params)
VALUES
  (
    GENERATE_UUID(),
    'product_view',
    'user_001',
    'sess_abc123',
    TIMESTAMP('2025-01-15 10:00:00'),
    'iOS',
    'mobile',
    [
      STRUCT('product_id' AS key, STRUCT('PROD-123' AS string_value, NULL AS int_value, NULL AS float_value, NULL AS double_value) AS value),
      STRUCT('price' AS key, STRUCT(NULL AS string_value, 89000 AS int_value, NULL AS float_value, NULL AS double_value) AS value)
    ]
  );
```

#### 3-2. CSV 파일 업로드

```bash
# CSV 파일을 BigQuery로 로드
bq load \
  --source_format=CSV \
  --skip_leading_rows=1 \
  --autodetect \
  tracking-analytics:tracking_data.events \
  gs://my-bucket/events.csv
```

#### 3-3. Python으로 데이터 입력

```python
from google.cloud import bigquery
import pandas as pd

# BigQuery 클라이언트 생성
client = bigquery.Client(project='tracking-analytics')

# DataFrame 생성
df = pd.DataFrame({
    'event_id': ['evt_001', 'evt_002', 'evt_003'],
    'event_name': ['product_view', 'add_to_cart', 'purchase_complete'],
    'user_id': ['user_001', 'user_001', 'user_001'],
    'timestamp': pd.to_datetime([
        '2025-01-15 10:00:00',
        '2025-01-15 10:02:30',
        '2025-01-15 10:07:45'
    ]),
    'platform': ['iOS', 'iOS', 'iOS']
})

# BigQuery에 삽입
table_id = 'tracking-analytics.tracking_data.events'
job = client.load_table_from_dataframe(df, table_id)
job.result()  # 완료 대기

print(f"{len(df)} 행 입력 완료")
```

#### 3-4. JSON Lines 파일 로드 (실무에서 가장 많이 사용)

```sql
-- GCS의 JSONL 파일을 테이블로 로드
LOAD DATA OVERWRITE `tracking-analytics.tracking_data.events`
FROM FILES (
  format = 'JSON',
  uris = ['gs://my-bucket/events/*.jsonl']
);
```

---

### 4. 기본 쿼리

#### 4-1. 전체 데이터 조회

```sql
-- 최근 10개 이벤트 조회
SELECT
  event_name,
  user_id,
  timestamp,
  platform
FROM `tracking-analytics.tracking_data.events`
WHERE DATE(timestamp) = '2025-01-15'  -- 파티션 필터 필수
ORDER BY timestamp DESC
LIMIT 10;
```

#### 4-2. 이벤트 집계

```sql
-- 이벤트별 발생 횟수
SELECT
  event_name,
  COUNT(*) AS event_count,
  COUNT(DISTINCT user_id) AS unique_users
FROM `tracking-analytics.tracking_data.events`
WHERE DATE(timestamp) BETWEEN '2025-01-01' AND '2025-01-31'
GROUP BY event_name
ORDER BY event_count DESC;
```

#### 4-3. 일별 추이

```sql
-- 일별 이벤트 발생 추이
SELECT
  DATE(timestamp) AS date,
  event_name,
  COUNT(*) AS event_count,
  COUNT(DISTINCT user_id) AS unique_users
FROM `tracking-analytics.tracking_data.events`
WHERE DATE(timestamp) >= DATE_SUB(CURRENT_DATE(), INTERVAL 30 DAY)
GROUP BY date, event_name
ORDER BY date DESC, event_count DESC;
```

---

### 5. UNNEST로 배열/구조체 다루기

BigQuery의 가장 중요한 개념입니다. GA4, Amplitude 데이터는 모두 중첩 구조입니다.

#### 5-1. ARRAY 언네스팅

```sql
-- event_params 배열에서 특정 파라미터 추출
SELECT
  event_name,
  user_id,
  timestamp,
  -- 배열을 행으로 펼침
  param.key AS param_key,
  param.value.string_value,
  param.value.int_value
FROM `tracking-analytics.tracking_data.events`,
  UNNEST(event_params) AS param
WHERE DATE(timestamp) = '2025-01-15'
  AND event_name = 'product_view'
LIMIT 10;
```

**출력 예시:**
```
event_name   | user_id  | timestamp           | param_key  | string_value | int_value
product_view | user_001 | 2025-01-15 10:00:00 | product_id | PROD-123    | NULL
product_view | user_001 | 2025-01-15 10:00:00 | price      | NULL        | 89000
```

#### 5-2. 특정 파라미터만 추출 (헬퍼 함수)

```sql
-- 파라미터 값 추출 함수
CREATE TEMP FUNCTION GET_PARAM_STRING(params ARRAY<STRUCT<key STRING, value STRUCT<string_value STRING, int_value INT64, float_value FLOAT64, double_value FLOAT64>>>, param_key STRING)
AS (
  (SELECT value.string_value FROM UNNEST(params) WHERE key = param_key)
);

CREATE TEMP FUNCTION GET_PARAM_INT(params ARRAY<STRUCT<key STRING, value STRUCT<string_value STRING, int_value INT64, float_value FLOAT64, double_value FLOAT64>>>, param_key STRING)
AS (
  (SELECT value.int_value FROM UNNEST(params) WHERE key = param_key)
);

-- 사용 예시
SELECT
  event_name,
  user_id,
  GET_PARAM_STRING(event_params, 'product_id') AS product_id,
  GET_PARAM_INT(event_params, 'price') AS price,
  GET_PARAM_STRING(event_params, 'category') AS category
FROM `tracking-analytics.tracking_data.events`
WHERE DATE(timestamp) = '2025-01-15'
  AND event_name = 'product_view'
LIMIT 10;
```

**출력 예시:**
```
event_name   | user_id  | product_id | price | category
product_view | user_001 | PROD-123   | 89000 | electronics
product_view | user_002 | PROD-456   | 45000 | fashion
```

---

### 6. GA4 데이터 분석

#### 6-1. GA4 BigQuery Export 구조

GA4 데이터는 `events_YYYYMMDD` 형식의 테이블로 자동 저장됩니다:

```sql
-- GA4 테이블 구조 확인
SELECT
  event_name,
  event_timestamp,
  user_pseudo_id,
  device.category AS device_category,
  geo.country AS country,
  traffic_source.source AS source,
  traffic_source.medium AS medium
FROM `project-id.analytics_123456789.events_20250115`
LIMIT 10;
```

#### 6-2. GA4 이벤트 파라미터 추출

```sql
-- GA4 event_params에서 값 추출
SELECT
  event_name,
  user_pseudo_id,
  TIMESTAMP_MICROS(event_timestamp) AS event_time,

  -- 파라미터 추출
  (SELECT value.string_value FROM UNNEST(event_params) WHERE key = 'page_location') AS page_url,
  (SELECT value.int_value FROM UNNEST(event_params) WHERE key = 'engagement_time_msec') AS engagement_time,
  (SELECT value.string_value FROM UNNEST(event_params) WHERE key = 'session_engaged') AS session_engaged

FROM `project-id.analytics_123456789.events_*`
WHERE _TABLE_SUFFIX BETWEEN '20250101' AND '20250131'
  AND event_name = 'page_view'
LIMIT 100;
```

#### 6-3. GA4 이커머스 이벤트 분석

```sql
-- GA4 구매 이벤트 분석
SELECT
  DATE(TIMESTAMP_MICROS(event_timestamp)) AS purchase_date,
  user_pseudo_id,

  -- 거래 정보
  (SELECT value.string_value FROM UNNEST(event_params) WHERE key = 'transaction_id') AS transaction_id,
  (SELECT value.double_value FROM UNNEST(event_params) WHERE key = 'value') AS revenue,
  (SELECT value.string_value FROM UNNEST(event_params) WHERE key = 'currency') AS currency,

  -- 상품 정보 (items 배열)
  items.item_id,
  items.item_name,
  items.price,
  items.quantity

FROM `project-id.analytics_123456789.events_*`,
  UNNEST(items) AS items
WHERE _TABLE_SUFFIX BETWEEN '20250101' AND '20250131'
  AND event_name = 'purchase'
ORDER BY purchase_date DESC;
```

---

### 7. 퍼널 분석

#### 7-1. 기본 퍼널

```sql
-- 구매 퍼널 전환율
WITH funnel_steps AS (
  SELECT
    DATE(timestamp) AS date,
    user_id,
    COUNTIF(event_name = 'product_view') AS step1_view,
    COUNTIF(event_name = 'add_to_cart') AS step2_cart,
    COUNTIF(event_name = 'checkout_start') AS step3_checkout,
    COUNTIF(event_name = 'purchase_complete') AS step4_purchase
  FROM `tracking-analytics.tracking_data.events`
  WHERE DATE(timestamp) BETWEEN '2025-01-01' AND '2025-01-31'
  GROUP BY date, user_id
)
SELECT
  date,
  COUNT(DISTINCT IF(step1_view > 0, user_id, NULL)) AS users_viewed,
  COUNT(DISTINCT IF(step2_cart > 0, user_id, NULL)) AS users_carted,
  COUNT(DISTINCT IF(step3_checkout > 0, user_id, NULL)) AS users_checkout,
  COUNT(DISTINCT IF(step4_purchase > 0, user_id, NULL)) AS users_purchased,

  -- 전환율
  ROUND(COUNT(DISTINCT IF(step2_cart > 0, user_id, NULL)) / COUNT(DISTINCT IF(step1_view > 0, user_id, NULL)) * 100, 2) AS view_to_cart_rate,
  ROUND(COUNT(DISTINCT IF(step4_purchase > 0, user_id, NULL)) / COUNT(DISTINCT IF(step1_view > 0, user_id, NULL)) * 100, 2) AS overall_conversion_rate
FROM funnel_steps
GROUP BY date
ORDER BY date DESC;
```

#### 7-2. 순차 퍼널 (시간 순서 고려)

```sql
-- 사용자가 순서대로 퍼널을 통과했는지 확인
WITH user_events AS (
  SELECT
    user_id,
    event_name,
    timestamp,
    ROW_NUMBER() OVER (PARTITION BY user_id ORDER BY timestamp) AS event_order
  FROM `tracking-analytics.tracking_data.events`
  WHERE DATE(timestamp) BETWEEN '2025-01-01' AND '2025-01-31'
    AND event_name IN ('product_view', 'add_to_cart', 'checkout_start', 'purchase_complete')
),
funnel_completion AS (
  SELECT
    user_id,
    MAX(CASE WHEN event_name = 'product_view' THEN event_order END) AS view_order,
    MAX(CASE WHEN event_name = 'add_to_cart' THEN event_order END) AS cart_order,
    MAX(CASE WHEN event_name = 'checkout_start' THEN event_order END) AS checkout_order,
    MAX(CASE WHEN event_name = 'purchase_complete' THEN event_order END) AS purchase_order
  FROM user_events
  GROUP BY user_id
)
SELECT
  COUNT(DISTINCT user_id) AS total_users,
  COUNT(DISTINCT IF(view_order IS NOT NULL, user_id, NULL)) AS reached_view,
  COUNT(DISTINCT IF(cart_order > view_order, user_id, NULL)) AS reached_cart,
  COUNT(DISTINCT IF(checkout_order > cart_order, user_id, NULL)) AS reached_checkout,
  COUNT(DISTINCT IF(purchase_order > checkout_order, user_id, NULL)) AS completed_purchase
FROM funnel_completion;
```

---

### 8. 코호트 분석

#### 8-1. 월별 코호트 리텐션

```sql
-- 가입 월별 리텐션
WITH user_cohorts AS (
  SELECT
    user_id,
    DATE_TRUNC(MIN(DATE(timestamp)), MONTH) AS cohort_month
  FROM `tracking-analytics.tracking_data.events`
  WHERE DATE(timestamp) >= '2024-01-01'
  GROUP BY user_id
),
user_activity AS (
  SELECT
    uc.user_id,
    uc.cohort_month,
    DATE_TRUNC(DATE(e.timestamp), MONTH) AS activity_month
  FROM user_cohorts uc
  JOIN `tracking-analytics.tracking_data.events` e
    ON uc.user_id = e.user_id
  WHERE DATE(e.timestamp) >= '2024-01-01'
  GROUP BY uc.user_id, uc.cohort_month, activity_month
)
SELECT
  cohort_month,
  activity_month,
  COUNT(DISTINCT user_id) AS active_users,
  DATE_DIFF(activity_month, cohort_month, MONTH) AS months_since_cohort
FROM user_activity
GROUP BY cohort_month, activity_month
ORDER BY cohort_month, activity_month;
```

#### 8-2. 리텐션율 피벗

```sql
-- 리텐션율을 피벗 테이블로 표시
WITH cohort_retention AS (
  -- (위의 쿼리 결과 사용)
  SELECT
    cohort_month,
    months_since_cohort,
    active_users,
    FIRST_VALUE(active_users) OVER (PARTITION BY cohort_month ORDER BY months_since_cohort) AS cohort_size
  FROM (
    -- 코호트 데이터
    SELECT
      cohort_month,
      activity_month,
      COUNT(DISTINCT user_id) AS active_users,
      DATE_DIFF(activity_month, cohort_month, MONTH) AS months_since_cohort
    FROM user_activity
    GROUP BY cohort_month, activity_month
  )
)
SELECT
  cohort_month,
  cohort_size,
  ROUND(MAX(IF(months_since_cohort = 0, active_users / cohort_size * 100, NULL)), 2) AS month_0,
  ROUND(MAX(IF(months_since_cohort = 1, active_users / cohort_size * 100, NULL)), 2) AS month_1,
  ROUND(MAX(IF(months_since_cohort = 2, active_users / cohort_size * 100, NULL)), 2) AS month_2,
  ROUND(MAX(IF(months_since_cohort = 3, active_users / cohort_size * 100, NULL)), 2) AS month_3
FROM cohort_retention
GROUP BY cohort_month, cohort_size
ORDER BY cohort_month;
```

---

### 9. 고급 분석

#### 9-1. 사용자 세션 재구성

```sql
-- 30분 기준 세션 구분
WITH events_with_session AS (
  SELECT
    user_id,
    event_name,
    timestamp,
    -- 이전 이벤트와의 시간 차이
    TIMESTAMP_DIFF(
      timestamp,
      LAG(timestamp) OVER (PARTITION BY user_id ORDER BY timestamp),
      MINUTE
    ) AS minutes_since_last_event,
    -- 30분 이상 차이나면 새 세션
    SUM(
      IF(
        TIMESTAMP_DIFF(
          timestamp,
          LAG(timestamp) OVER (PARTITION BY user_id ORDER BY timestamp),
          MINUTE
        ) > 30 OR LAG(timestamp) OVER (PARTITION BY user_id ORDER BY timestamp) IS NULL,
        1,
        0
      )
    ) OVER (PARTITION BY user_id ORDER BY timestamp) AS session_number
  FROM `tracking-analytics.tracking_data.events`
  WHERE DATE(timestamp) BETWEEN '2025-01-01' AND '2025-01-31'
)
SELECT
  user_id,
  session_number,
  MIN(timestamp) AS session_start,
  MAX(timestamp) AS session_end,
  COUNT(*) AS events_in_session,
  TIMESTAMP_DIFF(MAX(timestamp), MIN(timestamp), MINUTE) AS session_duration_minutes
FROM events_with_session
GROUP BY user_id, session_number
ORDER BY user_id, session_number;
```

#### 9-2. 구매 예측 (머신러닝 준비)

```sql
-- 구매 가능성이 높은 사용자 특성 추출
WITH user_features AS (
  SELECT
    user_id,
    -- 활동 지표
    COUNT(*) AS total_events,
    COUNT(DISTINCT DATE(timestamp)) AS active_days,
    COUNT(DISTINCT IF(event_name = 'product_view', 1, NULL)) AS product_views,
    COUNT(DISTINCT IF(event_name = 'add_to_cart', 1, NULL)) AS cart_adds,

    -- 구매 여부
    MAX(IF(event_name = 'purchase_complete', 1, 0)) AS has_purchased,

    -- 시간 패턴
    AVG(EXTRACT(HOUR FROM timestamp)) AS avg_active_hour,

    -- 플랫폼
    APPROX_TOP_COUNT(platform, 1)[OFFSET(0)].value AS primary_platform
  FROM `tracking-analytics.tracking_data.events`
  WHERE DATE(timestamp) BETWEEN '2025-01-01' AND '2025-01-31'
  GROUP BY user_id
)
SELECT
  has_purchased,
  COUNT(*) AS user_count,
  AVG(total_events) AS avg_events,
  AVG(active_days) AS avg_active_days,
  AVG(product_views) AS avg_product_views,
  AVG(cart_adds) AS avg_cart_adds
FROM user_features
GROUP BY has_purchased;
```

---

### 10. 성능 최적화

#### 10-1. 파티션 프루닝

```sql
-- ❌ 나쁜 예: 전체 테이블 스캔 (비용 높음)
SELECT COUNT(*)
FROM `tracking-analytics.tracking_data.events`
WHERE event_name = 'purchase_complete';

-- ✅ 좋은 예: 파티션 필터 사용 (비용 절감)
SELECT COUNT(*)
FROM `tracking-analytics.tracking_data.events`
WHERE DATE(timestamp) BETWEEN '2025-01-01' AND '2025-01-31'
  AND event_name = 'purchase_complete';
```

#### 10-2. 클러스터링 활용

```sql
-- 클러스터 컬럼으로 필터링하면 성능 향상
SELECT *
FROM `tracking-analytics.tracking_data.events`
WHERE DATE(timestamp) = '2025-01-15'
  AND event_name = 'purchase_complete'  -- 클러스터 컬럼
  AND user_id = 'user_001';  -- 클러스터 컬럼
```

#### 10-3. 쿼리 결과 캐싱

```sql
-- 동일한 쿼리를 24시간 내 다시 실행하면 캐시 사용 (무료)
-- 캐시 사용 안 함 옵션
SELECT *
FROM `tracking-analytics.tracking_data.events`
WHERE DATE(timestamp) = '2025-01-15'
OPTIONS(use_cache=false);
```

#### 10-4. 구체화된 뷰 (Materialized View)

```sql
-- 자주 사용하는 집계를 미리 계산
CREATE MATERIALIZED VIEW `tracking-analytics.tracking_data.daily_summary`
PARTITION BY summary_date
AS
SELECT
  DATE(timestamp) AS summary_date,
  event_name,
  COUNT(*) AS event_count,
  COUNT(DISTINCT user_id) AS unique_users
FROM `tracking-analytics.tracking_data.events`
GROUP BY summary_date, event_name;

-- 사용 (훨씬 빠름)
SELECT * FROM `tracking-analytics.tracking_data.daily_summary`
WHERE summary_date BETWEEN '2025-01-01' AND '2025-01-31';
```

---

### 11. 비용 관리

#### 11-1. 쿼리 비용 확인

```sql
-- 쿼리 실행 전 스캔할 데이터량 확인
-- 웹 콘솔에서 쿼리 입력 시 우측 상단에 "This query will process X MB" 표시

-- Python으로 예상 비용 계산
from google.cloud import bigquery

client = bigquery.Client()
query = """
  SELECT COUNT(*) FROM `tracking-analytics.tracking_data.events`
  WHERE DATE(timestamp) BETWEEN '2025-01-01' AND '2025-01-31'
"""

job_config = bigquery.QueryJobConfig(dry_run=True, use_query_cache=False)
query_job = client.query(query, job_config=job_config)

print(f"스캔할 데이터: {query_job.total_bytes_processed / 1024**3:.2f} GB")
print(f"예상 비용: ${query_job.total_bytes_processed / 1024**4 * 5:.4f}")  # $5/TB
```

#### 11-2. 비용 절감 팁

**1) 필요한 컬럼만 선택**
```sql
-- ❌ 나쁜 예
SELECT * FROM `tracking-analytics.tracking_data.events`
WHERE DATE(timestamp) = '2025-01-15';

-- ✅ 좋은 예
SELECT event_name, user_id, timestamp
FROM `tracking-analytics.tracking_data.events`
WHERE DATE(timestamp) = '2025-01-15';
```

**2) LIMIT는 비용 절감 안 됨**
```sql
-- ❌ LIMIT 10이어도 전체 테이블 스캔
SELECT * FROM `tracking-analytics.tracking_data.events` LIMIT 10;

-- ✅ 파티션 필터로 스캔 범위 제한
SELECT * FROM `tracking-analytics.tracking_data.events`
WHERE DATE(timestamp) = '2025-01-15'
LIMIT 10;
```

**3) 파티션/클러스터 활용**
```sql
-- ✅ 파티션 + 클러스터 컬럼 사용
SELECT *
FROM `tracking-analytics.tracking_data.events`
WHERE DATE(timestamp) = '2025-01-15'  -- 파티션
  AND event_name = 'purchase_complete'  -- 클러스터
  AND user_id = 'user_001';  -- 클러스터
```

---

### 12. Python 연동

#### 12-1. 데이터 조회

```python
from google.cloud import bigquery
import pandas as pd

client = bigquery.Client(project='tracking-analytics')

query = """
  SELECT
    DATE(timestamp) AS date,
    event_name,
    COUNT(*) AS event_count
  FROM `tracking-analytics.tracking_data.events`
  WHERE DATE(timestamp) BETWEEN '2025-01-01' AND '2025-01-31'
  GROUP BY date, event_name
  ORDER BY date DESC, event_count DESC
"""

# DataFrame으로 조회
df = client.query(query).to_dataframe()
print(df.head())

# 또는 pandas-gbq 사용
import pandas_gbq

df = pandas_gbq.read_gbq(query, project_id='tracking-analytics')
print(df.head())
```

#### 12-2. 데이터 저장

```python
# DataFrame을 BigQuery에 저장
import pandas as pd
from google.cloud import bigquery

client = bigquery.Client()

# 샘플 데이터
df = pd.DataFrame({
    'event_id': ['evt_001', 'evt_002'],
    'event_name': ['product_view', 'add_to_cart'],
    'user_id': ['user_001', 'user_001'],
    'timestamp': pd.to_datetime(['2025-01-15 10:00:00', '2025-01-15 10:02:30'])
})

# 저장
table_id = 'tracking-analytics.tracking_data.events'
job_config = bigquery.LoadJobConfig(
    write_disposition='WRITE_APPEND',  # 기존 데이터에 추가
    # write_disposition='WRITE_TRUNCATE',  # 기존 데이터 삭제 후 입력
)

job = client.load_table_from_dataframe(df, table_id, job_config=job_config)
job.result()

print(f"{len(df)} 행 저장 완료")
```

#### 12-3. 스케줄된 쿼리 (자동화)

```python
from google.cloud import bigquery_datatransfer

transfer_client = bigquery_datatransfer.DataTransferServiceClient()

project_id = 'tracking-analytics'
display_name = 'Daily Summary'

# 매일 오전 9시 실행
schedule = 'every day 09:00'

transfer_config = bigquery_datatransfer.TransferConfig(
    destination_dataset_id='tracking_data',
    display_name=display_name,
    data_source_id='scheduled_query',
    params={
        'query': """
          INSERT INTO `tracking-analytics.tracking_data.daily_summary`
          SELECT
            CURRENT_DATE() AS summary_date,
            event_name,
            COUNT(*) AS event_count,
            COUNT(DISTINCT user_id) AS unique_users
          FROM `tracking-analytics.tracking_data.events`
          WHERE DATE(timestamp) = CURRENT_DATE() - 1
          GROUP BY event_name
        """,
    },
    schedule=schedule,
)

parent = transfer_client.common_project_path(project_id)
transfer_config = transfer_client.create_transfer_config(
    parent=parent, transfer_config=transfer_config
)

print(f"스케줄 생성 완료: {transfer_config.name}")
```

---

### 13. 실무 활용 예시

#### 예시 1: 일일 대시보드 쿼리

```sql
-- 어제 주요 지표
DECLARE yesterday DATE DEFAULT DATE_SUB(CURRENT_DATE(), INTERVAL 1 DAY);

SELECT
  'DAU' AS metric,
  COUNT(DISTINCT user_id) AS value
FROM `tracking-analytics.tracking_data.events`
WHERE DATE(timestamp) = yesterday

UNION ALL

SELECT
  'Total Events' AS metric,
  COUNT(*) AS value
FROM `tracking-analytics.tracking_data.events`
WHERE DATE(timestamp) = yesterday

UNION ALL

SELECT
  'Purchasers' AS metric,
  COUNT(DISTINCT user_id) AS value
FROM `tracking-analytics.tracking_data.events`
WHERE DATE(timestamp) = yesterday
  AND event_name = 'purchase_complete'

UNION ALL

SELECT
  'Revenue' AS metric,
  CAST(SUM(total_amount) AS INT64) AS value
FROM `tracking-analytics.tracking_data.purchases`
WHERE DATE(timestamp) = yesterday;
```

#### 예시 2: 주간 리포트

```sql
-- 최근 7일 vs 이전 7일 비교
WITH current_week AS (
  SELECT
    COUNT(DISTINCT user_id) AS dau,
    COUNT(*) AS events,
    COUNT(DISTINCT IF(event_name = 'purchase_complete', user_id, NULL)) AS purchasers
  FROM `tracking-analytics.tracking_data.events`
  WHERE DATE(timestamp) BETWEEN DATE_SUB(CURRENT_DATE(), INTERVAL 7 DAY) AND CURRENT_DATE()
),
previous_week AS (
  SELECT
    COUNT(DISTINCT user_id) AS dau,
    COUNT(*) AS events,
    COUNT(DISTINCT IF(event_name = 'purchase_complete', user_id, NULL)) AS purchasers
  FROM `tracking-analytics.tracking_data.events`
  WHERE DATE(timestamp) BETWEEN DATE_SUB(CURRENT_DATE(), INTERVAL 14 DAY) AND DATE_SUB(CURRENT_DATE(), INTERVAL 7 DAY)
)
SELECT
  'DAU' AS metric,
  cw.dau AS current_week,
  pw.dau AS previous_week,
  ROUND((cw.dau - pw.dau) / pw.dau * 100, 2) AS change_pct
FROM current_week cw, previous_week pw

UNION ALL

SELECT
  'Events' AS metric,
  cw.events,
  pw.events,
  ROUND((cw.events - pw.events) / pw.events * 100, 2)
FROM current_week cw, previous_week pw

UNION ALL

SELECT
  'Purchasers' AS metric,
  cw.purchasers,
  pw.purchasers,
  ROUND((cw.purchasers - pw.purchasers) / pw.purchasers * 100, 2)
FROM current_week cw, previous_week pw;
```

---

### 14. 실무 팁

#### 팁 1: WITH 절 활용

복잡한 쿼리는 WITH 절로 나누면 가독성이 좋아집니다:

```sql
WITH base_events AS (
  SELECT * FROM `tracking-analytics.tracking_data.events`
  WHERE DATE(timestamp) BETWEEN '2025-01-01' AND '2025-01-31'
),
user_stats AS (
  SELECT
    user_id,
    COUNT(*) AS total_events,
    MAX(IF(event_name = 'purchase_complete', 1, 0)) AS has_purchased
  FROM base_events
  GROUP BY user_id
)
SELECT
  has_purchased,
  COUNT(*) AS user_count,
  AVG(total_events) AS avg_events
FROM user_stats
GROUP BY has_purchased;
```

#### 팁 2: 날짜 함수 마스터

```sql
-- 유용한 날짜 함수들
SELECT
  CURRENT_DATE() AS today,
  DATE_SUB(CURRENT_DATE(), INTERVAL 7 DAY) AS week_ago,
  DATE_TRUNC(CURRENT_DATE(), MONTH) AS month_start,
  LAST_DAY(CURRENT_DATE()) AS month_end,
  EXTRACT(DAYOFWEEK FROM CURRENT_DATE()) AS day_of_week,
  FORMAT_DATE('%Y-%m-%d', CURRENT_DATE()) AS formatted_date;
```

#### 팁 3: 쿼리 템플릿 저장

자주 사용하는 쿼리는 저장해두세요:

```sql
-- 웹 콘솔에서 "Save Query" 클릭
-- 또는 Saved Queries에 저장
```

---

이것으로 부록 C-3을 마칩니다. 이제 BigQuery로 대용량 트래킹 데이터를 효율적으로 분석할 수 있습니다!

---

## 부록 D: 실습 프로젝트 - 이커머스 데이터 분석

> **목표:** 실제 이커머스 데이터셋으로 트래킹 플랜 설계부터 분석까지 전 과정을 실습합니다.

### 1. 프로젝트 개요

#### 1-1. 프로젝트 배경

**회사:** "ShopKorea" 온라인 쇼핑몰
**상황:** 신규 런칭 1개월 후, 데이터 기반 의사결정 필요
**데이터:** 2025년 1월 한 달간 수집된 사용자 행동 데이터

**핵심 질문:**
1. 구매 전환율은 얼마나 되는가?
2. 어느 단계에서 가장 많이 이탈하는가?
3. 구매 가능성이 높은 사용자의 특성은?
4. 매출을 늘리려면 어디에 집중해야 하는가?

#### 1-2. 데이터셋 구조

**이벤트 종류:**
- `app_open`: 앱 실행
- `product_view`: 상품 조회
- `add_to_cart`: 장바구니 담기
- `checkout_start`: 결제 시작
- `purchase_complete`: 구매 완료

**데이터 규모:**
- 기간: 2025-01-01 ~ 2025-01-31 (31일)
- 사용자 수: 500명
- 총 이벤트: 약 10,000건
- 구매 건수: 약 50건

---

### 2. 샘플 데이터 생성

#### 2-1. Python으로 데이터 생성

```python
#!/usr/bin/env python3
# -*- coding: utf-8 -*-
"""
이커머스 샘플 데이터 생성기
"""

import pandas as pd
import numpy as np
from datetime import datetime, timedelta
import json
import random

# 시드 고정 (재현 가능하도록)
np.random.seed(42)
random.seed(42)

# 설정
NUM_USERS = 500
START_DATE = datetime(2025, 1, 1)
END_DATE = datetime(2025, 1, 31)

# 상품 목록
PRODUCTS = [
    {'id': 'PROD-001', 'name': '노트북', 'price': 1200000, 'category': 'electronics'},
    {'id': 'PROD-002', 'name': '무선 이어폰', 'price': 89000, 'category': 'electronics'},
    {'id': 'PROD-003', 'name': '스마트워치', 'price': 350000, 'category': 'electronics'},
    {'id': 'PROD-004', 'name': '백팩', 'price': 65000, 'category': 'fashion'},
    {'id': 'PROD-005', 'name': '운동화', 'price': 120000, 'category': 'fashion'},
    {'id': 'PROD-006', 'name': '티셔츠', 'price': 29000, 'category': 'fashion'},
    {'id': 'PROD-007', 'name': '책상', 'price': 180000, 'category': 'furniture'},
    {'id': 'PROD-008', 'name': '의자', 'price': 220000, 'category': 'furniture'},
]

PLATFORMS = ['iOS', 'Android', 'Web']
DEVICE_TYPES = ['mobile', 'mobile', 'desktop']

def generate_user_id(i):
    """사용자 ID 생성"""
    return f"user_{i:04d}"

def random_timestamp(start, end):
    """랜덤 타임스탬프 생성"""
    delta = end - start
    random_seconds = random.randint(0, int(delta.total_seconds()))
    return start + timedelta(seconds=random_seconds)

def generate_events():
    """이벤트 데이터 생성"""
    events = []

    for user_idx in range(NUM_USERS):
        user_id = generate_user_id(user_idx)

        # 사용자 특성
        platform = random.choice(PLATFORMS)
        device_type = DEVICE_TYPES[PLATFORMS.index(platform)]

        # 활동 수준 (20%는 고활동, 80%는 저활동)
        is_active = random.random() < 0.2
        num_sessions = random.randint(3, 10) if is_active else random.randint(1, 3)

        for session_idx in range(num_sessions):
            session_id = f"sess_{user_id}_{session_idx}"
            session_start = random_timestamp(START_DATE, END_DATE)

            # 앱 실행
            events.append({
                'event_id': f"evt_{len(events):06d}",
                'event_name': 'app_open',
                'user_id': user_id,
                'session_id': session_id,
                'timestamp': session_start,
                'platform': platform,
                'device_type': device_type,
            })

            # 상품 조회 (1-5개)
            num_views = random.randint(1, 5)
            current_time = session_start

            for view_idx in range(num_views):
                current_time += timedelta(seconds=random.randint(10, 120))
                product = random.choice(PRODUCTS)

                events.append({
                    'event_id': f"evt_{len(events):06d}",
                    'event_name': 'product_view',
                    'user_id': user_id,
                    'session_id': session_id,
                    'timestamp': current_time,
                    'platform': platform,
                    'device_type': device_type,
                    'product_id': product['id'],
                    'product_name': product['name'],
                    'price': product['price'],
                    'category': product['category'],
                })

                # 30% 확률로 장바구니 담기
                if random.random() < 0.3:
                    current_time += timedelta(seconds=random.randint(5, 60))
                    events.append({
                        'event_id': f"evt_{len(events):06d}",
                        'event_name': 'add_to_cart',
                        'user_id': user_id,
                        'session_id': session_id,
                        'timestamp': current_time,
                        'platform': platform,
                        'device_type': device_type,
                        'product_id': product['id'],
                        'product_name': product['name'],
                        'price': product['price'],
                        'category': product['category'],
                    })

                    # 50% 확률로 결제 시작
                    if random.random() < 0.5:
                        current_time += timedelta(seconds=random.randint(10, 180))
                        events.append({
                            'event_id': f"evt_{len(events):06d}",
                            'event_name': 'checkout_start',
                            'user_id': user_id,
                            'session_id': session_id,
                            'timestamp': current_time,
                            'platform': platform,
                            'device_type': device_type,
                            'product_id': product['id'],
                            'product_name': product['name'],
                            'price': product['price'],
                            'category': product['category'],
                        })

                        # 60% 확률로 구매 완료
                        if random.random() < 0.6:
                            current_time += timedelta(seconds=random.randint(30, 300))
                            payment_method = random.choice(['card', 'kakaopay', 'naverpay'])

                            events.append({
                                'event_id': f"evt_{len(events):06d}",
                                'event_name': 'purchase_complete',
                                'user_id': user_id,
                                'session_id': session_id,
                                'timestamp': current_time,
                                'platform': platform,
                                'device_type': device_type,
                                'product_id': product['id'],
                                'product_name': product['name'],
                                'price': product['price'],
                                'category': product['category'],
                                'payment_method': payment_method,
                                'order_id': f"ORD-2025-{len([e for e in events if e['event_name'] == 'purchase_complete']) + 1:05d}",
                            })

    return pd.DataFrame(events)

# 데이터 생성
print("이벤트 데이터 생성 중...")
df_events = generate_events()

# 기본 통계
print(f"\n=== 데이터 생성 완료 ===")
print(f"전체 이벤트 수: {len(df_events):,}")
print(f"전체 사용자 수: {df_events['user_id'].nunique():,}")
print(f"\n이벤트별 발생 횟수:")
print(df_events['event_name'].value_counts())

# CSV로 저장
df_events.to_csv('data/events.csv', index=False, encoding='utf-8-sig')
print(f"\n저장 완료: data/events.csv")

# JSONL로 저장
with open('data/events.jsonl', 'w', encoding='utf-8') as f:
    for _, row in df_events.iterrows():
        json.dump(row.to_dict(), f, ensure_ascii=False, default=str)
        f.write('\n')
print(f"저장 완료: data/events.jsonl")

# Parquet로 저장
df_events['timestamp'] = pd.to_datetime(df_events['timestamp'])
df_events.to_parquet('data/events.parquet', index=False, compression='snappy')
print(f"저장 완료: data/events.parquet")
```

**실행:**
```bash
python generate_sample_data.py
```

---

### 3. 미션 1: 퍼널 전환율 분석

#### 목표
각 단계별 전환율을 계산하고, 가장 큰 이탈 지점을 찾으세요.

#### 힌트
```python
# Pandas로 퍼널 분석
import pandas as pd

df = pd.read_parquet('data/events.parquet')

funnel_steps = ['product_view', 'add_to_cart', 'checkout_start', 'purchase_complete']

# 각 단계별 사용자 수
funnel_data = []
for step in funnel_steps:
    user_count = df[df['event_name'] == step]['user_id'].nunique()
    funnel_data.append({'step': step, 'users': user_count})

funnel_df = pd.DataFrame(funnel_data)
funnel_df['conversion_rate'] = (funnel_df['users'] / funnel_df['users'].iloc[0] * 100).round(2)

print(funnel_df)
```

#### 정답 (접기)

<details>
<summary>정답 보기</summary>

```python
import pandas as pd
import matplotlib.pyplot as plt

# 데이터 로드
df = pd.read_parquet('data/events.parquet')

# 퍼널 단계
funnel_steps = ['product_view', 'add_to_cart', 'checkout_start', 'purchase_complete']

# 각 단계별 고유 사용자 수
funnel_data = []
for step in funnel_steps:
    user_count = df[df['event_name'] == step]['user_id'].nunique()
    event_count = len(df[df['event_name'] == step])
    funnel_data.append({
        'step': step,
        'users': user_count,
        'events': event_count
    })

funnel_df = pd.DataFrame(funnel_data)

# 전환율 계산
funnel_df['overall_conversion'] = (funnel_df['users'] / funnel_df['users'].iloc[0] * 100).round(2)
funnel_df['step_conversion'] = (funnel_df['users'] / funnel_df['users'].shift(1) * 100).round(2)

print("=== 퍼널 분석 결과 ===")
print(funnel_df)

# 가장 큰 이탈 지점
funnel_df['drop_rate'] = 100 - funnel_df['step_conversion']
max_drop_idx = funnel_df['drop_rate'].idxmax()
if not pd.isna(funnel_df.loc[max_drop_idx, 'drop_rate']):
    print(f"\n가장 큰 이탈 지점: {funnel_df.loc[max_drop_idx, 'step']} ({funnel_df.loc[max_drop_idx, 'drop_rate']:.2f}% 이탈)")

# 시각화
plt.figure(figsize=(10, 6))
plt.barh(funnel_df['step'], funnel_df['users'], color='skyblue')
plt.xlabel('사용자 수')
plt.title('구매 퍼널 분석')
plt.tight_layout()
plt.savefig('output/funnel_analysis.png', dpi=300)
print("\n시각화 저장 완료: output/funnel_analysis.png")
```

**예상 결과:**
```
               step  users  events  overall_conversion  step_conversion  drop_rate
0     product_view    487    2456              100.00              NaN        NaN
1     add_to_cart    215     312               44.15            44.15      55.85
2   checkout_start    108     118               22.18            50.23      49.77
3 purchase_complete     65      65               13.35            60.19      39.81

가장 큰 이탈 지점: add_to_cart (55.85% 이탈)
```

**인사이트:**
- 상품 조회 후 장바구니 담기 단계에서 가장 큰 이탈 (55.85%)
- 개선 방안: 장바구니 담기 유도 CTA 개선, 할인 쿠폰 제공
</details>

---

### 4. 미션 2: 고가치 사용자 찾기 (RFM 분석)

#### 목표
RFM 분석으로 고가치 사용자를 찾고, 세그먼트별 특성을 파악하세요.

#### 힌트
```python
# 구매 이벤트만 추출
purchases = df[df['event_name'] == 'purchase_complete'].copy()

# RFM 계산
analysis_date = purchases['timestamp'].max()
rfm = purchases.groupby('user_id').agg({
    'timestamp': lambda x: (analysis_date - x.max()).days,  # Recency
    'event_name': 'count',  # Frequency
    'price': 'sum'  # Monetary
})
```

#### 정답 (접기)

<details>
<summary>정답 보기</summary>

```python
import pandas as pd

# 데이터 로드
df = pd.read_parquet('data/events.parquet')

# 구매 이벤트만 추출
purchases = df[df['event_name'] == 'purchase_complete'].copy()

print(f"총 구매 건수: {len(purchases)}")
print(f"구매 사용자 수: {purchases['user_id'].nunique()}")

# 분석 기준일
analysis_date = purchases['timestamp'].max()

# RFM 계산
rfm = purchases.groupby('user_id').agg({
    'timestamp': lambda x: (analysis_date - x.max()).days,  # Recency
    'event_name': 'count',  # Frequency
    'price': 'sum'  # Monetary
}).reset_index()

rfm.columns = ['user_id', 'recency', 'frequency', 'monetary']

# RFM 점수 (1-5점)
rfm['r_score'] = pd.qcut(rfm['recency'], 5, labels=[5, 4, 3, 2, 1], duplicates='drop')
rfm['f_score'] = pd.qcut(rfm['frequency'].rank(method='first'), 5, labels=[1, 2, 3, 4, 5], duplicates='drop')
rfm['m_score'] = pd.qcut(rfm['monetary'], 5, labels=[1, 2, 3, 4, 5], duplicates='drop')

# 총점
rfm['rfm_score'] = rfm['r_score'].astype(int) + rfm['f_score'].astype(int) + rfm['m_score'].astype(int)

# 세그먼트 분류
def rfm_segment(score):
    if score >= 12:
        return 'Champions'
    elif score >= 9:
        return 'Loyal Customers'
    elif score >= 6:
        return 'Potential'
    else:
        return 'At Risk'

rfm['segment'] = rfm['rfm_score'].apply(rfm_segment)

# 세그먼트별 통계
segment_stats = rfm.groupby('segment').agg({
    'user_id': 'count',
    'recency': 'mean',
    'frequency': 'mean',
    'monetary': 'mean'
}).round(2)

print("\n=== RFM 세그먼트 분석 ===")
print(segment_stats)

# 상위 10명 고가치 고객
top_customers = rfm.nlargest(10, 'monetary')[['user_id', 'recency', 'frequency', 'monetary', 'segment']]
print("\n=== 상위 10명 고가치 고객 ===")
print(top_customers)

# 결과 저장
rfm.to_csv('output/rfm_analysis.csv', index=False, encoding='utf-8-sig')
print("\n결과 저장 완료: output/rfm_analysis.csv")
```

**예상 결과:**
```
=== RFM 세그먼트 분석 ===
                  user_id  recency  frequency      monetary
segment
At Risk                15    18.53       1.00     156666.67
Champions              12     8.25       2.42     458333.33
Loyal Customers        18    12.11       1.67     298333.33
Potential              20    15.45       1.15     198500.00

=== 상위 10명 고가치 고객 ===
      user_id  recency  frequency   monetary        segment
user_0234       5          3      1550000     Champions
user_0089       7          3      1420000     Champions
...
```

**인사이트:**
- Champions 고객은 평균 2.42회 구매, 평균 458,333원 지출
- At Risk 고객은 18일 이상 미방문 → 리마케팅 캠페인 필요
</details>

---

### 5. 미션 3: 플랫폼별 성과 비교

#### 목표
iOS, Android, Web 플랫폼별로 전환율과 매출을 비교하세요.

#### 정답 (접기)

<details>
<summary>정답 보기</summary>

```python
import pandas as pd

df = pd.read_parquet('data/events.parquet')

# 플랫폼별 퍼널
platforms = ['iOS', 'Android', 'Web']
results = []

for platform in platforms:
    platform_df = df[df['platform'] == platform]

    views = platform_df[platform_df['event_name'] == 'product_view']['user_id'].nunique()
    purchases = platform_df[platform_df['event_name'] == 'purchase_complete']['user_id'].nunique()

    # 매출
    revenue = platform_df[platform_df['event_name'] == 'purchase_complete']['price'].sum()

    # 전환율
    conversion_rate = (purchases / views * 100) if views > 0 else 0

    results.append({
        'platform': platform,
        'views': views,
        'purchases': purchases,
        'revenue': revenue,
        'conversion_rate': round(conversion_rate, 2)
    })

result_df = pd.DataFrame(results)

print("=== 플랫폼별 성과 ===")
print(result_df)

# 시각화
import matplotlib.pyplot as plt

fig, axes = plt.subplots(1, 2, figsize=(14, 5))

# 전환율 비교
axes[0].bar(result_df['platform'], result_df['conversion_rate'], color=['#1f77b4', '#2ca02c', '#ff7f0e'])
axes[0].set_title('플랫폼별 전환율')
axes[0].set_ylabel('전환율 (%)')

# 매출 비교
axes[1].bar(result_df['platform'], result_df['revenue'] / 1000000, color=['#1f77b4', '#2ca02c', '#ff7f0e'])
axes[1].set_title('플랫폼별 매출')
axes[1].set_ylabel('매출 (백만원)')

plt.tight_layout()
plt.savefig('output/platform_comparison.png', dpi=300)
print("\n시각화 저장 완료: output/platform_comparison.png")
```

**인사이트:**
- iOS 사용자의 전환율이 가장 높음
- Web은 유입이 많지만 전환율이 낮음 → UX 개선 필요
</details>

---

### 6. 미션 4: 시간대별 구매 패턴 분석

#### 목표
어느 시간대에 구매가 가장 많이 발생하는지 분석하세요.

#### 정답 (접기)

<details>
<summary>정답 보기</summary>

```python
import pandas as pd
import matplotlib.pyplot as plt

df = pd.read_parquet('data/events.parquet')

# 구매 이벤트만 추출
purchases = df[df['event_name'] == 'purchase_complete'].copy()

# 시간대 추가
purchases['hour'] = purchases['timestamp'].dt.hour

# 시간대별 구매 건수
hourly_purchases = purchases.groupby('hour').size()

print("=== 시간대별 구매 건수 ===")
print(hourly_purchases)

# 시각화
plt.figure(figsize=(12, 6))
plt.plot(hourly_purchases.index, hourly_purchases.values, marker='o', linewidth=2, markersize=8)
plt.xlabel('시간대 (0-23시)')
plt.ylabel('구매 건수')
plt.title('시간대별 구매 패턴')
plt.grid(True, alpha=0.3)
plt.xticks(range(0, 24))
plt.tight_layout()
plt.savefig('output/hourly_purchases.png', dpi=300)
print("\n시각화 저장 완료: output/hourly_purchases.png")

# 피크 시간대
peak_hour = hourly_purchases.idxmax()
peak_count = hourly_purchases.max()
print(f"\n피크 시간대: {peak_hour}시 ({peak_count}건)")
```

**인사이트:**
- 저녁 8-10시에 구매가 집중됨
- 마케팅 캠페인은 저녁 시간대 타겟팅 추천
</details>

---

### 7. 미션 5: SQL로 데이터 분석 (MySQL)

#### 목표
생성한 데이터를 MySQL에 넣고 SQL로 분석하세요.

#### 단계

**1) 데이터 로드:**
```python
import pandas as pd
import mysql.connector

# 데이터 읽기
df = pd.read_csv('data/events.csv')

# MySQL 연결
conn = mysql.connector.connect(
    host='localhost',
    user='root',
    password='your_password',
    database='tracking_db'
)
cursor = conn.cursor()

# 테이블 생성 (부록 C-2 참고)
create_table_query = """
CREATE TABLE IF NOT EXISTS events (
    event_id VARCHAR(50) PRIMARY KEY,
    event_name VARCHAR(100) NOT NULL,
    user_id VARCHAR(100) NOT NULL,
    session_id VARCHAR(100),
    timestamp DATETIME NOT NULL,
    platform VARCHAR(50),
    device_type VARCHAR(50),
    product_id VARCHAR(50),
    product_name VARCHAR(255),
    price INT,
    category VARCHAR(100),
    payment_method VARCHAR(50),
    order_id VARCHAR(100),
    INDEX idx_event_name (event_name),
    INDEX idx_user_id (user_id),
    INDEX idx_timestamp (timestamp)
);
"""
cursor.execute(create_table_query)

# 데이터 삽입
for _, row in df.iterrows():
    insert_query = """
    INSERT INTO events (event_id, event_name, user_id, session_id, timestamp, platform, device_type,
                        product_id, product_name, price, category, payment_method, order_id)
    VALUES (%s, %s, %s, %s, %s, %s, %s, %s, %s, %s, %s, %s, %s)
    ON DUPLICATE KEY UPDATE event_id=event_id
    """
    cursor.execute(insert_query, (
        row['event_id'], row['event_name'], row['user_id'], row.get('session_id'),
        row['timestamp'], row.get('platform'), row.get('device_type'),
        row.get('product_id'), row.get('product_name'), row.get('price'),
        row.get('category'), row.get('payment_method'), row.get('order_id')
    ))

conn.commit()
print(f"{len(df)} 행 입력 완료")
cursor.close()
conn.close()
```

**2) SQL 퍼널 분석:**
```sql
-- 퍼널 전환율 계산
SELECT
    COUNT(DISTINCT CASE WHEN event_name = 'product_view' THEN user_id END) AS product_views,
    COUNT(DISTINCT CASE WHEN event_name = 'add_to_cart' THEN user_id END) AS add_to_cart,
    COUNT(DISTINCT CASE WHEN event_name = 'checkout_start' THEN user_id END) AS checkout_start,
    COUNT(DISTINCT CASE WHEN event_name = 'purchase_complete' THEN user_id END) AS purchases,
    ROUND(
        COUNT(DISTINCT CASE WHEN event_name = 'purchase_complete' THEN user_id END) /
        COUNT(DISTINCT CASE WHEN event_name = 'product_view' THEN user_id END) * 100,
        2
    ) AS conversion_rate
FROM events;
```

**3) 상품별 매출:**
```sql
-- 상품별 매출 순위
SELECT
    product_name,
    COUNT(*) AS purchase_count,
    SUM(price) AS total_revenue,
    AVG(price) AS avg_price
FROM events
WHERE event_name = 'purchase_complete'
GROUP BY product_name
ORDER BY total_revenue DESC
LIMIT 10;
```

---

### 8. 미션 6: BigQuery로 고급 분석

#### 목표
BigQuery에 데이터를 올리고 코호트 분석을 수행하세요.

#### 단계

**1) 데이터 업로드:**
```python
from google.cloud import bigquery
import pandas as pd

client = bigquery.Client(project='tracking-analytics')

# 데이터 읽기
df = pd.read_csv('data/events.csv')
df['timestamp'] = pd.to_datetime(df['timestamp'])

# BigQuery에 업로드
table_id = 'tracking-analytics.tracking_data.events'
job = client.load_table_from_dataframe(df, table_id)
job.result()

print(f"{len(df)} 행 업로드 완료")
```

**2) 코호트 리텐션 분석:**
```sql
-- 주별 코호트 리텐션
WITH user_cohorts AS (
  SELECT
    user_id,
    DATE_TRUNC(MIN(DATE(timestamp)), WEEK) AS cohort_week
  FROM `tracking-analytics.tracking_data.events`
  GROUP BY user_id
),
user_activity AS (
  SELECT
    uc.user_id,
    uc.cohort_week,
    DATE_TRUNC(DATE(e.timestamp), WEEK) AS activity_week
  FROM user_cohorts uc
  JOIN `tracking-analytics.tracking_data.events` e
    ON uc.user_id = e.user_id
  GROUP BY uc.user_id, uc.cohort_week, activity_week
)
SELECT
  cohort_week,
  DATE_DIFF(activity_week, cohort_week, WEEK) AS weeks_since_cohort,
  COUNT(DISTINCT user_id) AS active_users
FROM user_activity
GROUP BY cohort_week, weeks_since_cohort
ORDER BY cohort_week, weeks_since_cohort;
```

---

### 9. 최종 프로젝트: 경영진 보고서 작성

#### 목표
지금까지의 분석을 종합하여 경영진에게 제출할 보고서를 작성하세요.

#### 보고서 구조

```markdown
# ShopKorea 1월 데이터 분석 보고서

## 1. Executive Summary
- DAU: XXX명
- 총 매출: XXX원
- 전환율: X.X%

## 2. 주요 발견사항
### 2.1 퍼널 분석
- 가장 큰 이탈 지점: 장바구니 담기 (55.85% 이탈)
- 개선 필요

### 2.2 고가치 고객
- Champions 세그먼트: 12명
- 평균 구매액: 458,333원

### 2.3 플랫폼별 성과
- iOS: 전환율 가장 높음 (XX%)
- Web: 개선 필요

## 3. 액션 아이템
1. 장바구니 담기 유도 CTA 개선
2. iOS 사용자 대상 프리미엄 상품 마케팅
3. Web UX 개선 프로젝트 시작
4. 저녁 시간대 타겟 푸시 알림

## 4. 다음 달 목표
- 전환율 15% 달성
- Champions 고객 20명 확보
```

---

### 10. 추가 도전 과제

#### 도전 1: 머신러닝으로 구매 예측

```python
from sklearn.ensemble import RandomForestClassifier
from sklearn.model_selection import train_test_split
from sklearn.metrics import classification_report

# 사용자별 특성 추출
user_features = df.groupby('user_id').agg({
    'event_name': 'count',  # 총 이벤트 수
    'product_view': lambda x: (x == 'product_view').sum(),
    'add_to_cart': lambda x: (x == 'add_to_cart').sum(),
    'platform': lambda x: x.mode()[0] if len(x) > 0 else 'Unknown'
})

# 구매 여부 (타겟)
user_features['purchased'] = df[df['event_name'] == 'purchase_complete']['user_id'].unique()
user_features['purchased'] = user_features.index.isin(user_features['purchased']).astype(int)

# 모델 학습
X = user_features.drop('purchased', axis=1)
y = user_features['purchased']

X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=42)

model = RandomForestClassifier(n_estimators=100, random_state=42)
model.fit(X_train, y_train)

# 평가
y_pred = model.predict(X_test)
print(classification_report(y_test, y_pred))
```

#### 도전 2: 실시간 대시보드 구축

Streamlit으로 실시간 대시보드 만들기:

```python
import streamlit as st
import pandas as pd
import plotly.express as px

st.title("ShopKorea 실시간 대시보드")

# 데이터 로드
@st.cache_data
def load_data():
    return pd.read_parquet('data/events.parquet')

df = load_data()

# KPI
col1, col2, col3 = st.columns(3)
col1.metric("DAU", df['user_id'].nunique())
col2.metric("총 이벤트", len(df))
col3.metric("구매 건수", len(df[df['event_name'] == 'purchase_complete']))

# 일별 트렌드
daily = df.groupby(df['timestamp'].dt.date).size()
fig = px.line(x=daily.index, y=daily.values, title="일별 이벤트 추이")
st.plotly_chart(fig)
```

**실행:**
```bash
streamlit run dashboard.py
```

---