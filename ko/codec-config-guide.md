# Data & Analytics > DataFlow > 코덱(Codec) 설정 가이드

## 개요

* 코덱은 데이터의 입출력 형식을 결정하는 구성 요소입니다.
* Source 노드에서는 데이터 인입 시, Sink 노드에서는 데이터 출력 시 코덱이 적용됩니다.
* 다양한 코덱 타입을 지원하며, 각 코덱마다 고유한 특성과 사용 예시가 있습니다.

## 노드별 지원 코덱

### Source 노드

| 노드명                            | 지원 코덱             | 비고              |
|--------------------------------|-------------------|-----------------|
| (NHN Cloud) Log & Crash Search | json              |                 |
| (NHN Cloud) CloudTrail         | json              |                 |
| (NHN Cloud) Object Storage     | json, plain, line | line은 엔진 V1만 지원 |
| (Amazon) S3                    | json, plain, line | line은 엔진 V1만 지원 |
| JDBC                           | json              |                 |
| (Apache) Kafka                 | json, plain, line |                 |

### Sink 노드

| 노드명                        | 지원 코덱                      | 비고                        |
|----------------------------|----------------------------|---------------------------|
| (NHN Cloud) Object Storage | json, plain, line, parquet | plain, parquet는 엔진 V1만 지원 |
| (Amazon) S3                | json, plain, line, parquet | plain, parquet는 엔진 V1만 지원 |
| (Apache) Kafka             | json, plain, line          |                           |
| stdout                     | json, plain, line, debug   | plain, debug는 엔진 V1만 지원   |

## 지원 코덱 타입

### json 코덱

* JSON 형식의 데이터를 파싱하여 각 필드를 개별적으로 처리합니다.
* JSON의 모든 필드가 그대로 유지되어 필터링이나 가공에 유리합니다.
* 각 필드를 개별적으로 활용하고자 할 때 사용합니다.

#### 설정

* charset -> `UTF-8`

#### 입력 메시지

```json
{
  "name": "DataFlow",
  "type": "pipeline",
  "status": "running",
  "level": "info"
}

```

#### json 코덱 출력 결과 

```json
{
  "name": "DataFlow",
  "type": "pipeline",
  "status": "running",
  "level": "info"
}

```

### plain 코덱

* 원본 데이터를 문자열 형태로 `message` 필드에 저장합니다.
* JSON 데이터의 경우 전체 JSON을 문자열로 변환하여 저장됩니다.
* 원본 형태를 그대로 보존하고자 할 때 사용합니다.

#### plain 코덱 예제 - 기본
##### 조건
* 입력 메시지
  ```json
  {
    "name": "DataFlow",
    "type": "pipeline",
    "status": "running",
    "level": "info"
  } 
  ```
* charset -> `UTF-8`

##### 결과
```json
{
  "message": "{\"name\":\"DataFlow\",\"type\":\"pipeline\",\"status\":\"running\",\"level\":\"info\"}"
}
```

#### plain 코덱 예제 - format 기능 활용

!!! tip "참고"
    엔진 V1의 경우, `format`이라는 추가 항목을 입력할 수 있습니다.<br>
    format을 입력하지 않으면 `%{timestamp} %{host} %{message}` 형식으로 출력됩니다.<br>
    (`timestamp`, `host`는 엔진 V1에서 기본 제공하는 파라미터입니다.)

##### 조건
* 입력 메시지
  ```json
  {
    "name": "DataFlow",
    "type": "pipeline",
    "status": "running",
    "level": "info"
  } 
  ```
* charset -> `UTF-8`
* format -> `[%{level}] %{name} %{status}`

##### 결과
```text
[info] DataFlow running
```

### line 코덱

* 각 행을 하나의 메시지로 처리합니다.
* format 설정을 통해 출력 형식을 지정할 수 있습니다.
* 행 단위 처리가 필요할 때 사용합니다.

!!! tip "참고"
    엔진 V1의 경우, format을 입력하지 않으면 `%{timestamp} %{host} %{message}` 형식으로 출력됩니다.<br>
    (`timestamp`, `host`는 엔진 V1에서 기본 제공하는 파라미터입니다.)

#### line 코덱 예제

##### 조건
* 입력 메시지
  ```json
  {
    "name": "DataFlow",
    "type": "pipeline",
    "status": "running",
    "level": "info"
  } 
  ```
* delimiter -> `\n`
* format -> `[%{level}] %{name} %{status}`
* charset -> `UTF-8`

##### 결과
```text
[info] DataFlow running
```

### parquet 코덱

* Apache Parquet 형식으로 데이터를 저장합니다.
* 다양한 압축 옵션을 지원하며, 대용량 데이터 처리에 적합합니다.
* 현재 parquet 코덱은 `엔진 V1만 제공`되며, 엔진 V2에서는 추후 지원 예정입니다.
* 제공 압축 형식: `SNAPPY(기본값)`, `GZIP`, `BROTLI`, `LZ4`, `ZSTD`, `NONE` [압축 형식 참조](https://parquet.apache.org/docs/file-format/data-pages/compression/)

### debug 코덱

* 디버깅 목적으로 사용되는 코덱입니다.
* 메시지의 내부 구조와 메타데이터를 포함하여 출력합니다.
* debug 코덱은 `엔진 V1만 제공`됩니다.
* 개발 및 디버깅 시 사용합니다.

#### debug 코덱 예제
##### 조건
* 입력 메시지
  ```json
  {
    "name": "DataFlow",
    "type": "pipeline",
    "status": "running",
    "level": "info"
  } 
  ```

##### 결과
```text
{
    "name": "DataFlow",
    "type": "pipeline",
    "status": "running",
    "level": "info"
    "@timestamp" => 2022-11-21T07:49:20.000Z,
    "@version" => "1",
    "host" => "b3e7b1b35c26",
    "sequence" => 0
}
```