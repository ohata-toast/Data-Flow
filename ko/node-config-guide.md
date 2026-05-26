## Data & Analytics > DataFlow > 노드 설정 가이드

* 노드 유형은 손쉽게 플로우를 작성할 수 있게 사전 정의된 템플릿입니다.
* 노드 유형의 종류는 Source, Filter, Branch, Sink입니다.
* Source, Sink 노드 유형은 테스트를 수행해 엔드포인트 정보가 유효한지 확인하기를 권장합니다.
* 접근 제어가 설정된 데이터 소스 연결 시에는 DataFlow IP 고정 기능을 사용해야 합니다.
    * DataFlow IP 고정 기능을 사용하려면 **고객지원 > 문의하기**로 문의하세요.

### Object Storage 연결 시 주의점
리전 또는 프로젝트가 서로 다른 Object Storage이지만 버킷 이름은 동일한 경우, 하나의 플로우에서 함께 사용할 수 없습니다.

!!! tip "불가능한 연결 설정 예제"
    * 예제 1
        * 첫 번째 연결 대상 Object Storage 정보
            * 리전: KR1
            * 버킷 이름: Data
            * 프로젝트: TEST
        * 두 번째 연결 대상 Object Storage 정보
            * 리전: JP1
            * 버킷 이름: Data
            * 프로젝트: TEST
        * 리전이 다르므로 두 버킷은 서로 다른 버킷이지만 DataFlow의 플로우에서는 함께 사용할 수 없음
    * 예제 2
        * 첫 번째 연결 대상 Object Storage 정보
            * 리전: KR1
            * 버킷 이름: Data
            * 프로젝트: TEST_1
        * 두 번째 연결 대상 Object Storage 정보
            * 리전: KR1
            * 버킷 이름: Data
            * 프로젝트: TEST_2
        * 프로젝트가 다르므로 두 버킷은 서로 다른 버킷이지만 DataFlow의 플로우에서는 함께 사용할 수 없음


## Domain Specific Language(DSL) 정의

플로우 실행에 필요한 DSL 정의입니다.

### Variable

* `{{ executionTime }}`
    * 플로우 실행 시간
* 시간 단위 ( unit )
    * 분 - `{{ MINUTE }}`
    * 시 - `{{ HOUR }}`
    * 일 - `{{ DAY }}`
    * 월 - `{{ MONTH }}`
    * 년 - `{{ YEAR }}`

### Filter

* `{{ time | startOf: unit }}`
    * 주어진 시간으로부터 `unit`으로 정의된 시간대의 시작 시간을 반환합니다.
    * [주의] 한국 시간을 기준으로 계산합니다.
    * 예: \{\{ executionTime \| startOf: MINUTE \}\}
    * 예: \{\{ "2022\-11\-04T13:31:28Z" \| startOf: MINUTE \}\}
        * → 2022-11-04T13:31:00Z
    * 예: \{\{ "2022\-11\-04T13:31:28Z" \| startOf: HOUR \}\}
        * → 2022-11-04T13:00:00Z
    * 예: \{\{ "2022\-11\-04T13:31:28Z" \| startOf: DAY \}\}
        * → 2022-11-04T00:00:00Z
    * 예: \{\{ "2022\-11\-04T13:31:28Z" \| startOf: MONTH \}\}
        * → 2022-11-01T00:00:00Z
    * 예: \{\{ "2022\-11\-04T13:31:28Z" \| startOf: YEAR \}\}
        * → 2022-01-01T00:00:00Z
* `{{ time | endOf: unit }}`
    * 주어진 시간으로부터 `unit`으로 정의된 시간대의 마지막 시간을 반환합니다.
    * [주의] 한국 시간을 기준으로 계산합니다.
    * 예: \{\{ executionTime \| endOf: MINUTE \}\}
    * 예: \{\{ "2022\-11\-04T13:31:28Z" \| endOf: MINUTE \}\}
        * → 2022-11-04T13:31:59.999999999Z
    * 예: \{\{ "2022\-11\-04T13:31:28Z" \| endOf: HOUR \}\}
        * → 2022-11-04T13:59:59.999999999Z
    * 예: \{\{ "2022\-11\-04T13:31:28Z" \| endOf: DAY \}\}
        * → 2022-11-04T23:59:59.999999999Z
    * 예: \{\{ "2022\-11\-04T13:31:28Z" \| endOf: MONTH \}\}
        * → 2022-11-30T23:59:59.999999999Z
    * 예: \{\{ "2022\-11\-04T13:31:28Z" \| endOf: YEAR \}\}
        * → 2022-12-31T23:59:59.999999999Z
* `{{ time | subTime: delta, unit }}`
    * 주어진 시간으로부터 `unit`으로 정의된 시간대의 `delta`만큼 뺀 시간을 반환합니다.
    * 예: \{\{ executionTime \| subTime: 10, MINUTE \}\}
    * 예: \{\{ "2022\-11\-04T13:31:28Z" \| subTime: 10, MINUTE \}\}
        * → 2022-11-04T13:21:28Z
* `{{ time | addTime: delta, unit }}`
    * 주어진 시간으로부터 `unit`으로 정의된 시간대의 `delta`만큼 더한 시간을 반환합니다.
    * 예: \{\{ executionTime \| addTime: 10, MINUTE \}\}
    * 예: \{\{ "2022\-11\-04T13:31:28Z" \| addTime: 10, MINUTE \}\}
        * → 2022-11-04T13:41:28Z
* `{{ time | format: formatStr }}`
    * 주어진 시간을 `formatStr` 형태로 반환합니다.
        * iso8601
        * yyyy
        * yy
        * MM
        * M
        * dd
        * d
        * mm
        * m
        * ss
        * s
    * 예: \{\{ executionTime \| format: 'yyyy' \}\}
    * 예: \{\{ "2022\-11\-04T13:31:28Z" \| format: 'yyyy' \}\}
        * → 2022
* nested filter 예제
    * 플로우 실행이 시작된 날의 03시의 DSL 표현
        * → \{\{ executionTime \| startOf: DAY \| addTime: 3\, HOUR \}\}

## 자료형별 입력 방법
### string
문자열을 입력합니다.

### number
* 0 이상의 숫자를 입력합니다.
* 입력창 오른쪽의 화살표를 이용해 값을 1씩 조절할 수 있습니다.

### boolean
드롭다운 메뉴에서 `TRUE` 또는 `FALSE`를 선택합니다.

### enum
드롭다운 메뉴에서 항목을 선택합니다.

### array of strings
* 배열에 들어갈 문자열을 하나씩 입력합니다.
* 문자열 입력 후 `+` 버튼을 클릭하면 배열에 문자열이 삽입됩니다.
* 예: `["message" , "yyyy-MM-dd HH:mm:ssZ", "ISO8601"]`를 입력하려면 `message`, `yyyy-MM-dd HH:mm:ssZ`, `ISO8601`의 순서로 배열에 문자열을 삽입합니다.

### hash
JSON 형식의 문자열을 입력합니다.

## 스키마

### 개요

* Source 노드에서 출력 스키마(필드명과 데이터 타입)를 정의하면, 정의된 필드만 선택적으로 데이터를 읽습니다.
* 정의된 스키마는 DAG 그래프를 따라 하위 노드로 자동 전파됩니다.
* Filter 노드에서 필드를 입력할 때 스키마에 정의된 필드를 드롭다운으로 선택할 수 있습니다.
* 스키마를 정의하지 않으면 기존과 동일하게 모든 필드를 읽어 옵니다.

### 지원 데이터 타입

| 데이터 타입 | 설명 |
|---|---|
| String | 문자열 |
| Integer | 32비트 정수 |
| Long | 64비트 정수 |
| Float | 32비트 부동 소수점 |
| Double | 64비트 부동 소수점 |
| Boolean | 참/거짓 |
| Timestamp | 날짜와 시간 |
| Array | 배열 |

### 스키마 정의

* Source 노드의 **Codec** 탭에서 스키마를 정의할 수 있습니다.
* 다음 코덱을 사용 시 Source 노드에서 스키마를 정의할 수 있습니다.
    * JSON
* PLAIN 코덱은 데이터가 `message` 필드에 고정 매핑되므로 해당 필드만 정의 가능합니다.
* 필드명과 데이터 타입을 추가하여 스키마를 구성합니다.
* 스키마를 정의하면 플로우 실행 시 정의된 필드만 선택적으로 파싱합니다.

### 스키마 전파 및 변환

* Source 노드에서 정의한 스키마는 연결된 하위 노드로 자동 전파됩니다.
* Filter 노드의 속성에 따라 스키마가 자동으로 변환됩니다.

### 스키마 기반 필드 선택

* 상위 모든 Source 노드에서 스키마가 정의되어 있으면 필드 입력 시 드롭다운으로 필드 목록이 표시됩니다.
* 스키마가 정의되어 있지 않으면 기존과 동일하게 텍스트로 직접 입력합니다.

## Source

플로우로 데이터를 인입할 엔드포인트를 정의하는 노드 유형입니다.

### 실행 모드

* Source 노드에는 실행 모드가 존재하며, BATCH 모드와 STREAMING 모드로 나뉩니다.
    * STREAMING 모드: 플로우를 종료하지 않고 실시간으로 데이터를 처리합니다.
    * BATCH 모드: 정해진 데이터를 처리한 후 플로우를 종료합니다.
* Source 노드별로 지원하는 실행 모드가 다릅니다.

### Source 노드의 공통 설정

| 속성명 | 기본값 | 자료형 | 설명 | 비고 |
| --- | --- | --- | --- | --- |
| 아이디 | - | string | 노드의 아이디를 설정합니다.<br/>이 속성에 정의된 값으로 차트보드에 노드 이름을 표기합니다. |  |

## Source > (NHN Cloud) Log & Crash Search

### 노드 설명

* (NHN Cloud) Log & Crash Search 노드는 Log & Crash Search로부터 로그를 읽어오는 노드입니다.
* 노드에 로그 조회 시작 시간을 설정할 수 있습니다. 설정하지 않으면 플로우를 시작하는 시점부터 로그를 읽어 옵니다.
* 노드에 종료 시간을 입력하지 않으면 스트리밍 형식으로 로그를 읽어 옵니다. 종료 시간을 입력하면 종료 시간까지의 로그를 읽어 오고 플로우는 종료됩니다.
* 현재 세션 로그와 크래시 로그는 지원하지 않습니다.
* Log & Crash Search의 로그 검색 API의 토큰에 영향을 받습니다.
    * 토큰이 부족할 경우 Log & Crash Search 서비스에 문의하세요.

### 실행 모드
* STREAMING: `조회 시작 시간` 이후의 데이터를 계속해서 처리합니다.
* BATCH: `조회 시작 시간`, `조회 종료 시간` 사이에 해당하는 데이터를 모두 처리하고 플로우를 종료합니다.


### 속성 설명

