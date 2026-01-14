## Data & Analytics > DataFlow > コーデック設定ガイド

## 概要

* コーデックはデータの入出力形式を決定する構成要素です。
* Sourceノードではデータ流入時、Sinkノードではデータ出力時にコーデックが適用されます。
* 多様なコーデックタイプに対応しており、各コーデックごとに固有の特性と使用例があります。

## 対応コーデックタイプ

### JSONコーデック

* JSON形式のデータをパースして各フィールドを個別に処理します。
* Source及びSinkノードともにJSONの全てのフィールドがそのまま維持されるため、フィルタリングや加工に有利です。

#### 条件

* 入力データ

```json
{"name": "DataFlow","type": "pipeline","status": "running","level": "info"}

```
* charset → `UTF-8`

#### 処理結果 

```json
{
  "name": "DataFlow",
  "type": "pipeline",
  "status": "running",
  "level": "info"
}

```

#### 伝達&出力データ

```json
{"name": "DataFlow","type": "pipeline","status": "running","level": "info"}

```

### plainコーデック

* 元データを文字列形式で処理します。
* Sourceノードでは入力データを`message`フィールドに文字列として保存し、Sinkノードではデータを元の文字列形式で出力します。
* 元の形式をそのまま保存したい場合に使用します。

#### plainコーデック例 - Sourceノード
##### 条件
* 入力データ
  ```json
  {"name": "DataFlow","type": "pipeline","status": "running","level": "info"} 
  ```
* charset → `UTF-8`

##### 処理結果
```text
{\"name\":\"DataFlow\",\"type\":\"pipeline\",\"status\":\"running\",\"level\":\"info\"}
```

##### 伝達データ
```json
{"message":"{\"name\":\"DataFlow\",\"type\":\"pipeline\",\"status\":\"running\",\"level\":\"info\"}"}
```

##### plainコーデック例 - Sinkノード

!!! tip "参考"
    エンジンV1の場合、`format`という追加項目を入力できます。<br>
    formatを入力しない場合、`%{timestamp} %{host} %{message}`形式で出力されます。<br>
    (`timestamp`, `host`はエンジンV1で基本提供されるパラメータです。)

###### 条件
* 入力メッセージ
  ```json
  {
    "name": "DataFlow",
    "type": "pipeline",
    "status": "running",
    "level": "info"
  } 
  ```
* charset → `UTF-8`
* format → `[%{level}] %{name} %{status}`

###### 処理結果
```text
[info] DataFlow running
```

###### 出力データ
```text
[info] DataFlow running
```

### lineコーデック

* 各行を1つのメッセージとして処理します。
* Sourceノードでは入力された各行を`message`フィールドに文字列として保存し、Sinkノードではデータをformatに従ってテキスト行として出力します。
* 行単位の処理が必要な場合に使用します。
* lineコーデックの場合、delimiterを通じて出力されるメッセージの区切り文字を定義できます。 
  * デフォルト値は改行(`\n`)です。

#### lineコーデック例 - Sourceノード

##### 条件
* 入力データ
  ```text
  {"name":"DataFlow","type":"pipeline","status":"running","level":"info"}
  ```
* charset → `UTF-8`

##### 処理結果
```text
{\"name\":\"DataFlow\",\"type\":\"pipeline\",\"status\":\"running\",\"level\":\"info\"}
```

##### 伝達データ
```json
{"message":"{\"name\":\"DataFlow\",\"type\":\"pipeline\",\"status\":\"running\",\"level\":\"info\"}"}
```

#### lineコーデック例 - Sinkノード

!!! tip "参考"
    エンジンV1の場合、formatを入力しない場合`%{timestamp} %{host} %{message}`形式で出力されます。<br>
    (`timestamp`, `host`はエンジンV1で基本提供されるパラメータです。)

##### 条件
* 入力メッセージ
  ```json
  {
    "name": "DataFlow",
    "type": "pipeline",
    "status": "running",
    "level": "info"
  } 
  ```
* delimiter → `\n`
* format → `[%{level}] %{name} %{status}`
* charset → `UTF-8`

##### 処理結果
```text
[info] DataFlow running
```

##### 出力データ
```text
[info] DataFlow running
```

### parquetコーデック

* Apache Parquet形式でデータを保存します。
* 多様な圧縮オプションに対応しており、大容量データの処理に適しています。
* 現在parquetコーデックは`エンジンV1のみ提供`され、エンジンV2では今後対応予定です。
* 提供圧縮形式: `SNAPPY(デフォルト値)`, `GZIP`, `BROTLI`, `LZ4`, `ZSTD`, `NONE` [圧縮形式参照](https://parquet.apache.org/docs/file-format/data-pages/compression/)

### debugコーデック

* デバッグ目的で使用されるコーデックで、開発及びデバッグ時に使用します。
* メッセージの内部構造とメタデータを含めて出力します。
* debugコーデックは`エンジンV1のみ提供`されます。

#### debugコーデック例
##### 条件
* 入力メッセージ
  ```json
  {
    "name": "DataFlow",
    "type": "pipeline",
    "status": "running",
    "level": "info"
  } 
  ```
##### 処理結果 
```text
{
    "name": "DataFlow",
    "type": "pipeline",
    "status": "running",
    "level": "info"
}
```

##### 出力メッセージ
```text
{
    "name" => "DataFlow",
    "type" => "pipeline",
    "status" => "running",
    "level" => "info"
    "@timestamp" => 2022-11-21T07:49:20.000Z,
    "@version" => "1",
    "host" => "b3e7b1b35c26",
}
```
