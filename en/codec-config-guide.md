## Data & Analytics > DataFlow > Codec Configuration Guide

## Overview

* A codec is a component that determines the input and output formats of data.
* Codecs are applied to Source nodes during data ingestion and to Sink nodes during data output.
* We support various codec types, each with its own unique characteristics and use cases.

## Supported codec type

### JSON codec

* It parses JSON data to process each field individually.
* Since all JSON fields are preserved intact at both Source and Sink nodes, it is highly effective for data filtering and transformation.

#### Condition

* Input data

```json
{"name": "DataFlow","type": "pipeline","status": "running","level": "info"}

```
* charset → `UTF-8`

#### Process result 

```json
{
  "name": "DataFlow",
  "type": "pipeline",
  "status": "running",
  "level": "info"
}

```

#### Delivery & output data

```json
{"name": "DataFlow","type": "pipeline","status": "running","level": "info"}

```

### plain codec

* Processes raw data in a string format.
* The Source node stores input data as a string in the message field, while the Sink node outputs the data in its original string format.
* Use this codec when you need to preserve the data in its original form.

#### plain codec example - Source node
##### Condition
* Input data
  ```json
  {"name": "DataFlow","type": "pipeline","status": "running","level": "info"} 
  ```
* charset → `UTF-8`

##### Process result
```text
{\"name\":\"DataFlow\",\"type\":\"pipeline\",\"status\":\"running\",\"level\":\"info\"}
```

##### Delivery data
```json
{"message":"{\"name\":\"DataFlow\",\"type\":\"pipeline\",\"status\":\"running\",\"level\":\"info\"}"}
```

##### plain codec example - Sink node

!!! tip "Note"
    For Engine V1, an optional format field can be specified.<br>
    If no format is defined, the output defaults to: `%{timestamp} %{host} %{message}`.<br>
    (`timestamp` and `host` are built-in parameters provided by Engine V1.)

###### Condition
* Input message
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

###### Process result
```text
[info] DataFlow running
```

###### Output data
```text
[info] DataFlow running
```

### line codec

* Processes each line as an individual message.
* The Source node stores each input line as a string in the `message` field, while the Sink node outputs data as text lines according to the specified format.
* Use this codec when line-by-line processing is required.
* For the Line codec, you can define a delimiter to separate output messages. 
  * The default value is a newline (`\n`).

#### line codec example - Source node

##### Condition
* Input data
  ```text
  {"name":"DataFlow","type":"pipeline","status":"running","level":"info"}
  ```
* charset → `UTF-8`

##### Process result
```text
{\"name\":\"DataFlow\",\"type\":\"pipeline\",\"status\":\"running\",\"level\":\"info\"}
```

##### Delivery data
```json
{"message":"{\"name\":\"DataFlow\",\"type\":\"pipeline\",\"status\":\"running\",\"level\":\"info\"}"}
```

#### line codec example - Sink node

!!! tip "Note"
    For Engine V1, if no format is defined, the output defaults to: `%{timestamp} %{host} %{message}`.<br>
    (`timestamp` and `host` are built-in parameters provided by Engine V1.)

##### Condition
* Input message
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

##### Process result
```text
[info] DataFlow running
```

##### Output data
```text
[info] DataFlow running
```

### parquet codec

* Saves data in the Apache Parquet format.
* Supports various compression options and is optimized for large-scale data processing.
* The Parquet codec is `exclusive to Engine V1`; Engine V2 support is planned for a future release.
* Supported compression formats: `SNAPPY (default)`, `GZIP`, `BROTLI`, `LZ4`, `ZSTD`, `NONE` [refer to the compression formats](https://parquet.apache.org/docs/file-format/data-pages/compression/)

### debug codec

* Designed for debugging purposes, this codec is intended for use during development and debugging.
* It outputs the complete internal structure of the message, including all associated metadata.
* The debug codec is currently `exclusive to Engine V1`.

#### debug codec example
##### Condition
* Input message
  ```json
  {
    "name": "DataFlow",
    "type": "pipeline",
    "status": "running",
    "level": "info"
  } 
  ```
##### Process result 
```text
{
    "name": "DataFlow",
    "type": "pipeline",
    "status": "running",
    "level": "info"
}
```

##### Output message
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