| 속성명       | 기본값                 | 자료형    | 설명                                                                                                                                                     | 비고 |
|-----------|---------------------|--------|--------------------------------------------------------------------------------------------------------------------------------------------------------|----|
| Appkey    | -                   | string | Log & Crash Search의 앱키를 입력합니다.                                                                                                                         |    |
| SecretKey | -                   | string | Log & Crash Search의 시크릿키를 입력합니다.                                                                                                                       |    |
| 조회 시작 시간  | {{executionTime}} | string | 로그 조회의 시작 시간을 입력합니다. 오프셋이 포함된 ISO 8601 형식 또는 [DSL](#domain-specific-languagedsl) 형식으로 입력해야 합니다. <br/>예: 2025-07-23T11:23:00+09:00, {{ executionTime }} |    |
| 조회 종료 시간  | -                   | string | 로그 조회의 종료 시간을 입력합니다. 오프셋이 포함된 ISO 8601 형식 또는 [DSL](#domain-specific-languagedsl) 형식으로 입력해야 합니다. <br/>예: 2025-07-23T11:23:00+09:00, {{ executionTime }} |    |
| 검색 쿼리     | *                   | string | Log & Crash Search 조회 요청 시 사용할 검색 쿼리를 입력합니다. 자세한 쿼리 작성 방법은 Log & Crash Search 서비스의 'Lucene 쿼리 가이드'를 참고하세요.                                             |    |

### 코덱별 메시지 인입

* Log & Crash Search는 기본적으로 JSON 형식의 데이터를 다룹니다.
* Log & Crash Search 로그의 각 필드를 활용하고 싶다면 JSON 코덱을 사용하는 것이 좋습니다.

지원 코덱
* [JSON 코덱](./codec-config-guide.md#json) - JSON 형식 데이터 파싱

## Source > (NHN Cloud) CloudTrail

### 노드 설명

* (NHN Cloud) CloudTrail은 CloudTrail로부터 데이터를 읽어 오는 노드입니다.
* 노드에 데이터 조회 시작 시간을 설정할 수 있습니다. 설정하지 않으면 플로우를 시작하는 시점부터 데이터를 읽어 옵니다.
* 노드에 종료 시간을 입력하지 않으면 스트리밍 형식으로 데이터를 읽어 옵니다. 종료 시간을 입력하면 종료 시간까지의 데이터를 읽어 오고 플로우는 종료됩니다.

### 실행 모드

* STREAMING: `조회 시작 시간` 이후의 데이터를 계속해서 처리합니다.
* BATCH: `조회 시작 시간`, `조회 종료 시간` 사이에 해당하는 데이터를 모두 처리하고 플로우를 종료합니다.

### 속성 설명

| 속성명                | 기본값                 | 자료형    | 설명                                                                                                                                                      | 비고 |
|--------------------|---------------------|--------|---------------------------------------------------------------------------------------------------------------------------------------------------------|----|
| Appkey             | -                   | string | CloudTrail의 앱키를 입력합니다.                                                                                                                                  |    |
| User Access Key ID | -                   | string | 사용자 계정의 User Access Key ID를 입력합니다.                                                                                                                      |    |
| Secret Access Key  | -                   | string | 사용자 계정의 User Secret Key를 입력합니다.                                                                                                                         |    |
| 조회 시작 시간           | {{executionTime}} | string | 데이터 조회의 시작 시간을 입력합니다. 오프셋이 포함된 ISO 8601 형식 또는 [DSL](#domain-specific-languagedsl) 형식으로 입력해야 합니다. <br/>예: 2025-07-23T11:23:00+09:00, {{ executionTime }} |    |
| 조회 종료 시간           | -                   | string | 데이터 조회의 종료 시간을 입력합니다. 오프셋이 포함된 ISO 8601 형식 또는 [DSL](#domain-specific-languagedsl) 형식으로 입력해야 합니다. <br/>예: 2025-07-23T11:23:00+09:00, {{ executionTime }} |    |
| 이벤트 타입             | *                   | string | 조회할 이벤트 ID를 입력합니다.                                                                                                                                      |    |

### 코덱별 메시지 인입

* CloudTrail은 기본적으로 JSON 형식의 데이터를 다룹니다.
* CloudTrail 데이터의 각 필드를 활용하고 싶다면 JSON 코덱을 사용하는 것이 좋습니다.

지원 코덱
* [JSON 코덱](./codec-config-guide.md#json) - JSON 형식 데이터 파싱

## Source > (NHN Cloud) Object Storage

### 노드 설명

* NHN Cloud의 Object Storage로부터 데이터를 입력받는 노드입니다.
* 오브젝트 생성 시간을 기준으로 가장 빨리 생성된 오브젝트부터 데이터를 읽습니다.

### 실행 모드
* STREAMING: `리스트 갱신 주기`마다 오브젝트 리스트를 갱신하며, 새롭게 추가된 오브젝트들을 읽어 데이터를 처리합니다.
* BATCH: 플로우 시작 시점에 오브젝트 리스트를 한 번 불러온 뒤, 오브젝트들을 읽어 데이터를 처리하고 플로우를 종료합니다.

### 속성 설명

| 속성명 | 기본값     | 자료형 | 설명 | 비고 |
| --- |---------| --- | --- | --- |
| 버킷 | -       | string | 데이터를 읽을 버킷 이름을 입력합니다. |  |
| 리전 | -       | string | 저장소에 설정된 리전 정보를 입력합니다. |  |
| 비밀 키 | -       | string | S3가 발급한 자격 증명 비밀 키를 입력합니다. |  |
| 액세스 키 | -       | string | S3가 발급한 자격 증명 액세스 키를 입력합니다. |  |
| 리스트 갱신 주기 | 60    | number | 버킷에 포함된 오브젝트 리스트 갱신 주기를 입력합니다. |  |
| Prefix | -       | string | 읽어 올 오브젝트의 접두사를 입력합니다. |  |
| 제외할 키 패턴 | -       | string | 읽지 않을 오브젝트의 패턴을 입력합니다. |  |

### 코덱별 메시지 인입

지원 코덱
* [PLAIN 코덱](./codec-config-guide.md#plain) - 원본 데이터 문자열 저장
* [JSON 코덱](./codec-config-guide.md#json) - JSON 형식 데이터 파싱

## Source > (NHN Cloud) Data Lake Storage

### 노드 설명
* NHN Cloud의 Data Lake Storage로부터 데이터를 입력받는 노드입니다.

### 실행 모드
* STREAMING: `리스트 갱신 주기`마다 오브젝트 리스트를 갱신하며, 새롭게 추가된 오브젝트들을 읽어 데이터를 처리합니다.
* BATCH: 플로우 시작 시점에 오브젝트 리스트를 한 번 불러온 뒤, 오브젝트들을 읽어 데이터를 처리하고 플로우를 종료합니다.

### 속성 설명
| 속성명 | 기본값     | 자료형 | 설명 | 비고 |
| --- |---------| --- | --- | --- |
| 버킷 | -       | string | 데이터를 읽을 버킷 이름을 입력합니다. |  |
| 리전 | -       | string | 저장소에 설정된 리전 정보를 입력합니다. |  |
| 비밀 키 | -       | string | S3가 발급한 자격 증명 비밀 키를 입력합니다. |  |
| 액세스 키 | -       | string | S3가 발급한 자격 증명 액세스 키를 입력합니다. |  |
| 리스트 갱신 주기 | 60    | number | 버킷에 포함된 오브젝트 리스트 갱신 주기를 입력합니다. |  |
| Prefix | -       | string | 읽어 올 오브젝트의 접두사를 입력합니다. |  |
| 제외할 키 패턴 | -       | string | 읽지 않을 오브젝트의 패턴을 입력합니다. |  |

### 코덱별 메시지 인입
지원 코덱
* [PLAIN 코덱](./codec-config-guide.md#plain) - 원본 데이터 문자열 저장
* [JSON 코덱](./codec-config-guide.md#json) - JSON 형식 데이터 파싱

## Source > (Amazon) S3

### 노드 설명

* S3로부터 데이터를 입력받는 노드입니다.
* 오브젝트 생성 시간을 기준으로 가장 빨리 생성된 오브젝트부터 데이터를 읽습니다.

### 실행 모드
* STREAMING: `리스트 갱신 주기`마다 오브젝트 리스트를 갱신하며, 새롭게 추가된 오브젝트들을 읽어 데이터를 처리합니다.
* BATCH: 플로우 시작 시점에 오브젝트 리스트를 한 번 갱신한 뒤, 오브젝트들을 읽어 데이터를 처리하고 플로우를 종료합니다.

### 속성 설명

| 속성명           | 기본값                            | 자료형     | 설명                                                                                                   | 비고                                                                                                                                                                                                           |
|---------------|--------------------------------|---------|------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 엔드포인트         | -                              | string  | S3 저장소 엔드포인트를 입력합니다.                                                                                 | HTTP, HTTPS URL 형태만 입력 가능합니다.                                                                                                                                                                                |
| 버킷            | -                              | string  | 데이터를 읽을 버킷 이름을 입력합니다.                                                                                |                                                                                                                                                                                                              |
| 리전            | - | string  | 저장소에 설정된 리전 정보를 입력합니다.                                                                               |                                                                                                                                                                                                              |
| 비밀 키          | -                              | string  | S3가 발급한 자격 증명 비밀 키를 입력합니다.                                                                           |                                                                                                                                                                                                              |
| 액세스 키         | -                              | string  | S3가 발급한 자격 증명 액세스 키를 입력합니다.                                                                          |                                                                                                                                                                                                              |
| 리스트 갱신 주기     | 60                           | number  | 버킷에 포함된 오브젝트 리스트 갱신 주기를 입력합니다.                                                                       |                                                                                                                                                                                                              |
| Prefix        | -      | string  | 읽어 올 오브젝트의 접두사를 입력합니다.                                                                               |                                                                                                                                                                                                              |
| 제외할 키 패턴      | -      | string  | 읽지 않을 오브젝트의 패턴을 입력합니다.                                                                               |                                                                                                                                                                                                              |
| 경로 방식 요청      | false                        | boolean | 경로 방식 요청을 사용할지 여부를 결정합니다.                                                                            |                                                                                                                                                                                                              |

!!! danger "주의"
    * (Amazon) S3 노드를 이용하여 NHN Cloud Object Storage에 연결할 경우 **경로 방식 요청**을 `true`로 설정해야 합니다.


### 코덱별 메시지 인입

지원 코덱
* [PLAIN 코덱](./codec-config-guide.md#plain) - 원본 데이터 문자열 저장
* [JSON 코덱](./codec-config-guide.md#json) - JSON 형식 데이터 파싱

## Source > (NHN Cloud) EasyQueue

### 노드 설명
NHN Cloud의 EasyQueue에서 데이터를 수신하는 노드입니다.

### 실행 모드
STREAMING: 큐에 새로운 메시지가 도착할 때마다 데이터를 처리합니다.

### 속성 설명
| 속성명 | 기본값 | 자료형 | 설명 | 비고 |
| --- | --- | --- | --- | --- |
| 앱키 | - | string | EasyQueue의 앱키를 입력합니다. |  |
| User Access Key ID | - | string | 사용자 계정의 User Access Key ID를 입력합니다. |  |
| Secret Access Key | - | string | 사용자 계정의 User Secret Key를 입력합니다. |  |
| 브로커 서버 목록 | - | string | Kafka 브로커 서버를 입력합니다. 서버가 여러 대일 경우 콤마(`,`)로 구분합니다. | [Kafka 공식 문서](https://kafka.apache.org/39/configuration/consumer-configs/)의 `bootstrap.servers` 속성 참고 <br/>예: 10.100.1.1:9092,10.100.1.2:9092 |
| 컨슈머 그룹 아이디 | dataflow | string | Kafka Consumer Group을 식별하는 ID를 입력합니다. | [Kafka 공식 문서](https://kafka.apache.org/39/configuration/consumer-configs/)의 `group.id` 속성 참고 |
| 토픽 목록 | - | array of strings | 메시지를 수신할 Kafka 토픽 목록을 입력합니다. |  |
| 토픽 패턴 | - | string | 메시지를 수신할 Kafka 토픽 패턴을 입력합니다. | 예: `*-messages` |
| 내부 토픽 제외 여부 | true | boolean | __consumer_offsets와 같은 내부 토픽을 제외합니다. | [Kafka 공식 문서](https://kafka.apache.org/39/configuration/consumer-configs/)의 `exclude.internal.topics` 속성 참고 <br/>수신 대상에서 `__consumer_offsets`와 같은 내부 토픽을 제외합니다. |
| 클라이언트 아이디 | dataflow | string | Kafka Consumer를 식별하는 ID를 입력합니다. | [Kafka 공식 문서](https://kafka.apache.org/39/configuration/consumer-configs/)의 `client.id` 속성 참고 |
| 격리 수준 | read_committed | enum | 컨슈머가 트랜잭션이 커밋되지 않은 메시지까지 읽을지, 커밋된 메시지만 읽을지를 결정합니다. | [Kafka 공식 문서](https://kafka.apache.org/39/configuration/consumer-configs/)의 `isolation.level` 속성 참고<br/>read_uncommitted: 모든 메시지를 오프셋 순서대로 읽습니다.<br/>read_committed: 커밋된 트랜잭션의 메시지만 읽습니다. |
| 파티션 할당 정책 | ["RANGE", "COOPERATIVE_STICKY"] | array of strings | Kafka에서 메시지 수신 시 컨슈머 그룹에 어떻게 파티션을 할당할지 결정합니다. | [Kafka 공식 문서](https://kafka.apache.org/39/configuration/consumer-configs/)의 `partition.assignment.strategy` 속성 참고 <br/>org.apache.kafka.clients.consumer.RangeAssignor<br/>org.apache.kafka.clients.consumer.RoundRobinAssignor<br/>org.apache.kafka.clients.consumer.StickyAssignor<br/>org.apache.kafka.clients.consumer.CooperativeStickyAssignor |
| 오프셋 설정 | latest | enum | 컨슈머 그룹의 오프셋을 설정하는 기준을 입력합니다. | [Kafka 공식 문서](https://kafka.apache.org/39/configuration/consumer-configs/)의 `auto.offset.reset` 속성 참고 <br/>아래 설정 모두 컨슈머 그룹이 이미 존재하는 경우 기존 오프셋을 유지합니다.<br/>none: 컨슈머 그룹이 없으면 오류를 반환합니다.<br/>earliest: 컨슈머 그룹이 없으면 파티션의 가장 오래된 오프셋으로 초기화합니다.<br/>latest: 컨슈머 그룹이 없으면 파티션의 가장 최근 오프셋으로 초기화합니다. |
| 키 역직렬화 유형 | STRING | enum | 수신하는 메시지의 키의 타입을 입력합니다. | [Kafka 공식 문서](https://kafka.apache.org/39/configuration/consumer-configs/)의 `key.deserializer` 속성 참고 |
| 메타데이터 생성 여부 | false | boolean | 속성값이 true일 경우 메시지에 대한 메타데이터 필드를 생성합니다. 메타데이터는 `kafka_metadata` 필드에 생성됩니다. | 생성되는 필드는 다음과 같습니다.<br/>topic: 메시지를 수신한 토픽<br/>groupId: 메시지를 수신하는 데 사용한 컨슈머 그룹 아이디<br/>partition: 메시지를 수신한 토픽의 파티션 번호<br/>offset: 메시지를 수신한 파티션의 오프셋<br/>key: 메시지 키 |
| Fetch 최소 크기 | 1 | number | 한 번의 fetch 요청으로 가져올 데이터의 최소 크기(byte)를 입력합니다. | [Kafka 공식 문서](https://kafka.apache.org/39/configuration/consumer-configs/)의 `fetch.min.bytes` 속성 참고 |
| 전송 버퍼 크기 | 131072 | number | 데이터를 전송하는 데 사용하는 TCP send 버퍼의 크기(byte)를 입력합니다. | [Kafka 공식 문서](https://kafka.apache.org/39/configuration/consumer-configs/)의 `send.buffer.bytes` 속성 참고 |
| 재시도 요청 주기 | 100 | number | 전송 요청이 실패했을 때 재시도할 주기(ms)를 입력합니다. | [Kafka 공식 문서](https://kafka.apache.org/39/configuration/consumer-configs/)의 `retry.backoff.ms` 속성 참고 |
| 순환 중복 검사 | true | boolean | 메시지의 CRC를 검사합니다. | [Kafka 공식 문서](https://kafka.apache.org/39/configuration/consumer-configs/)의 `check.crcs` 속성 참고 |
| 서버 재연결 주기 | 50 | number | 브로커 서버에 연결이 실패했을 때 재시도할 주기(ms)를 입력합니다. | [Kafka 공식 문서](https://kafka.apache.org/39/configuration/consumer-configs/)의 `reconnect.backoff.ms` 속성 참고 |
| 파티션 당 Fetch 최대 크기 | 1048576 | number | 파티션 당 한 번의 fetch 요청으로 가져올 최대 크기(byte)를 입력합니다. | [Kafka 공식 문서](https://kafka.apache.org/39/configuration/consumer-configs/)의 `max.partition.fetch.bytes` 속성 참고 |
| 서버 요청 타임아웃 | 30000 | number | 전송 요청에 대한 타임아웃(ms)을 입력합니다. | [Kafka 공식 문서](https://kafka.apache.org/39/configuration/consumer-configs/)의 `request.timeout.ms` 속성 참고 |
| TCP 수신 버퍼 크기 | 65536 | number | 데이터를 읽는 데 사용하는 TCP receive 버퍼의 크기(byte)를 입력합니다. | [Kafka 공식 문서](https://kafka.apache.org/39/configuration/consumer-configs/)의 `receive.buffer.bytes` 속성 참고 |
| 세션 타임아웃 | 45000 | number | 컨슈머의 세션 타임아웃(ms)을 입력합니다.<br/>컨슈머가 해당 시간 안에 heartbeat를 보내지 못할 경우 컨슈머 그룹에서 제외합니다. | [Kafka 공식 문서](https://kafka.apache.org/39/configuration/consumer-configs/)의 `session.timeout.ms` 속성 참고 |
| 최대 poll 메시지 개수 | 500 | number | 한 번의 poll 요청으로 가져올 최대 메시지 개수를 입력합니다. | [Kafka 공식 문서](https://kafka.apache.org/39/configuration/consumer-configs/)의 `max.poll.records` 속성 참고 |
| 최대 poll 주기 | 300000 | number | poll 요청 간 최대 주기(ms)를 입력합니다. | [Kafka 공식 문서](https://kafka.apache.org/39/configuration/consumer-configs/)의 `max.poll.interval.ms` 속성 참고 |
| Fetch 최대 크기 | 52428800 | number | 한 번의 fetch 요청으로 가져올 최대 크기(byte)를 입력합니다. | [Kafka 공식 문서](https://kafka.apache.org/39/configuration/consumer-configs/)의 `fetch.max.bytes` 속성 참고 |
| Fetch 최대 대기 시간 | 500 | number | `Fetch 최소 크기` 설정 만큼의 데이터가 모이지 않은 경우 fetch 요청을 보낼 대기 시간(ms)을 입력합니다. | [Kafka 공식 문서](https://kafka.apache.org/39/configuration/consumer-configs/)의 `fetch.max.wait.ms` 속성 참고 |
| 컨슈머 헬스체크 주기 | 3000 | number | 컨슈머가 heartbeat를 보내는 주기(ms)를 입력합니다. | [Kafka 공식 문서](https://kafka.apache.org/39/configuration/consumer-configs/)의 `heartbeat.interval.ms` 속성 참고 |
| 메타데이터 갱신 주기 | 300000 | number | 파티션, 브로커 서버 상태 등을 갱신할 주기(ms)를 입력합니다. | [Kafka 공식 문서](https://kafka.apache.org/39/configuration/producer-configs/)의 `metadata.max.age.ms` 속성 참고 |
| IDLE 타임아웃 | 540000 | number | 데이터 전송이 없는 커넥션을 닫을 대기 시간(ms)을 입력합니다. | [Kafka 공식 문서](https://kafka.apache.org/39/configuration/consumer-configs/)의 `connections.max.idle.ms` 속성 참고 |
| 추가 설정 | - | hash | Kafka 연결에 사용할 추가 Consumer 설정을 입력합니다. | [Kafka 공식 문서](https://kafka.apache.org/39/configuration/consumer-configs/) 참고 |

### 코덱별 메시지 인입
지원 코덱
* [PLAIN 코덱](./codec-config-guide.md#plain) - 원본 데이터 문자열 저장
* [JSON 코덱](./codec-config-guide.md#json) - JSON 형식 데이터 파싱

## Source > (Apache) Kafka

### 노드 설명

Kafka에서 데이터를 수신하는 노드입니다.

### 실행 모드
STREAMING: 토픽에 새로운 메시지가 도착할 때마다 데이터를 처리합니다.

!!! danger "주의"
    * Kafka 노드는 BATCH 모드를 지원하지 않습니다.

### 속성 설명

| 속성명               | 기본값                               | 자료형              | 설명                                                                              | 비고                                                                                                                                                                                                                                                                                                                                                   |
|-------------------|-----------------------------------|------------------|---------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 브로커 서버 목록         | -                                 | string           | Kafka 브로커 서버를 입력합니다. 서버가 여러 대일 경우 콤마(`,`)로 구분합니다.                               | [Kafka 공식 문서](https://kafka.apache.org/39/configuration/consumer-configs/)의 `bootstrap.servers` 속성 참고 <br/>예: 10.100.1.1:9092,10.100.1.2:9092                                                                                                                                                                                                        |
| 컨슈머 그룹 아이디        | dataflow                        | string           | Kafka Consumer Group을 식별하는 ID를 입력합니다.                                           | [Kafka 공식 문서](https://kafka.apache.org/39/configuration/consumer-configs/)의 `group.id` 속성 참고                                                                                                                                                                                                                                                         |
| 토픽 목록             | -                                 | array of strings | 메시지를 수신할 Kafka 토픽 목록을 입력합니다.                                                    |                                                                                                                                                                                                                                                                                                                                                      |
| 토픽 패턴             | -                                 | string           | 메시지를 수신할 Kafka 토픽 패턴을 입력합니다.                                                    | 예: `*-messages`                                                                                                                                                                                                                                                                                                                                      |
| 내부 토픽 제외 여부       | true                            | boolean          | __consumer_offsets와 같은 내부 토픽을 제외합니다.                                            | [Kafka 공식 문서](https://kafka.apache.org/39/configuration/consumer-configs/)의 `exclude.internal.topics` 속성 참고 <br/>수신 대상에서 `__consumer_offsets`와 같은 내부 토픽을 제외합니다.                                                                                                                                                                                      |
| 클라이언트 아이디         | dataflow                        | string           | Kafka Consumer를 식별하는 ID를 입력합니다.                                                 | [Kafka 공식 문서](https://kafka.apache.org/39/configuration/consumer-configs/)의 `client.id` 속성 참고                                                                                                                                                                                                                                                        |
| 파티션 할당 정책         | ["RANGE", "COOPERATIVE_STICKY"] | array of strings | Kafka에서 메시지 수신 시 컨슈머 그룹에 어떻게 파티션을 할당할지 결정합니다.                                   | [Kafka 공식 문서](https://kafka.apache.org/39/configuration/consumer-configs/)의 `partition.assignment.strategy` 속성 참고 <br/>org.apache.kafka.clients.consumer.RangeAssignor<br/>org.apache.kafka.clients.consumer.RoundRobinAssignor<br/>org.apache.kafka.clients.consumer.StickyAssignor<br/>org.apache.kafka.clients.consumer.CooperativeStickyAssignor |
| 오프셋 설정            | latest                          | enum             | 컨슈머 그룹의 오프셋을 설정하는 기준을 입력합니다.                                                    | [Kafka 공식 문서](https://kafka.apache.org/39/configuration/consumer-configs/)의 `auto.offset.reset` 속성 참고 <br/>아래 설정 모두 컨슈머 그룹이 이미 존재하는 경우 기존 오프셋을 유지합니다.<br/>none: 컨슈머 그룹이 없으면 오류를 반환합니다.<br/>earliest: 컨슈머 그룹이 없으면 파티션의 가장 오래된 오프셋으로 초기화합니다.<br/>latest: 컨슈머 그룹이 없으면 파티션의 가장 최근 오프셋으로 초기화합니다.                                                          |
| 키 역직렬화 유형         | STRING                          | enum             | 수신하는 메시지의 키의 타입을 입력합니다.                                                         | [Kafka 공식 문서](https://kafka.apache.org/39/configuration/consumer-configs/)의 `key.deserializer` 속성 참고                                                                                                                                                                                                                                                 |
| 메타데이터 생성 여부       | false                           | boolean          | 속성값이 true일 경우 메시지에 대한 메타데이터 필드를 생성합니다. 메타데이터는 `kafka_metadata` 필드에 생성됩니다.       | 생성되는 필드는 다음과 같습니다.<br/>topic: 메시지를 수신한 토픽<br/>groupId: 메시지를 수신하는 데 사용한 컨슈머 그룹 아이디<br/>partition: 메시지를 수신한 토픽의 파티션 번호<br/>offset: 메시지를 수신한 파티션의 오프셋<br/>key: 메시지 키                                                                                                                                                                                    |
| Fetch 최소 크기       | 1                               | number           | 한 번의 fetch 요청으로 가져올 데이터의 최소 크기(byte)를 입력합니다.                                    | [Kafka 공식 문서](https://kafka.apache.org/39/configuration/consumer-configs/)의 `fetch.min.bytes` 속성 참고                                                                                                                                                                                                                                                  |
| 전송 버퍼 크기          | 131072                          | number           | 데이터를 전송하는 데 사용하는 TCP send 버퍼의 크기(byte)를 입력합니다.                                  | [Kafka 공식 문서](https://kafka.apache.org/39/configuration/consumer-configs/)의 `send.buffer.bytes` 속성 참고                                                                                                                                                                                                                                                |
| 재시도 요청 주기         | 100                             | number           | 전송 요청이 실패했을 때 재시도할 주기(ms)를 입력합니다.                                               | [Kafka 공식 문서](https://kafka.apache.org/39/configuration/consumer-configs/)의 `retry.backoff.ms` 속성 참고                                                                                                                                                                                                                                                 |
| 순환 중복 검사          | true                            | boolean          | 메시지의 CRC를 검사합니다.                                                                | [Kafka 공식 문서](https://kafka.apache.org/39/configuration/consumer-configs/)의 `check.crcs` 속성 참고                                                                                                                                                                                                                                                       |
| 서버 재연결 주기         | 50                              | number           | 브로커 서버에 연결이 실패했을 때 재시도할 주기(ms)를 입력합니다.                                          | [Kafka 공식 문서](https://kafka.apache.org/39/configuration/consumer-configs/)의 `reconnect.backoff.ms` 속성 참고                                                                                                                                                                                                                                             |
| 파티션 당 Fetch 최대 크기 | 1048576                         | number           | 파티션 당 한 번의 fetch 요청으로 가져올 최대 크기(byte)를 입력합니다.                                   | [Kafka 공식 문서](https://kafka.apache.org/39/configuration/consumer-configs/)의 `max.partition.fetch.bytes` 속성 참고                                                                                                                                                                                                                                        |
| 서버 요청 타임아웃        | 30000                           | number           | 전송 요청에 대한 타임아웃(ms)을 입력합니다.                                                      | [Kafka 공식 문서](https://kafka.apache.org/39/configuration/consumer-configs/)의 `request.timeout.ms` 속성 참고                                                                                                                                                                                                                                               |
| TCP 수신 버퍼 크기      | 65536                           | number           | 데이터를 읽는 데 사용하는 TCP receive 버퍼의 크기(byte)를 입력합니다.                                 | [Kafka 공식 문서](https://kafka.apache.org/39/configuration/consumer-configs/)의 `receive.buffer.bytes` 속성 참고                                                                                                                                                                                                                                             |
| 세션 타임아웃           | 45000                           | number           | 컨슈머의 세션 타임아웃(ms)을 입력합니다.<br/>컨슈머가 해당 시간 안에 heartbeat를 보내지 못할 경우 컨슈머 그룹에서 제외합니다. | [Kafka 공식 문서](https://kafka.apache.org/39/configuration/consumer-configs/)의 `session.timeout.ms` 속성 참고                                                                                                                                                                                                                                               |
| 최대 poll 메시지 개수    | 500                             | number           | 한 번의 poll 요청으로 가져올 최대 메시지 개수를 입력합니다.                                            | [Kafka 공식 문서](https://kafka.apache.org/39/configuration/consumer-configs/)의 `max.poll.records` 속성 참고                                                                                                                                                                                                                                                 |
| 최대 poll 주기        | 300000                          | number           | poll 요청 간 최대 주기(ms)를 입력합니다.                                                     | [Kafka 공식 문서](https://kafka.apache.org/39/configuration/consumer-configs/)의 `max.poll.interval.ms` 속성 참고                                                                                                                                                                                                                                             |
| Fetch 최대 크기       | 52428800                        | number           | 한 번의 fetch 요청으로 가져올 최대 크기(byte)를 입력합니다.                                         | [Kafka 공식 문서](https://kafka.apache.org/39/configuration/consumer-configs/)의 `fetch.max.bytes` 속성 참고                                                                                                                                                                                                                                                  |
| Fetch 최대 대기 시간    | 500                             | number           | `Fetch 최소 크기` 설정 만큼의 데이터가 모이지 않은 경우 fetch 요청을 보낼 대기 시간(ms)을 입력합니다.              | [Kafka 공식 문서](https://kafka.apache.org/39/configuration/consumer-configs/)의 `fetch.max.wait.ms` 속성 참고                                                                                                                                                                                                                                                |
| 컨슈머 헬스체크 주기       | 3000                            | number           | 컨슈머가 heartbeat를 보내는 주기(ms)를 입력합니다.                                              | [Kafka 공식 문서](https://kafka.apache.org/39/configuration/consumer-configs/)의 `heartbeat.interval.ms` 속성 참고                                                                                                                                                                                                                                            |
| 메타데이터 갱신 주기       | 300000                          | number           | 파티션, 브로커 서버 상태 등을 갱신할 주기(ms)를 입력합니다.                                            | [Kafka 공식 문서](https://kafka.apache.org/39/configuration/producer-configs/)의 `metadata.max.age.ms` 속성 참고                                                                                                                                                                                                                                              |
| IDLE 타임아웃         | 540000                          | number           | 데이터 전송이 없는 커넥션을 닫을 대기 시간(ms)을 입력합니다.                                            | [Kafka 공식 문서](https://kafka.apache.org/39/configuration/consumer-configs/)의 `connections.max.idle.ms` 속성 참고                                                                                                                                                                                                                                          |
| 격리 수준 | read_committed | enum | 컨슈머가 트랜잭션이 커밋되지 않은 메시지까지 읽을지, 커밋된 메시지만 읽을지를 결정합니다. | [Kafka 공식 문서](https://kafka.apache.org/39/configuration/consumer-configs/)의 `isolation.level` 속성 참고<br/>read_uncommitted: 모든 메시지를 오프셋 순서대로 읽습니다.<br/>read_committed: 커밋된 트랜잭션의 메시지만 읽습니다. |
| 추가 설정             | -                                 | hash             | Kafka 연결에 사용할 추가 Consumer 설정을 입력합니다.                                            | [Kafka 공식 문서](https://kafka.apache.org/39/configuration/consumer-configs/) 참고                                                                                                                                                                                                                                                                        |

### 코덱별 메시지 인입

지원 코덱
* [PLAIN 코덱](./codec-config-guide.md#plain) - 원본 데이터 문자열 저장
* [JSON 코덱](./codec-config-guide.md#json) - JSON 형식 데이터 파싱

## Filter

인입된 데이터를 어떻게 처리할지 정의하는 노드 유형입니다.

### Filter 노드의 공통 설정

| 속성명 | 기본값 | 자료형 | 설명 | 비고 |
| --- | --- | --- | --- | --- |
| 아이디 | - | string | 노드의 아이디를 설정합니다.<br/>이 속성에 정의된 값으로 차트보드에 노드 이름을 표기합니다. |  |

## Filter > Cipher

### 노드 설명

* 메시지 필드 값을 암복호화하는 노드입니다.
* 암호화 키는 Secure Key Manager 대칭 키를 참조합니다.
    * Secure Key Manager 대칭 키는 Secure Key Manager 웹 콘솔이나 Secure Key Manager의 키 추가 API로 생성할 수 있습니다.
    * 한 플로우에 여러 Cipher 노드가 포함되더라도 모든 Cipher 노드는 반드시 하나의 Secure Key Manager 키 레퍼런스만 참조할 수 있습니다.

### 속성 설명

| 속성명    | 기본값     | 자료형     | 설명                                        | 비고               |
|--------|---------|---------|-------------------------------------------|------------------|
| 모드     | -       | enum    | 암호화 모드와 복호화 모드 중 선택합니다.                   | 목록 중에 하나를 선택합니다. |
| 앱키     | -       | string  | 암/복호화에 사용할 키를 저장한 SKM 앱키를 입력합니다.          |                  |
| 키 ID   | -       | string  | 암/복호화에 사용할 키를 저장한 SKM 키 ID를 입력합니다.        |                  |
| 키 버전   | -       | string  | 암/복호화에 사용할 키를 저장한 SKM 키 버전을 입력합니다.        |                  |
| 소스 필드  | -       | string  | 암/복호화할 필드명을 입력합니다.                        | 스키마 정의 시 드롭다운 제공 |
| 저장할 필드 | -       | string  | 암/복호화 결과를 저장할 필드명을 입력합니다.                 |                  |
| 덮어쓰기   | false | boolean | 지정한 대상 필드명에 값이 존재하는 경우 이를 덮어쓸지 여부를 선택합니다. |                  |

### encrypt 예제

#### 조건

* mode → `encrypt`
* 앱키 → `SKM 앱키`
* 키 ID → `SKM 대칭키 ID`
* 키 버전 → `1`
* 소스 필드 → message
* 저장할 필드 → encrypted\_message

#### 입력 메시지

``` js
{
    "message": "this is plain message"
}
```

#### 출력 메시지

``` js
{
    "message": "this is plain message",
    "encrypted_message": "oZA6CHd4OwjPuS+MW0ydCU9NqbPQHGbPf4rll2ELzB8y5pyhxF6UhWZq5fxrt0/e"
}
```

### decrypt 예제

#### 조건

* mode → `decrypt`
* 앱키 → `SKM 앱키`
* 키 ID → `SKM 대칭키 ID`
* 키 버전 → `1`
* 소스 필드 → message
* 저장할 필드 → decrypted\_message

#### 입력 메시지

``` js
{
    "message": "oZA6CHd4OwjPuS+MW0ydCU9NqbPQHGbPf4rll2ELzB8y5pyhxF6UhWZq5fxrt0/e"
}
```

#### 출력 메시지

``` js
{
    "message": "oZA6CHd4OwjPuS+MW0ydCU9NqbPQHGbPf4rll2ELzB8y5pyhxF6UhWZq5fxrt0/e",
    "decrypted_message": "this is plain message"
}
```

## Filter > CSV

### 노드 설명
CSV 형식의 메시지를 파싱해 필드에 저장하는 노드입니다.

### 속성 설명

| 속성명       | 기본값                      | 자료형              | 설명                                             | 비고                       |
|-----------|--------------------------|------------------|------------------------------------------------|--------------------------|
| 저장할 필드    | -                        | string           | CSV 파싱 결과를 저장할 필드명을 입력합니다.                     |                          |
| Quote     | "                        | string           | 칼럼 필드를 나누는 문자를 입력합니다.                          |                          |
| 첫 행 무시 여부 | false                    | boolean          | 속성값이 true일 경우 읽은 데이터 중 첫 행에 입력된 칼럼 이름을 무시합니다.  |                          |
| 구분자       | ,                        | string           | 칼럼을 구분할 문자열을 입력합니다.                            |                          |
| 소스 필드     | - | string           | CSV 파싱할 필드명을 입력합니다.                            |                          |
| 스키마       | -                        | hash             | 각 칼럼의 이름과 자료형을 dictionary 형태로 입력합니다.           | `스키마 입력 방법` 참고 |
| 덮어쓰기      | false                    | boolean          | true일 경우 CSV 파싱 결과가 저장할 필드나 기존 필드와 겹치면 덮어씁니다. |                          |
| 원본 필드 삭제  | false                    | boolean          | CSV 파싱이 완료되면 소스 필드를 삭제합니다. 파싱이 실패한다면 유지합니다.    |                          |

#### 스키마 입력 방법
칼럼 타입을 지원하지 않고 전체 칼럼 및 자료형을 스키마로 입력받습니다.


### 자료형 변환이 필요 없는 CSV 파싱 예제

#### 조건

* 소스 필드 → `message`
* 스키마 → `{"one": "string", "two": "string", "t hree": "string"}`

#### 입력 메시지

```js
{
    "message": "hey,foo,\"bar baz\""
}
```

#### 출력 메시지

```js
{
    "message": "hey,foo,\"bar baz\"",
    "one": "hey",
    "t hree": "bar baz",
    "two": "foo"
}
```


### 자료형 변환이 필요한 CSV 파싱 예제

#### 조건

* 소스 필드 → `message`
* 스키마 → `{"one": "string", "two": "integer", "t hree": "boolean"}`

#### 입력 메시지

```js
{
    "message": "\"wow hello world!\", 2, false"
}
```

#### 출력 메시지

```js
{
    "message": "\"wow hello world!\", 2, false",
    "one": "wow hello world!",
    "t hree": false,
    "two": 2
}
```

## Filter > JSON

### 노드 설명

JSON 문자열을 파싱하여 지정된 필드에 저장하는 노드입니다.

### 속성 설명

| 속성명 | 기본값 | 자료형 | 설명 | 비고 |
| --- | --- | --- | --- | --- |
| 소스 필드 | - | string | JSON 문자열을 파싱할 필드명을 입력합니다. |  |
| 저장할 필드 | - | string | JSON 파싱 결과를 저장할 필드명을 입력합니다.<br/>만약 속성값을 지정하지 않을 경우 root 필드에 결과를 저장합니다. |  |
| 덮어쓰기 | false | boolean | true일 경우 JSON 파싱 결과가 저장할 필드나 기존 필드와 겹치면 덮어씁니다.  |  |
| 원본 필드 삭제 | false | boolean | JSON 파싱이 완료되면 소스 필드를 삭제합니다. 파싱이 실패한다면 유지합니다. |  |
| 스키마 | - | hash | 각 필드의 이름과 자료형을 dictionary 형태로 입력합니다. | `스키마 입력 방법` 참고 |

#### 스키마 입력 방법
칼럼 타입을 지원하지 않고 전체 칼럼 및 자료형을 스키마로 입력받습니다.

### 자료형 변환이 필요 없는 JSON 파싱 예제

#### 조건

* 소스 필드 → `message`
* 저장할 필드 → `json_parsed_message`

#### 입력 메시지

```js
{
    "message": "{\"json\": \"parse\", \"example\": \"string\"}"
}
```

#### 출력 메시지

```js
{
    "json_parsed_message": {
        "json": "parse",
        "example": "string"
    },
    "message": "{\"json\": \"parse\", \"example\": \"string\"}"
}
```

### 자료형 변환이 필요한 JSON 파싱 예제

#### 조건

* 소스 필드 → `message`
* 저장할 필드 → `json_parsed_message`
* 스키마 → `{"json": "string", "example": "integer"}`

#### 입력 메시지

```js
{
    "message": "{\"json\": \"parse\", \"example\": \"123\"}"
}
```

#### 출력 메시지

```js
{
    "json_parsed_message": {
        "json": "parse",
        "example": 123
    },
    "message": "{\"json\": \"parse\", \"example\": \"123\"}"
}
```

## Filter > Date

### 노드 설명

Date 문자열을 파싱하여 timestamp 형태로 저장하는 노드입니다.

### 속성 설명

| 속성명    | 기본값                              | 자료형              | 설명                                   | 비고                                                              |
|--------|----------------------------------|------------------|--------------------------------------|-----------------------------------------------------------------|
| 소스 필드  | -                                | string           | 문자열을 가져오기 위한 필드명을 입력합니다.             | 스키마 정의 시 드롭다운 제공                                                |
| 형식     | -                                | array of strings | 문자열을 가져오기 위한 형식을 입력합니다.              | 사전 정의된 형식은 다음과 같습니다.<br/>ISO8601, UNIX, UNIX_MS |
| Locale | ko_KR      | string           | Date 문자열 분석을 위해 사용할 Locale을 입력합니다.   | 예: en, en-US, ko_KR                                             |
| 저장할 필드 | -  | string           | Date 문자열 파싱 결과를 저장할 필드명을 입력합니다.      |                                                                 |
| 시간대    | Asia/Seoul | string           | 날짜의 시간대를 입력합니다.                      | 예: Asia/Seoul                                                   |

### Date 문자열 파싱 예제

#### 조건

* 소스 필드 → `message`
* 형식 → `["yyyy-MM-dd HH:mm:ssZ", "ISO8601"]`
* 저장할 필드 → `time`
* 시간대 → `Asia/Seoul`

#### 입력 메시지

```js
{
    "message": "2017-03-16T17:40:00"
}
```

#### 출력 메시지

```js
{
    "message": "2017-03-16T17:40:00",
    "time": 2022-04-04T09:08:01.222Z
}
```

## Filter > UUID

### 노드 설명

UUID를 생성하여 필드에 저장하는 노드입니다.

### 속성 설명

| 속성명 | 기본값 | 자료형 | 설명 | 비고 |
| --- | --- | --- | --- | --- |
| UUID 저장 필드 | - | string | UUID 생성 결과값을 저장할 필드명을 입력합니다. |  |
| 덮어쓰기 | false | boolean | 지정된 필드명에 값이 존재할 경우 이를 덮어쓸지 여부를 선택합니다. |  |

### UUID 생성 예제

#### 조건

UUID 저장 필드 → `userId`

#### 입력 메시지

```js
{
    "message": "uuid test message"
}
```

#### 출력 메시지

```js
{
    "userId": "70186b1e-bdec-43d6-8086-ed0481b59370",
    "message": "uuid test message"
}
```

## Filter > Convert

### 노드 설명

특정 필드의 데이터 타입을 변환하는 노드입니다.

### 속성 설명

| 속성명   | 기본값 | 자료형    | 설명                                                                          | 비고 |
|-------|-----|--------|-----------------------------------------------------------------------------|----|
| 대상 필드 | -   | string | 데이터 타입을 변환할 대상 필드를 입력합니다.                                                   | 스키마 정의 시 드롭다운 제공 |
| 변환 타입 | -   | enum   | 변환할 데이터 타입을 선택합니다. <br/> * 제공 타입: `STRING, INTEGER, FLOAT, DOUBLE, BOOLEAN` |    |

### 데이터 변환 예제

#### 조건

* 대상 필드 → `message`
* 변환 타입 → `INTEGER`

#### 입력 메시지

```js
{
    "message": "2025"
}
```

#### 출력 메시지

```js
{
    "message": 2025
}
```


## Filter > Coerce

### 노드 설명

null 값을 기본값으로 대체하는 노드입니다.

### 속성 설명

| 속성명 | 기본값 | 자료형 | 설명 | 비고 |
| --- | --- | --- | --- | --- |
| 대상 필드 | - | string | 기본값을 지정할 필드명을 입력합니다. | 스키마 정의 시 드롭다운 제공 |
| 기본값 | - | string | 기본값을 입력합니다. |  |

### 기본값 설정 예제

#### 조건
* 대상 필드 → `fieldname`
* 기본값 → `default_value`

#### 입력 메시지

```json
{
    "fieldname": null
}
```

#### 출력 메시지

```json
{
    "fieldname": "default_value"
}
```

## Filter > Copy

### 노드 설명

기존 필드를 다른 필드로 복사하는 노드입니다.

### 속성 설명

| 속성명 | 기본값 | 자료형 | 설명 | 비고 |
| --- | --- | --- | --- | --- |
| 대상 필드 | - | string | 복사할 소스 필드명을 입력합니다. | 스키마 정의 시 드롭다운 제공 |
| 저장할 필드 | - | string | 복사한 결과를 저장할 필드명을 입력합니다. |  |
| 덮어쓰기 | false | boolean | true일 경우 저장할 필드가 이미 존재하면 덮어씁니다.  |  |

### 예제

#### 조건
* 대상 필드 → `source_field`
* 저장할 필드 → `dest_field`

#### 입력 메시지

```json
{
    "source_field": "Hello World!"
}

```

#### 출력 메시지

```json
{
    "source_field": "Hello World!",
    "dest_field": "Hello World!"
}
```

## Filter > Rename

### 노드 설명

필드 이름을 변경하는 노드입니다.

### 속성 설명

| 속성명 | 기본값 | 자료형 | 설명 | 비고 |
| --- | --- | --- | --- | --- |
| 소스 필드 |  | string | 이름을 변경할 소스 필드를 입력합니다. | 스키마 정의 시 드롭다운 제공 |
| 대상 필드 | - | string | 변경할 필드명을 입력합니다. |  |
| 덮어쓰기 | false | boolean | true일 경우 대상 필드가 이미 존재할 경우 덮어씁니다.  |  |

### 예제

#### 조건
* 소스 필드 → `fieldname`
* 대상 필드 → `changed_fieldname`

#### 입력 메시지

```json
{
    "fieldname": "Hello World!"
}
```

#### 출력 메시지

```json
{
    "changed_fieldname": "Hello World!"
}
```

## Filter > Strip

### 노드 설명

필드의 문자열 앞뒤 공백을 제거하는 노드입니다.

### 속성 설명

| 속성명 | 기본값 | 자료형 | 설명 | 비고 |
| --- | --- | --- | --- | --- |
| 대상 필드 | - | array of strings | 공백을 제거할 대상 필드들을 입력합니다. | 스키마 정의 시 드롭다운 제공(복수 선택) |

### 예제

#### 조건
대상 필드 → `["field1", "field2"]`

#### 입력 메시지

```json
{
    "field1": "Hello World!   ",
    "field2": "   Hello DataFlow!"
}

```

#### 출력 메시지

```json
{
    "field1": "Hello World!",
    "field2": "Hello DataFlow!"
}

```

## Filter > Remove Fields

### 노드 설명

필드를 삭제하는 노드입니다.

### 속성 설명

| 속성명    | 기본값 | 자료형              | 설명                 | 비고 |
|--------|-----|------------------|--------------------|----|
| 삭제할 필드 | -   | array of strings | 삭제할 필드명 목록을 입력합니다. | 스키마 정의 시 드롭다운 제공(복수 선택) |

### 설정 예제

#### 조건
삭제할 필드 → `["field2", "field3"]`

#### 입력 메시지

```json
{
    "field1": "value1",
    "field2": "value2",
    "field3": "value3",
    "field4": "value4"
}
```

#### 출력 메시지

```json
{
    "field1": "value1",
    "field4": "value4"
}
```

## Filter > Tokenizer

### 노드 설명

정규식을 이용해 문자열 필드를 토큰화하는 노드입니다.

### 속성 설명

| 속성명      | 기본값         | 자료형     | 설명                                              | 비고                                               |
|----------|-------------|---------|-------------------------------------------------|--------------------------------------------------|
| 소스 필드    | -           | string  | 토큰화할 소스 필드명을 입력합니다.                            |                                                  |
| 저장할 필드   | -           | string  | 토큰화 결과를 저장할 필드명을 입력합니다.                         |                                                  |
| 정규식      | \s+       | string  | 토큰화에 사용될 정규식을 입력합니다.                            |                                                  |
| 모드       | SEPARATOR | enum    | 토큰화 모드를 선택합니다.                                  | SEPARATOR: 정규식을 구분자로 사용<br>MATCH: 정규식을 토큰 매칭에 사용 |
| 최소 토큰 길이 | 1           | number  | 토큰의 최소 길이를 입력합니다. 최소 토큰 길이보다 짧은 토큰은 결과에서 제외됩니다. |                                                  |
| 덮어쓰기     | false     | boolean | true일 경우 저장할 필드가 이미 존재하면 덮어씁니다.                 |                                                  |

### SEPARATOR 모드 예제

#### 조건
* 소스 필드 → `src_field`
* 저장할 필드 → `target_field`
* 정규식 → `,`
* 모드 → `SEPARATOR`

#### 입력 메시지

```json
{
    "src_field": "foo,bar,baz"
}
```

#### 출력 메시지

```json
{
    "src_field": "foo,bar,baz",
    "target_field": ["foo", "bar", "baz"]
}
```

### MATCH 모드 예제

#### 조건
* 소스 필드 → `src_field`
* 저장할 필드 → `target_field`
* 정규식 → `[^,]+`
* 모드 → `MATCH`

#### 입력 메시지

```json
{
    "src_field": "foo,bar,baz"
}
```

#### 출력 메시지

```json
{
    "src_field": "foo,bar,baz",
    "target_field": ["foo", "bar", "baz"]
}
```

## Filter > Sampling

### 노드 설명

* 메시지를 일정 비율로 선별하여 다음 노드로 전달하는 노드입니다.
* 확률 기반으로 전달 여부를 결정합니다. 따라서, 메시지의 수가 적을수록 입력한 비율과의 오차가 커집니다.

### 속성 설명

| 속성명 | 기본값 | 자료형 | 설명 | 비고 |
| --- | --- | --- | --- | --- |
| 비율 | - | number | 메시지를 다음 노드로 전달할 비율을 입력합니다. |  |
| 시드 | - | number | 난수 생성 시 사용할 시드를 입력합니다. 시드가 같고 입력 메시지가 동일하다면 결과는 동일합니다. |  |

## Filter > Stop Words Remover(불용어 제거)

### 노드 설명

문자열 배열 필드에 포함된 Stop Word(불용어)를 제거하는 노드입니다.

### 속성 설명

| 속성명             | 기본값     | 자료형     | 설명                                           | 비고 |
|-----------------|---------|---------|----------------------------------------------|----|
| 소스 필드           | -       | string  | 불용어를 제거할 소스 필드명을 입력합니다.                     |    |
| 저장할 필드          | -       | string  | 불용어 제거 결과를 저장할 필드명을 입력합니다.                   |    |
| 기본 제공 불용어 사전 언어 | none  | enum    | 불용어 제거에 사용할 기본 제공 불용어 사전의 언어를 선택합니다.         |    |
| 불용어 사전          |         | string  | 불용어 제거에 사용할 단어 목록을 입력합니다. 각 단어는 줄바꿈으로 구분됩니다. |    |
| 대소문자 구분 여부      | false | boolean | 대소문자 구분 여부를 선택합니다.                           |    |
| 덮어쓰기            | false | boolean | true일 경우 저장할 필드가 이미 존재하면 덮어씁니다.              |    |

### 사전 정의 사전
* 언어별 사전 정의 사전은 다음과 같습니다.
  * [ko](http://static.toastoven.net/prod_dataflow/ko/node-config-guide/stop_word_remover_dict_ko.txt)
  * [en](http://static.toastoven.net/prod_dataflow/ko/node-config-guide/stop_word_remover_dict_en.txt)

### 설정 예제

#### 조건
* 소스 필드 → `src_field`
* 저장할 필드 → `target_field`
* 사전
```
is
a
```

#### 입력 메시지

```json
{
    "src_field": ["hello", "world", "this", "is", "a", "test"]
}
```

#### 출력 메시지

```json
{
  "src_field": ["hello", "world", "this", "is", "a", "test"],
  "target_field": ["hello", "world", "this", "test"]
}
```

## Filter > Pattern Extractor (Grok)

### 노드 설명

* 텍스트 데이터에서 구조화된 정보를 추출하는 노드입니다.
* 정규 표현식을 활용한 패턴 매칭으로 로그나 텍스트에서 필요한 정보를 추출합니다.
* Logstash와 호환되는 Grok 패턴 문법을 지원하며, 복잡한 로그 파싱을 간단한 패턴으로 처리할 수 있습니다.
* 기본 제공 패턴을 활용하거나 사용자 정의 패턴을 생성하여 다양한 형식의 데이터를 파싱할 수 있습니다.


### 속성 설명

| 속성명 | 기본값 | 자료형 | 설명 | 비고 |
|---|---|---|---|---|
| 소스 필드 | - | string | 패턴을 추출할 원본 필드명을 입력합니다. |   |
| 대상 필드 | - | string | 추출 결과를 저장할 필드명을 입력합니다. 입력하지 않으면 결과가 root에 바로 추가됩니다. |   |
| 사용자 정의 패턴 | - | hash | 기본 제공 패턴 외에 추가로 사용할 패턴을 정의합니다. 패턴명과 정규표현식을 key-value 형태로 입력합니다. | 동일한 패턴명이 기본 제공 패턴에 존재할 경우, 사용자 정의 패턴이 우선 적용되어 기본 패턴을 재정의할 수 있습니다. |
| 패턴 표현식 | - | string | 데이터에서 추출할 필드와 패턴을 Grok 표현식으로 입력합니다. |   |
| 덮어쓰기 | false | boolean | 대상 필드에 이미 값이 존재하는 경우 추출 결과로 덮어쓸지 여부를 설정합니다. |   |

!!! tip "기본 제공 패턴"
    자주 사용되는 패턴들을 사전 정의하여 제공합니다.
    날짜/시간, IP 주소, URL, 로그 레벨 등 여러 가지 상황에 필요한 다양한 패턴이 포함되어 있습니다.
    기본 제공 패턴은 내부적으로 다른 패턴들을 참조하는 계층 구조를 가지므로, 지정한 필드명 외에 추가 필드가 생성될 수 있습니다.
    [기본 제공 패턴 목록](https://static.toastoven.net/prod_dataflow/node-config-guide/predefined_patterns.txt) 참고


### 예제

#### 조건
* 소스 필드 → `log_message`
* 대상 필드 → `result`
* 사용자 정의 패턴 → `{"CUSTOM_PHONE_NUMBER": "01[016789]-\d{3,4}-\d{4}", "CUSTOM_EMPLOYEE_ID": "EMP-\d{6}", "CUSTOM_ORDER_ID": "ORD-[A-Z]{3}-\d{8}"}`
* 패턴 표현식 → `%{TIMESTAMP_ISO8601:timestamp} %{LOGLEVEL:level} %{CUSTOM_EMPLOYEE_ID:custom_emp_id} %{CUSTOM_PHONE_NUMBER:custom_phone_number} %{CUSTOM_ORDER_ID:custom_order_id} %{GREEDYDATA:message}`

#### 입력 메시지

```json
{
  "log_message": "2024-03-15T09:30:00.000Z INFO EMP-123456 010-1234-5678 ORD-ABC-12345678 Order processing started",
  "created_by": "DataFlow"
}

```

#### 출력 메시지

```json
{
  "log_message": "2024-03-15T09:30:00.000Z INFO EMP-123456 010-1234-5678 ORD-ABC-12345678 Order processing started",
  "created_by": "DataFlow",
  "result": {
    "YEAR": "2024",
    "MONTHNUM": "03",
    "ISO8601_TIMEZONE": "Z",
    "MONTHDAY": "15",
    "HOUR": [
      "09",
      null
    ],
    "MINUTE": [
      "30",
      null
    ],
    "SECOND": "00.000",
    "timestamp": "2024-03-15T09:30:00.000Z",
    "level": "INFO",
    "custom_emp_id": "EMP-123456",
    "custom_phone_number": "010-1234-5678",
    "custom_order_id": "ORD-ABC-12345678",
    "message": "Order processing started"
  }
}
```

## Sink

Filter 작업을 마친 데이터를 적재할 엔드포인트를 정의하는 노드 유형입니다.

### Sink 노드의 공통 설정

| 속성명 | 기본값 | 자료형 | 설명 | 비고 |
| --- | --- | --- | --- | --- |
| 아이디 | - | string | 노드의 아이디를 설정합니다.<br/>이 속성에 정의된 값으로 차트보드에 노드 이름을 표기합니다. |  |

## Sink > (NHN Cloud) Object Storage

### 노드 설명

* NHN Cloud의 Object Storage에 데이터를 업로드하는 노드입니다.
* 다른 설정 없이 기본 설정만으로 생성하면 오브젝트는 다음 경로 포맷에 맞게 출력됩니다.
    * `/{bucket_name}/year={yyyy}/month={MM}/day={dd}/hour={HH}/part-{uuid}-{file_counter}`   
* 제공 코덱은 JSON, LINE, Parquet입니다.

### 속성 설명

| 속성명                   | 기본값                                                | 자료형    | 설명                                                           | 비고                                                                                                                         |
|-----------------------|----------------------------------------------------|--------|--------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------|
| 리전                    | -                                                  | enum   | Object Storage 상품의 리전을 입력합니다.                                |                                                                                                                            |
| 버킷                    | -                                                  | string | 버킷 이름을 입력합니다.                                                |                                                                                                                            |
| 비밀 키                  | -                                                  | string | S3 API 자격 증명 비밀 키를 입력합니다.                                    |                                                                                                                            |
| 액세스 키                 | -                                                  | string | S3 API 자격 증명 액세스 키를 입력합니다.                                   |                                                                                                                            |
| Prefix                | /year=%{+YYYY}/month=%{+MM}/day=%{+dd}/hour=%{+HH} | string | 오브젝트 업로드 시 이름 앞에 붙일 접두사를 입력합니다.<br/>필드 또는 시간 형식을 입력할 수 있습니다. | [사용 가능한 시간 형식](https://joda-time.sourceforge.net/apidocs/org/joda/time/format/DateTimeFormat.html)                         |
| Prefix 시간 필드          | -                                                  | string | Prefix에 적용할 시간 필드를 입력합니다.                                    |                                                                                                                            |
| Prefix 시간 필드 타입       | DATE_FILTER_RESULT                                 | enum   | Prefix에 적용할 시간 필드의 타입을 입력합니다.                                | DATE_FILTER_RESULT 타입만 가능(추후 다른 타입 지원 예정)                                                                        |
| Prefix 시간대            | UTC                                                | string | Prefix에 적용할 시간 필드의 타임 존을 입력합니다.                              |                                                                                                                            |
| Prefix 시간 적용 fallback | _prefix_datetime_parse_failure                                   | string | Prefix 시간 적용에 실패한 경우 대체할 Prefix를 입력합니다.                      |                                                                                                                            |
| 기준 시각                 | 1                                                  | number | 오브젝트를 분할할 기준이 될 시간을 설정합니다.                                   |                                                                                                                            |
| 기준 오브젝트 크기            | 5242880                                            | number | 오브젝트를 분할할 기준이 될 크기(단위: byte)를 설정합니다.                         |                                                                                                                            |
| 비활성 간격                | 1                                                  | number | 데이터 인입이 없는 상태가 지속될 때 오브젝트를 분할하는 기준 시간을 설정합니다.                | 설정된 시간 동안 데이터 인입이 없으면 현재 오브젝트가 업로드되며, 이후 새로 인입되는 데이터는 새로운 오브젝트에 작성됩니다.                                                     |

### 코덱별 출력 예제

지원 코덱
* [JSON 코덱](./codec-config-guide.md#json) - JSON 형식 데이터 파싱
* [LINE 코덱](./codec-config-guide.md#line) - 행 단위 메시지 처리
* [Parquet 코덱](./codec-config-guide.md#parquet) - 데이터를 Parquet 형식으로 압축 

### Prefix 예시 - 필드

#### 조건

* 버킷 → `obs-test-container`
* Prefix → `/dataflow/%{deployment}`

#### 입력 메시지
``` json
{
    "deployment": "production",
    "message": "example",
    "logTime": "2022-11-21T07:49:20Z"
}
```

#### 출력 경로

```
/obs-test-container/dataflow/production/part-378be4d8-2c59-4014-aaeb-a9bc75af2653-0
```

### Prefix 예시 - 시간

#### 조건

* 버킷 → `obs-test-container`
* Prefix → `/dataflow/year=%{+YYYY}/month=%{+MM}/day=%{+dd}/hour=%{+HH}`
* Prefix 시간 필드 → `logTime`
* Prefix 시간 필드 타입 → `ISO8601`
* Prefix 시간대 → `Asia/Seoul`

#### 입력 메시지
``` json
{
    "deployment": "production",
    "message": "example",
    "logTime": "2022-11-21T07:49:20Z"
}
```

#### 출력 경로

```
/obs-test-container/dataflow/year=2022/month=11/day=21/hour=16/part-378be4d8-2c59-4014-aaeb-a9bc75af2653-0
```

### Prefix 예시 - 시간 적용 실패한 경우

#### 조건

* 버킷 → `obs-test-container`
* Prefix → `/dataflow/year=%{+YYYY}/month=%{+MM}/day=%{+dd}/hour=%{+HH}`
* Prefix 시간 필드 → `logTime`
* Prefix 시간 필드 타입 → `TIMESTAMP_SEC`
* Prefix 시간대 → `Asia/Seoul`
* Prefix 시간 적용 fallback → `_failure`

#### 입력 메시지
``` json
{
    "deployment": "production",
    "message": "example",
    "logTime": "2022-11-21T07:49:20Z"
}
```

#### 출력 경로

```
/obs-test-container/_failure/part-378be4d8-2c59-4014-aaeb-a9bc75af2653-0
```

## Sink > (NHN Cloud) Data Lake Storage

### 노드 설명
* NHN Cloud의 Data Lake Storage에 데이터를 업로드하는 노드입니다.
* 다른 설정 없이 기본 설정만으로 생성하면 오브젝트는 다음 경로 포맷에 맞게 출력됩니다.
    * `/{bucket_name}/year={yyyy}/month={MM}/day={dd}/hour={HH}/part-{uuid}-{file_counter}`   
* 제공 코덱은 JSON, LINE, Parquet입니다.

### 속성 설명
| 속성명                   | 기본값                                                | 자료형    | 설명                                                           | 비고                                                                                                                         |
|-----------------------|----------------------------------------------------|--------|--------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------|
| 리전                    | -                                                  | enum   | Data Lake Storage 상품의 리전을 입력합니다.                                |                                                                                                                            |
| 버킷                    | -                                                  | string | 버킷 이름을 입력합니다.                                                |                                                                                                                            |
| 비밀 키                  | -                                                  | string | S3 API 자격 증명 비밀 키를 입력합니다.                                    |                                                                                                                            |
| 액세스 키                 | -                                                  | string | S3 API 자격 증명 액세스 키를 입력합니다.                                   |                                                                                                                            |
| Prefix                | /year=%{+YYYY}/month=%{+MM}/day=%{+dd}/hour=%{+HH} | string | 오브젝트 업로드 시 이름 앞에 붙일 접두사를 입력합니다.<br/>필드 또는 시간 형식을 입력할 수 있습니다. | [사용 가능한 시간 형식](https://joda-time.sourceforge.net/apidocs/org/joda/time/format/DateTimeFormat.html)                         |
| Prefix 시간 필드          | -                                                  | string | Prefix에 적용할 시간 필드를 입력합니다.                                    |                                                                                                                            |
| Prefix 시간 필드 타입       | DATE_FILTER_RESULT                                 | enum   | Prefix에 적용할 시간 필드의 타입을 입력합니다.                                | DATE_FILTER_RESULT 타입만 가능(추후 다른 타입 지원 예정)                                                                        |
| Prefix 시간대            | UTC                                                | string | Prefix에 적용할 시간 필드의 타임 존을 입력합니다.                              |                                                                                                                            |
| Prefix 시간 적용 fallback | _prefix_datetime_parse_failure                                   | string | Prefix 시간 적용에 실패한 경우 대체할 Prefix를 입력합니다.                      |                                                                                                                            |
| 기준 시각                 | 1                                                  | number | 오브젝트를 분할할 기준이 될 시간을 설정합니다.                                   |                                                                                                                            |
| 기준 오브젝트 크기            | 5242880                                            | number | 오브젝트를 분할할 기준이 될 크기(단위: byte)를 설정합니다.                         |                                                                                                                            |
| 비활성 간격                | 1                                                  | number | 데이터 인입이 없는 상태가 지속될 때 오브젝트를 분할하는 기준 시간을 설정합니다.                | 설정된 시간 동안 데이터 인입이 없으면 현재 오브젝트가 업로드되며, 이후 새로 인입되는 데이터는 새로운 오브젝트에 작성됩니다.                                                     |

### 코덱별 출력 예제
지원 코덱
* [JSON 코덱](./codec-config-guide.md#json) - JSON 형식 데이터 파싱
* [LINE 코덱](./codec-config-guide.md#line) - 행 단위 메시지 처리
* [Parquet 코덱](./codec-config-guide.md#parquet) - 데이터를 Parquet 형식으로 압축 

### Prefix 예시 - 필드
#### 조건
* 버킷 → `dls-test-container`
* Prefix → `/dataflow/%{deployment}`

#### 입력 메시지
``` json
{
    "deployment": "production",
    "message": "example",
    "logTime": "2022-11-21T07:49:20Z"
}
```

#### 출력 경로
```
/dls-test-container/dataflow/production/part-378be4d8-2c59-4014-aaeb-a9bc75af2653-0
```

### Prefix 예시 - 시간
#### 조건
* 버킷 → `dls-test-container`
* Prefix → `/dataflow/year=%{+YYYY}/month=%{+MM}/day=%{+dd}/hour=%{+HH}`
* Prefix 시간 필드 → `logTime`
* Prefix 시간 필드 타입 → `ISO8601`
* Prefix 시간대 → `Asia/Seoul`

#### 입력 메시지
``` json
{
    "deployment": "production",
    "message": "example",
    "logTime": "2022-11-21T07:49:20Z"
}
```

#### 출력 경로
```
/dls-test-container/dataflow/year=2022/month=11/day=21/hour=16/part-378be4d8-2c59-4014-aaeb-a9bc75af2653-0
```

### Prefix 예시 - 시간 적용 실패한 경우
#### 조건
* 버킷 → `dls-test-container`
* Prefix → `/dataflow/year=%{+YYYY}/month=%{+MM}/day=%{+dd}/hour=%{+HH}`
* Prefix 시간 필드 → `logTime`
* Prefix 시간 필드 타입 → `ISO8601`
* Prefix 시간대 → `Asia/Seoul`
* Prefix 시간 적용 fallback → `_failure`

#### 입력 메시지
``` json
{
    "deployment": "production",
    "message": "example",
    "logTime": "2022-11-21T07:49:20Z"
}
```

#### 출력 경로
```
/dls-test-container/_failure/part-378be4d8-2c59-4014-aaeb-a9bc75af2653-0
```

## Sink > (Amazon) S3

### 노드 설명

* Amazon S3에 데이터를 업로드하는 노드입니다.
* 제공 코덱은 JSON, LINE, Parquet입니다.

### 속성 설명
| 속성명 | 기본값 | 자료형 | 설명 | 비고 |
| --- | --- | --- | --- | --- |
| 리전 | - | enum | S3 상품의 리전을 입력합니다. | [s3 region](https://docs.aws.amazon.com/general/latest/gr/s3.html) |
| 버킷 | - | string | 버킷 이름을 입력합니다. |  |
| 액세스 키 | - | string | S3 API 자격 증명 액세스 키를 입력합니다. |  |
| 비밀 키 | - | string | S3 API 자격 증명 비밀 키를 입력합니다. |  |
| Prefix | - | string | 오브젝트 업로드 시 이름 앞에 붙일 접두사를 입력합니다.<br/>필드 또는 시간 형식을 입력할 수 있습니다. | [사용 가능한 시간 형식](https://joda-time.sourceforge.net/apidocs/org/joda/time/format/DateTimeFormat.html) |
| Prefix 시간 필드 | - | string | Prefix에 적용할 시간 필드를 입력합니다. |  |
| Prefix 시간 필드 타입 | DATE_FILTER_RESULT | enum | Prefix에 적용할 시간 필드의 타입을 입력합니다. | DATE_FILTER_RESULT 타입만 가능(추후 다른 타입 지원 예정) |
| Prefix 시간대 | UTC | string | Prefix에 적용할 시간 필드의 타임 존을 입력합니다. |  |
| Prefix 시간 적용 fallback  | _prefix_datetime_parse_failure | string | Prefix 시간 적용에 실패한 경우 대체할 Prefix를 입력합니다. |  |
| 기준 시각 | 1 | number | 오브젝트를 분할할 기준이 될 시간을 설정합니다. |  |
| 기준 오브젝트 크기 | 5242880 | number | 오브젝트를 분할할 기준이 될 크기를 설정합니다. |  |
| 경로 방식 요청 | false | boolean | 경로 방식 요청을 사용할지 여부를 결정합니다. |  |
| 비활성 간격 | 1 | number | 데이터 인입이 없는 상태가 지속될 때 오브젝트를 분할하는 기준 시간을 설정합니다.                | 설정된 시간 동안 데이터 인입이 없으면 현재 오브젝트가 업로드되며, 이후 새로 인입되는 데이터는 새로운 오브젝트에 작성됩니다. |

!!! danger "주의"
    * (Amazon) S3 노드를 이용하여 NHN Cloud Object Storage에 연결할 경우 **경로 방식 요청**을 `true`로 설정해야 합니다.


### 코덱별 출력 예제

지원 코덱
* [JSON 코덱](./codec-config-guide.md#json) - JSON 형식 데이터 파싱
* [LINE 코덱](./codec-config-guide.md#line) - 행 단위 메시지 처리
* [Parquet 코덱](./codec-config-guide.md#parquet) - 데이터를 Parquet 형식으로 압축 

## Sink > (NHN Cloud) EasyQueue

### 노드 설명
NHN Cloud의 EasyQueue에 데이터를 전송하는 노드입니다.

### 속성 설명
| 속성명 | 기본값 | 자료형 | 설명 | 비고 |
| --- | --- | --- | --- | --- |
| 앱키 | - | string | EasyQueue의 앱키를 입력합니다. |  |
| User Access Key ID | - | string | 사용자 계정의 User Access Key ID를 입력합니다. |  |
| Secret Access Key | - | string | 사용자 계정의 User Secret Key를 입력합니다. |  |
| 토픽 | - | string | 메시지를 전송할 Kafka 토픽 이름을 입력합니다. |  |
| 브로커 서버 목록 | - | string | Kafka 브로커 서버를 입력합니다. 서버가 여러 대일 경우 콤마(`,`)로 구분합니다. | [Kafka 공식 문서](https://kafka.apache.org/39/configuration/producer-configs/)의 `bootstrap.servers` 속성 참고<br/>예: 10.100.1.1:9092,10.100.1.2:9092 |
| 클라이언트 아이디 | dataflow | string | Kafka Producer를 식별하는 ID를 입력합니다. | [Kafka 공식 문서](https://kafka.apache.org/39/configuration/producer-configs/)의 `client.id` 속성 참고 |
| 압축 유형 | none | enum | 전송하는 데이터를 압축할 방법을 입력합니다. | [Kafka 공식 문서](https://kafka.apache.org/39/configuration/topic-level-configs/)의 `compression.type` 속성 참고<br/>none, gzip, snappy, lz4, zstd 중 선택 |
| 메시지 키 | - | string | 메시지 키로 사용할 필드를 입력합니다. |  |
| 메타데이터 갱신 주기 | 300000 | number | 파티션, 브로커 서버 상태 등을 갱신할 주기(ms)를 입력합니다. | [Kafka 공식 문서](https://kafka.apache.org/39/configuration/producer-configs/)의 `metadata.max.age.ms` 속성 참고 |
| 최대 요청 크기 | 1048576 | number | 전송 요청당 최대 크기(byte)를 입력합니다. | [Kafka 공식 문서](https://kafka.apache.org/39/configuration/producer-configs/)의 `max.request.size` 속성 참고 |
| 서버 재연결 주기 | 50 | number | 브로커 서버에 연결이 실패했을 때 재시도할 주기(ms)를 입력합니다. | [Kafka 공식 문서](https://kafka.apache.org/39/configuration/producer-configs/)의 `reconnect.backoff.ms` 속성 참고 |
| 배치 크기 | 16384 | number | 배치 요청으로 전송할 크기(byte)를 입력합니다. | [Kafka 공식 문서](https://kafka.apache.org/39/configuration/producer-configs/)의 `batch.size` 속성 참고 |
| 버퍼 메모리 | 33554432 | number | Kafka 전송에 사용하는 버퍼의 크기(byte)를 입력합니다. | [Kafka 공식 문서](https://kafka.apache.org/39/configuration/producer-configs/)의 `buffer.memory` 속성 참고 |
| 수신 버퍼 크기 | 32768 | number | 데이터를 읽는 데 사용하는 TCP receive 버퍼의 크기(byte)를 입력합니다. | [Kafka 공식 문서](https://kafka.apache.org/39/configuration/producer-configs/)의 `receive.buffer.bytes` 속성 참고 |
| 전송 지연 시간 | 0 | number | 메시지 전송을 지연할 시간을 입력합니다. 지연된 메시지는 배치 요청으로 한 번에 전송합니다. | [Kafka 공식 문서](https://kafka.apache.org/39/configuration/producer-configs/)의 `linger.ms` 속성 참고 |
| 서버 요청 타임아웃 | 30000 | number | 전송 요청에 대한 타임아웃(ms)을 입력합니다. | [Kafka 공식 문서](https://kafka.apache.org/39/configuration/producer-configs/)의 `request.timeout.ms` 속성 참고 |
| 전송 버퍼 크기 | 131072 | number | 데이터를 전송하는 데 사용하는 TCP send 버퍼의 크기(byte)를 입력합니다. | [Kafka 공식 문서](https://kafka.apache.org/39/configuration/producer-configs/)의 `send.buffer.bytes` 속성 참고 |
| ack 속성 | all | enum | 브로커 서버에서 메시지를 받았는지 확인하는 설정을 입력합니다. | [Kafka 공식 문서](https://kafka.apache.org/39/configuration/producer-configs/)의 `acks` 속성 참고<br/>0 - 메시지 수신 여부를 확인하지 않습니다.<br/>1 - 토픽의 leader가 follower가 데이터를 복사하는 것을 기다리지 않고 메시지를 수신했다는 응답을 합니다.<br/>all - 토픽의 leader가 follower가 데이터를 복사하는 것을 기다린 뒤 메시지를 수신했다는 응답을 합니다. |
| 재시도 요청 주기 | 100 | number | 전송 요청이 실패했을 때 재시도할 주기(ms)를 입력합니다. | [Kafka 공식 문서](https://kafka.apache.org/39/configuration/producer-configs/)의 `retry.backoff.ms` 속성 참고 |
| 재시도 횟수 | 2147483647 | number | 전송 요청이 실패했을 때 재시도할 최대 횟수를 입력합니다. | [Kafka 공식 문서](https://kafka.apache.org/39/configuration/producer-configs/)의 `retries` 속성 참고<br/>설정값을 초과하여 재시도하는 경우 데이터 유실이 발생할 수 있습니다. |
| 전달 보장 방식 | EXACTLY_ONCE | enum | 메시지 전달 보장 방식을 선택합니다. | AT_LEAST_ONCE: 메시지가 최소 한 번은 전달되지만, 장애 상황에서 중복이 발생할 수 있습니다. 중복 처리를 애플리케이션에서 직접 관리할 수 있거나, 중복이 허용되는 경우에 적합합니다.<br/><br/>EXACTLY_ONCE: 메시지가 정확히 한 번만 처리됩니다. 중복이 허용되지 않는 결제·정산 등 핵심 트랜잭션에 적합하지만, 내부적으로 트랜잭션을 사용하므로 처리량이 다소 낮아질 수 있습니다. |
| 추가 설정 | - | hash | Kafka 연결에 사용할 추가 Producer 설정을 입력합니다. | [Kafka 공식 문서](https://kafka.apache.org/39/configuration/producer-configs/) 참고 |

### 코덱별 출력 예제
지원 코덱
* [JSON 코덱](./codec-config-guide.md#json) - JSON 형식 데이터 파싱
* [LINE 코덱](./codec-config-guide.md#line) - 행 단위 메시지 처리

## Sink > (Apache) Kafka

### 노드 설명

Kafka에 데이터를 전송하는 노드입니다.

### 속성 설명

| 속성명         | 기본값          | 자료형    | 설명                                                 | 비고                                                                                                                                                                                                                                                                   |
|-------------|--------------|--------|----------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 토픽          | -            | string | 메시지를 전송할 Kafka 토픽 이름을 입력합니다.                       |                                                                                                                                                                                                                                                                      |
| 브로커 서버 목록   |              | string | Kafka 브로커 서버를 입력합니다. 서버가 여러 대일 경우 콤마(`,`)로 구분합니다.  | [Kafka 공식 문서](https://kafka.apache.org/39/configuration/producer-configs/)의 `bootstrap.servers` 속성 참고<br/>예: 10.100.1.1:9092,10.100.1.2:9092                                                                                                                         |
| 클라이언트 아이디   | dataflow   | string | Kafka Producer를 식별하는 ID를 입력합니다.                    | [Kafka 공식 문서](https://kafka.apache.org/39/configuration/producer-configs/)의 `client.id` 속성 참고                                                                                                                                                                        |
| 압축 유형       | none       | enum   | 전송하는 데이터를 압축할 방법을 입력합니다.                           | [Kafka 공식 문서](https://kafka.apache.org/39/configuration/topic-level-configs/)의 `compression.type` 속성 참고<br/>none, gzip, snappy, lz4, zstd 중 선택                                                                                                                       |
| 메시지 키       | -            | string | 메시지 키로 사용할 필드를 입력합니다.                              |                                                                                                                                                                                                                                                                      |
| 메타데이터 갱신 주기 | 300000     | number | 파티션, 브로커 서버 상태 등을 갱신할 주기(ms)를 입력합니다.               | [Kafka 공식 문서](https://kafka.apache.org/39/configuration/producer-configs/)의 `metadata.max.age.ms` 속성 참고                                                                                                                                                              |
| 최대 요청 크기    | 1048576    | number | 전송 요청당 최대 크기(byte)를 입력합니다.                         | [Kafka 공식 문서](https://kafka.apache.org/39/configuration/producer-configs/)의 `max.request.size` 속성 참고                                                                                                                                                                 |
| 서버 재연결 주기   | 50         | number | 브로커 서버에 연결이 실패했을 때 재시도할 주기(ms)를 입력합니다.             | [Kafka 공식 문서](https://kafka.apache.org/39/configuration/producer-configs/)의 `reconnect.backoff.ms` 속성 참고                                                                                                                                                             |
| 배치 크기       | 16384      | number | 배치 요청으로 전송할 크기(byte)를 입력합니다.                       | [Kafka 공식 문서](https://kafka.apache.org/39/configuration/producer-configs/)의 `batch.size` 속성 참고                                                                                                                                                                       |
| 버퍼 메모리      | 33554432   | number | Kafka 전송에 사용하는 버퍼의 크기(byte)를 입력합니다.                | [Kafka 공식 문서](https://kafka.apache.org/39/configuration/producer-configs/)의 `buffer.memory` 속성 참고                                                                                                                                                                    |
| 수신 버퍼 크기    | 32768      | number | 데이터를 읽는 데 사용하는 TCP receive 버퍼의 크기(byte)를 입력합니다.    | [Kafka 공식 문서](https://kafka.apache.org/39/configuration/producer-configs/)의 `receive.buffer.bytes` 속성 참고                                                                                                                                                             |
| 전송 지연 시간    | 0          | number | 메시지 전송을 지연할 시간을 입력합니다. 지연된 메시지는 배치 요청으로 한 번에 전송합니다. | [Kafka 공식 문서](https://kafka.apache.org/39/configuration/producer-configs/)의 `linger.ms` 속성 참고                                                                                                                                                                        |
| 서버 요청 타임아웃  | 30000      | number | 전송 요청에 대한 타임아웃(ms)을 입력합니다.                         | [Kafka 공식 문서](https://kafka.apache.org/39/configuration/producer-configs/)의 `request.timeout.ms` 속성 참고                                                                                                                                                               |
| 전송 버퍼 크기    | 131072     | number | 데이터를 전송하는 데 사용하는 TCP send 버퍼의 크기(byte)를 입력합니다.     | [Kafka 공식 문서](https://kafka.apache.org/39/configuration/producer-configs/)의 `send.buffer.bytes` 속성 참고                                                                                                                                                                |
| ack 속성      | all        | enum   | 브로커 서버에서 메시지를 받았는지 확인하는 설정을 입력합니다.                 | [Kafka 공식 문서](https://kafka.apache.org/39/configuration/producer-configs/)의 `acks` 속성 참고<br/>0 - 메시지 수신 여부를 확인하지 않습니다.<br/>1 - 토픽의 leader가 follower가 데이터를 복사하는 것을 기다리지 않고 메시지를 수신했다는 응답을 합니다.<br/>all - 토픽의 leader가 follower가 데이터를 복사하는 것을 기다린 뒤 메시지를 수신했다는 응답을 합니다. |
| 재시도 요청 주기   | 100        | number | 전송 요청이 실패했을 때 재시도할 주기(ms)를 입력합니다.                  | [Kafka 공식 문서](https://kafka.apache.org/39/configuration/producer-configs/)의 `retry.backoff.ms` 속성 참고                                                                                                                                                                 |
| 재시도 횟수      | 2147483647 | number | 전송 요청이 실패했을 때 재시도할 최대 횟수를 입력합니다.                   | [Kafka 공식 문서](https://kafka.apache.org/39/configuration/producer-configs/)의 `retries` 속성 참고<br/>설정값을 초과하여 재시도하는 경우 데이터 유실이 발생할 수 있습니다.                                                                                                                               |
| 전달 보장 방식 | EXACTLY_ONCE | enum | 메시지 전달 보장 방식을 선택합니다. | AT_LEAST_ONCE: 메시지가 최소 한 번은 전달되지만, 장애 상황에서 중복이 발생할 수 있습니다. 중복 처리를 애플리케이션에서 직접 관리할 수 있거나, 중복이 허용되는 경우에 적합합니다.<br/>EXACTLY_ONCE: 메시지가 정확히 한 번만 처리됩니다. 중복이 허용되지 않는 결제·정산 등 핵심 트랜잭션에 적합하지만, 내부적으로 트랜잭션을 사용하므로 처리량이 다소 낮아질 수 있습니다.                                        |
| 추가 설정       | -            | hash   | Kafka 연결에 사용할 추가 Producer 설정을 입력합니다.               | [Kafka 공식 문서](https://kafka.apache.org/39/configuration/producer-configs/) 참고                                                                                                                                                                                        |

### 코덱별 출력 예제

지원 코덱
* [JSON 코덱](./codec-config-guide.md#json) - JSON 형식 데이터 출력
* [LINE 코덱](./codec-config-guide.md#line) - 행 단위 메시지 출력

## Sink > Stdout

### 노드 설명

* 표준 출력으로 메시지를 출력하는 노드입니다.
* Source, Filter 노드에서 처리된 데이터를 확인할 때 유용하게 사용할 수 있습니다.

### 코덱별 출력 예제

지원 코덱
* [JSON 코덱](./codec-config-guide.md#json) - JSON 형식 데이터 출력
* [LINE 코덱](./codec-config-guide.md#line) - 행 단위 메시지 출력

## Branch

인입된 데이터의 값에 따라 흐름 분기를 정의하는 노드 유형입니다.

## Branch > IF

### 노드 설명

조건문으로 메시지를 필터링하는 노드입니다.

### 속성 설명

| 속성명 | 기본값 | 자료형 | 설명 | 비고 |
| --- | --- | --- | --- | --- |
| 조건문 | - | string | 메시지를 필터링할 조건을 입력합니다. | 아래 예제를 참고하세요. |

#### 사용 가능한 연산자
* 비교: ==, !=, <, >, <=, >=
* 정규식: =~ (우항에 주어진 패턴으로 좌항의 문자열을 검사)
* 포함: =~, !~, .contains()
* 논리 연산자: &&, ||, not
* 부정 연산자: !, not

### 필터링 예제 - first depth field reference

#### 조건
조건문 → `logLevel == "ERROR"`

#### 통과 메시지

``` json
{
    "logLevel": "ERROR"
}
```

#### 누락 메시지

``` json
{
    "logLevel": "INFO"
}
```

### 필터링 예제 - second depth field reference

#### 조건

조건문 → `response.status == 200` 또는 `response["status"] == 200`

#### 통과 메시지

``` json
{
    "response": {
        "status": 200
    }
}
```

#### 누락 메시지

``` json
{
    "response": {
        "status": 404
    }
}
```

## Branch > Dataset Split

### 노드 설명

* 이벤트를 설정된 비율에 따라 여러 분기로 분할하는 노드입니다.
* 머신러닝 데이터셋 분할(예: 학습/테스트/검증) 등의 목적으로 활용할 수 있습니다.
* 각 분기에는 하나의 하위 노드를 연결할 수 있습니다.

### 속성 설명

| 속성명 | 기본값 | 자료형 | 설명 | 비고 |
| --- | --- | --- | --- | --- |
| 시드 | - | number | 난수 생성 시 사용할 시드를 입력합니다. 시드가 같고 입력 메시지가 동일하다면 결과는 동일합니다. |  |
| 분할 설정 | - | hash | 분기 이름과 비율을 JSON 형식으로 입력합니다. 모든 비율의 합은 `1.0`이어야 합니다. | 예: `{"train": 0.6, "test": 0.3, "sampling": 0.1}` |

### 이벤트 분할 예제

#### 조건

* 시드 → `42`
* 분할 설정 → `{"train": 0.6, "test": 0.3, "sampling": 0.1}`

#### 동작

입력된 이벤트가 설정된 비율에 따라 각 분기로 전달됩니다.

* `train` 분기에 연결된 하위 노드: 전체 이벤트의 약 60%가 전달됩니다.
* `test` 분기에 연결된 하위 노드: 전체 이벤트의 약 30%가 전달됩니다.
* `sampling` 분기에 연결된 하위 노드: 전체 이벤트의 약 10%가 전달됩니다.
