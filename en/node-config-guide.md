## Data & Analytics > DataFlow > Node Type Guide

* Node types are pre-defined templates for easy flow creation.
* Types of node types are Source, Filter, Branch, and Sink.
* It is recommended that Source, Sink Node type have to be tested to ensure that Endpoint information is valid.
* You must use the DataFlow IP fixation feature when connecting to data sources with access control enabled.
    * To use static DataFlow IP, contact us via **Customer Support > Contact Us**.

### Notes on Connecting to Object Storage
Multiple Object Storage instances cannot be used within the same flow if they share the same bucket name, even if they belong to different regions or projects.

!!! tip "Unsupported Configuration Examples"
    * Example 1
        * First Object Storage connection details
            * Region: KR1
            * Bucket name: Data
            * Project: TEST
        * Second Object Storage connection details
            * Region: JP1
            * Bucket name: Data
            * Project: TEST
        * Although the buckets are distinct due to being in different regions, they cannot be used together in a single DataFlow if they share the same name.
    * Example 2
        * First Object Storage connection details
            * Region: KR1
            * Bucket name: Data
            * Project: TEST_1
        * Second Object Storage connection details
            * Region: KR1
            * Bucket name: Data
            * Project: TEST_2
        * Although the buckets are distinct due to being in different projects, they cannot be used together in a single DataFlow if they share the same name.


## Domain Specific Language(DSL) Definition 

DSL definition is required to execute the flow.

### Variable

* `{{ executionTime }}`
    * Flow execution time
* Time unit
    * MINUTE - `{{ MINUTE }}`
    * HOUR - `{{ HOUR }}`
    * DAY - `{{ DAY }}`
    * MONTH - `{{ MONTH }}`
    * YEAR - `{{ YEAR }}`

### Filter

* `{{ time | startOf: unit }}`
    * Returns the start time of time zone defined by `unit` from the given time.
    * **Calculate based on Korean time.**
    * e.g., {{ executionTime | startOf: MINUTE }}
    * e.g., {{ "2022-11-04T13:31:28Z" | startOf: MINUTE }}
        * → 2022-11-04T13:31:00Z
    * e.g., {{ "2022-11-04T13:31:28Z" | startOf: HOUR }}
        * → 2022-11-04T13:00:00Z
    * e.g., {{ "2022-11-04T13:31:28Z" | startOf: DAY }}
        * → 2022-11-04T00:00:00Z
    * e.g., {{ "2022-11-04T13:31:28Z" | startOf: MONTH }}
        * → 2022-11-01T00:00:00Z
    * e.g., {{ "2022-11-04T13:31:28Z" | startOf: YEAR }}
        * → 2022-01-01T00:00:00Z
* `{{ time | endOf: unit }}`
    * Returns the last time of time zone defined by `unit` from the given time.
    * **Calculate based on Korean time.**
    * e.g., {{ executionTime | endOf: MINUTE }}
    * e.g., {{ "2022-11-04T13:31:28Z" | endOf: MINUTE }}
        * → 2022-11-04T13:31:59.999999999Z
    * e.g., {{ "2022-11-04T13:31:28Z" | endOf: HOUR }}
        * → 2022-11-04T13:59:59.999999999Z
    * e.g., {{ "2022-11-04T13:31:28Z" | endOf: DAY }}
        * → 2022-11-04T23:59:59.999999999Z
    * e.g., {{ "2022-11-04T13:31:28Z" | endOf: MONTH }}
        * → 2022-11-30T23:59:59.999999999Z
    * e.g., {{ "2022-11-04T13:31:28Z" | endOf: YEAR }}
        * → 2022-12-31T23:59:59.999999999Z
* `{{ time | subTime: delta, unit }}`
    * Returns the time subtracted by `delta` in the time zone defined by `unit` from the given time.
    * e.g., {{ executionTime | subTime: 10, MINUTE }}
    * e.g., {{ "2022-11-04T13:31:28Z" | subTime: 10, MINUTE }}
        * → 2022-11-04T13:21:28Z
* `{{ time | addTime: delta, unit }}`
    * Returns the time added by `delta` in the time zone defined by `unit` from the given time.
    * e.g., {{ executionTime | addTime: 10, MINUTE }}
    * e.g., {{ "2022-11-04T13:31:28Z" | addTime: 10, MINUTE }}
        * → 2022-11-04T13:41:28Z
* `{{ time | format: formatStr }}`
    * Returns the given time in the form `formatStr`.
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
    * e.g., {{ executionTime | format: 'yyyy' }}
    * e.g., {{ "2022-11-04T13:31:28Z" | format: 'yyyy' }}
        * → 2022
* nested filter example
    * DSL expression at 03:00 hour on the day the flow started
        * → {{ executionTime | startOf: DAY | addTime: 3, HOUR }}

## Input by Data Type
### string
Enter a string.

### number
* Enter a number greater or equal to 0.
* Use the arrow to the right of the input box to adjust the value by 1.

### boolean
Select `TRUE` or `FALSE` from the drop-down menu.

### enum
Select an item from the drop-down menu.

### array of strings
* Enter the strings that will go into the array one by one.
* After entering the string, click `+` to insert the string into the array.
* e.g., If you want to enter `["message" , "yyyy-MM-dd HH:mm:ssZ", "ISO8601"]`, insert the string into the array in the following order: `message`, `yyyy-MM-dd HH:mm:ssZ`, `ISO8601`.

### Hash
Enter a string in JSON format.

## Schema

### Overview

* If you define an output schema (field names and data types) in a Source node, only the defined fields are selectively read.
* The defined schema is automatically propagated to downstream nodes along the DAG graph.
* When entering fields in a Filter node, you can select fields defined in the schema from a dropdown.
* If no schema is defined, all fields are read as before.

### Supported Data Types

| Data type | Description |
|---|---|
| String | String |
| Integer | 32-bit integer |
| Long | 64-bit integer |
| Float | 32-bit floating point |
| Double | 64-bit floating point |
| Boolean | True/False |
| Timestamp | Date and time |
| Array | Array |

### Schema Definition

* You can define a schema in the **Codec** tab of the Source node.
* The schema can be defined in the Source node when using the following codecs:
    * JSON
* The PLAIN codec only allows defining the `message` field, as data is fixedly mapped to it.
* Configure the schema by adding field names and data types.
* When a schema is defined, only the defined fields are selectively parsed when the flow runs.

### Schema Propagation and Conversion

* The schema defined in a Source node is automatically propagated to connected downstream nodes.
* The schema is automatically converted based on the properties of the Filter node.

### Schema-Based Field Selection

* If schemas are defined in all upstream Source nodes, a dropdown with the field list is displayed when entering fields.
* If no schema is defined, fields are entered directly as text, as before.

## Source

Node type that defines an endpoint that imports data to the flow.

### Execution Mode

* The Source node has two execution modes, BATCH and STREAMING.
    * STREAMING mode: Processes data in real time without exiting the flow.
    * BATCH mode: Processes a set amount of data and then terminates the flow.
* The execution modes are supported differently by each source node.

## Common Settings on Source Node

| Property Name | Default Value | Data Type | Description | Notes |
| --- | --- | --- | --- | --- |
| ID | - | string | Sets the node ID.<br/>The value defined in this property is used as the node name on the chart board. |  |

## Source > (NHN Cloud) Log & Crash Search

### Node Description

* (NHN Cloud) Log & Crash Search Node is node that reads logs from Log & Crash Search.
* You can set the log query start time for a node. If not set, the log is read from the start of the flow.
* If no end time is entered in the node, logs are read in streaming format. If an end time is entered, logs are read up to the end time and the flow ends.
* Currently, session logs and crash logs are not supported.
* Affected by tokens from Log & Crash Search's Log Search API.
    * If you don't have enough tokens, you need to contact Log & Crash Search service.

### Execution Mode
* STREAMING: Continues processing data after the `Query Start time`.
* BATCH: Processes all data that falls between the `Query Start time` and the `Query End time` and ends the flow.


### Property Description 

| Property Name     | Default Value       | Data Type | Description                                                                                                                                                                                              | Notes |
|-----------|---------------------|--------|--------------------------------------------------------------------------------------------------------------------------------------------------------|----|
| Appkey    | -                   | string | Enter the app key for Log & Crash Search.                                                                                                                         |    |
| SecretKey | -                   | string | Enter the secret key for Log & Crash Search.                                                                                                                       |    |
| Query Start Time  | {{executionTime}} | string | Enter the start time for the log query. Must be entered in ISO 8601 format with offset or [DSL](#domain-specific-languagedsl) format. <br/>e.g., 2025-07-23T11:23:00+09:00, {{ executionTime }} |    |
| Query End Time    | -                   | string | Enter the end time for the log query. Must be entered in ISO 8601 format with offset or [DSL](#domain-specific-languagedsl) format. <br/>e.g., 2025-07-23T11:23:00+09:00, {{ executionTime }} |    |
| Search Query      | *                   | string | Enter the search query to use when making a query request to Log & Crash Search. For detailed query syntax, refer to the 'Lucene Query Guide' in the Log & Crash Search service.                                             |    |

### Message imported by codec

* Log & Crash Search is designed to process data in JSON format by default.
* If you want to use each field in the Log&Crash Search log, we recommend using the JSON codec.

Supported codec:
* [JSON codec](./codec-config-guide.md#json) - JSON data parsing

## Source > (NHN Cloud) CloudTrail

### Node Description

* (NHN Cloud) CloudTrail is a node that reads data from CloudTrail.
* You can set the data query start time for a node. If not set, data is read from the start of the flow.
* If no end time is entered in the node, data is read in streaming format. If an end time is entered, the data up to the end time is read and the flow ends.

### Execution Mode

* STREAMING: Continues processing data after the `Query Start time`.
* BATCH: Processes all data that falls between the `Query Start time` and the `Query End time` and ends the flow.

### Property Description 

| Property Name      | Default Value       | Data Type | Description                                                                                                                                                                                              | Notes |
|--------------------|---------------------|--------|---------------------------------------------------------------------------------------------------------------------------------------------------------|----|
| Appkey             | -                   | string | Enter the app key for CloudTrail.                                                                                                                                  |    |
| User Access Key ID | -                   | string | Enter the User Access Key ID of the user account.                                                                                                                      |    |
| Secret Access Key  | -                   | string | Enter the User Secret Key of the user account.                                                                                                                         |    |
| Query Start Time   | {{executionTime}} | string | Enter the start time for the data query. Must be entered in ISO 8601 format with offset or [DSL](#domain-specific-languagedsl) format. <br/>e.g., 2025-07-23T11:23:00+09:00, {{ executionTime }} |    |
| Query End Time     | -                   | string | Enter the end time for the data query. Must be entered in ISO 8601 format with offset or [DSL](#domain-specific-languagedsl) format. <br/>e.g., 2025-07-23T11:23:00+09:00, {{ executionTime }} |    |
| Event Type         | *                 | string | Enter the event ID to query.                                                                                                                                      |    |

### Message imported by codec

* CloudTrail is designed to process data in JSON format by default.
* If you want to use each field in CloudTrail data, we recommend using json Codec.

Supported codec:
* [JSON codec](./codec-config-guide.md#json) - JSON data parsing

## Source > (NHN Cloud) Object Storage

### Node Description

* Node that receives data from Object Storage of NHN Cloud.
* Based on the object creation time, data is read from the object created the earliest.

### Execution Mode
* STREAMING: Updates the object list on each `list update cycle`and processes data by reading newly added objects.
* BATCH: Fetches the object list once at the beginning of the flow, reads the objects, processes the data, and ends the flow.

### Property Description 

| Property Name | Default Value | Data Type | Description | Notes |
| --- |---------| --- | --- | --- |
| Bucket | -       | string | Enter the bucket name to read data from. |  |
| Region | -       | string | Enter the region information configured for the storage. |  |
| Secret Key | -       | string | Enter the secret key of the credentials issued by S3. |  |
| Access Key | -       | string | Enter the access key of the credentials issued by S3. |  |
| List Refresh Interval | 60    | number | Enter the refresh interval for the list of objects in the bucket. |  |
| Prefix | -       | string | Enter the prefix of the objects to read. |  |
| Exclude Key Pattern | -       | string | Enter the pattern of objects to exclude from reading. |  |

### Message imported by codec

Supported codec:
* [PLAIN codec](./codec-config-guide.md#plain) - Raw data string storage
* [JSON codec](./codec-config-guide.md#json) - JSON data parsing

## Source > (NHN Cloud) Data Lake Storage

### Node Description
* Node that receives data from Data Lake Storage of NHN Cloud.

### Execution Mode
* STREAMING: Updates the object list on each `list update cycle` and processes data by reading newly added objects.
* BATCH: Fetches the object list once at the beginning of the flow, reads the objects, processes the data, and ends the flow.

### Property Description
| Property name | Default value | Data type | Description | Others |
| --- |---------| --- | --- | --- |
| Bucket | -       | string | Enter a bucket name to read data. |  |
| Region | -       | string | 	
Enter region information configured in the storage. |  |
| Secret key | -       | string | Enter your S3 credentials secret key. |  |
| Access key | -       | string | Enter your S3 credentials access key. |  |
| List update cycle | 60    | number | Enter the object list update cycle included in the bucket. |  |
| Prefix | -       | string | 	
Enter a prefix of an object to read. |  |
| Key pattern to exclude | -       | string | Enter a pattern of an object not to read. |  |

### Message Ingestion by Codec Type
Supported codecs
* [PLAIN codec](./codec-config-guide.md#plain) - Raw data string storage
* [JSON codec](./codec-config-guide.md#json) - JSON data parsing

## Source > (Amazon) S3

### Node Description

* Node for uploading data to Amazon S3.
* Based on the object creation time, data is read from the object created the earliest.

### Execution Mode
* STREAMING: Updates the object list on each `list update cycle`and processes data by reading newly added objects.
* BATCH: Updates the object list once at the start of the flow, then reads the objects, processes the data, and ends the flow.

### Property Description 

| Property Name | Default Value | Data Type | Description | Notes |
|---------------|--------------------------------|---------|------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Endpoint      | -                              | string  | Enter the S3 storage endpoint.                                                                        | Only HTTP and HTTPS URL formats are accepted.                                                                                                                                                                 |
| Bucket        | -                              | string  | Enter the bucket name to read data from.                                                              |                                                                                                                                                                                                              |
| Region        | -                              | string  | Enter the region information configured for the storage.                                              |                                                                                                                                                                                                              |
| Secret Key    | -                              | string  | Enter the secret key of the credentials issued by S3.                                                 |                                                                                                                                                                                                              |
| Access Key    | -                              | string  | Enter the access key of the credentials issued by S3.                                                 |                                                                                                                                                                                                              |
| List Refresh Interval | 60                   | number  | Enter the refresh interval for the list of objects in the bucket.                                     |                                                                                                                                                                                                              |
| Prefix        | -                              | string  | Enter the prefix of the objects to read.                                                              |                                                                                                                                                                                                              |
| Exclude Key Pattern | -                        | string  | Enter the pattern of objects to exclude from reading.                                                 |                                                                                                                                                                                                              |
| Path-style Request | false                   | boolean | Determine whether to use path-style requests.                                                        |                                                                                                                                                                                                              |

!!! danger "Caution"
    * If you connect to NHN Cloud Object Storage using the (Amazon) S3 node, **Path-style Request** must be set to `true`.


### Message imported by codec

Supported codec:
* [PLAIN codec](./codec-config-guide.md#plain) - Raw data string storage
* [JSON codec](./codec-config-guide.md#json) - JSON data parsing

## Source > (NHN Cloud) EasyQueue

### Node Decription
Node that receives data from EasyQueue of NHN Cloud.

### Execution Mode
STREAMING: Processes data every time a new message arrives in a queue.

### Property Description
| Property name | Default value | Data type | Description | Others |
| --- | --- | --- | --- | --- |
| Appkey | - | string | Enter the appkey for EasyQueue. |  |
| User Access Key ID | - | string | Enter the User Access Key ID for the user account. |  |
| Secret Access Key | - | string | Enter the User Secret Key for the user account. |  |
| Broker server list | - | string | Enter the Kafka broker servers. If there are multiple servers, separate them with a comma (`,`). | See `bootstrap.servers` in the [Kafka official documentation](https://kafka.apache.org/39/configuration/consumer-configs/) <br/>Example: 10.100.1.1:9092,10.100.1.2:9092 |
| Consumer group ID | dataflow | string | Enter the ID that identifies the Kafka Consumer Group. | See `group.id` in the [Kafka official documentation](https://kafka.apache.org/39/configuration/consumer-configs/) |
| Topic list | - | array of strings | Enter the list of Kafka topics to receive messages from. |  |
| Topic pattern | - | string | Enter the Kafka topic pattern to receive messages from. | Example: `*-messages` |
| Exclude internal topics | true | boolean | Excludes internal topics such as __consumer_offsets. | See `exclude.internal.topics` in the [Kafka official documentation](https://kafka.apache.org/39/configuration/consumer-configs/) <br/>Excludes internal topics such as `__consumer_offsets` from the list of topics to receive. |
| Client ID | dataflow | string | Enter the ID that identifies the Kafka Consumer. | See `client.id` in the [Kafka official documentation](https://kafka.apache.org/39/configuration/consumer-configs/) |
| Isolation level | read_committed | enum | Determines whether the consumer reads messages from uncommitted transactions or only committed messages. | See `isolation.level` in the [Kafka official documentation](https://kafka.apache.org/39/configuration/consumer-configs/)<br/>read_uncommitted: Reads all messages in offset order.<br/>read_committed: Reads only messages from committed transactions. |
| Partition assignment strategy | ["RANGE", "COOPERATIVE_STICKY"] | array of strings | Determines how partitions are assigned to the consumer group when receiving messages from Kafka. | See `partition.assignment.strategy` in the [Kafka official documentation](https://kafka.apache.org/39/configuration/consumer-configs/) <br/>org.apache.kafka.clients.consumer.RangeAssignor<br/>org.apache.kafka.clients.consumer.RoundRobinAssignor<br/>org.apache.kafka.clients.consumer.StickyAssignor<br/>org.apache.kafka.clients.consumer.CooperativeStickyAssignor |
| Offset setting | latest | enum | Enter the criteria for setting the consumer group offset. | See `auto.offset.reset` in the [Kafka official documentation](https://kafka.apache.org/39/configuration/consumer-configs/) <br/>All settings below retain the existing offset if the consumer group already exists.<br/>none: Returns an error if the consumer group does not exist.<br/>earliest: Initializes to the oldest offset of the partition if the consumer group does not exist.<br/>latest: Initializes to the most recent offset of the partition if the consumer group does not exist. |
| Key deserializer type | STRING | enum | Enter the type of the key of the received message. | See `key.deserializer` in the [Kafka official documentation](https://kafka.apache.org/39/configuration/consumer-configs/) |
| Generate metadata | false | boolean | If the property value is true, metadata fields are generated for the message. Metadata is generated in the `kafka_metadata` field. | The following fields are generated:<br/>topic: The topic from which the message was received<br/>groupId: The consumer group ID used to receive the message<br/>partition: The partition number of the topic from which the message was received<br/>offset: The offset of the partition from which the message was received<br/>key: The message key |
| Fetch minimum size | 1 | number | Enter the minimum size (in bytes) of data to retrieve in a single fetch request. | See `fetch.min.bytes` in the [Kafka official documentation](https://kafka.apache.org/39/configuration/consumer-configs/) |
| Send buffer size | 131072 | number | Enter the size (in bytes) of the TCP send buffer used to transmit data. | See `send.buffer.bytes` in the [Kafka official documentation](https://kafka.apache.org/39/configuration/consumer-configs/) |
| Retry request interval | 100 | number | Enter the interval (in ms) for retrying when a transmission request fails. | See `retry.backoff.ms` in the [Kafka official documentation](https://kafka.apache.org/39/configuration/consumer-configs/) |
| Cyclic redundancy check | true | boolean | Checks the CRC of messages. | See `check.crcs` in the [Kafka official documentation](https://kafka.apache.org/39/configuration/consumer-configs/) |
| Server reconnect interval | 50 | number | Enter the interval (in ms) for retrying when a connection to the broker server fails. | See `reconnect.backoff.ms` in the [Kafka official documentation](https://kafka.apache.org/39/configuration/consumer-configs/) |
| Max fetch size per partition | 1048576 | number | Enter the maximum size (in bytes) of data to retrieve per partition in a single fetch request. | See `max.partition.fetch.bytes` in the [Kafka official documentation](https://kafka.apache.org/39/configuration/consumer-configs/) |
| Server request timeout | 30000 | number | Enter the timeout (in ms) for transmission requests. | See `request.timeout.ms` in the [Kafka official documentation](https://kafka.apache.org/39/configuration/consumer-configs/) |
| TCP receive buffer size | 65536 | number | Enter the size (in bytes) of the TCP receive buffer used to read data. | See `receive.buffer.bytes` in the [Kafka official documentation](https://kafka.apache.org/39/configuration/consumer-configs/) |
| Session timeout | 45000 | number | Enter the session timeout (in ms) for the consumer.<br/>If the consumer fails to send a heartbeat within this time, it is removed from the consumer group. | See `session.timeout.ms` in the [Kafka official documentation](https://kafka.apache.org/39/configuration/consumer-configs/) |
| Max poll records | 500 | number | Enter the maximum number of messages to retrieve in a single poll request. | See `max.poll.records` in the [Kafka official documentation](https://kafka.apache.org/39/configuration/consumer-configs/) |
| Max poll interval | 300000 | number | Enter the maximum interval (in ms) between poll requests. | See `max.poll.interval.ms` in the [Kafka official documentation](https://kafka.apache.org/39/configuration/consumer-configs/) |
| Fetch maximum size | 52428800 | number | Enter the maximum size (in bytes) of data to retrieve in a single fetch request. | See `fetch.max.bytes` in the [Kafka official documentation](https://kafka.apache.org/39/configuration/consumer-configs/) |
| Fetch maximum wait time | 500 | number | Enter the wait time (in ms) before sending a fetch request when the amount of data has not reached the `Fetch minimum size` setting. | See `fetch.max.wait.ms` in the [Kafka official documentation](https://kafka.apache.org/39/configuration/consumer-configs/) |
| Consumer health check interval | 3000 | number | Enter the interval (in ms) at which the consumer sends heartbeats. | See `heartbeat.interval.ms` in the [Kafka official documentation](https://kafka.apache.org/39/configuration/consumer-configs/) |
| Metadata refresh interval | 300000 | number | Enter the interval (in ms) for refreshing partition and broker server status. | See `metadata.max.age.ms` in the [Kafka official documentation](https://kafka.apache.org/39/configuration/producer-configs/) |
| IDLE timeout | 540000 | number | Enter the wait time (in ms) before closing connections with no data transmission. | See `connections.max.idle.ms` in the [Kafka official documentation](https://kafka.apache.org/39/configuration/consumer-configs/) |
| Additional settings | - | hash | Enter additional Consumer settings to use for the Kafka connection. | See the [Kafka official documentation](https://kafka.apache.org/39/configuration/consumer-configs/) |

### Message Ingestion by Codec Type
Supported codecs
* [PLAIN codec](./codec-config-guide.md#plain) - Raw data string storage
* [JSON codec](./codec-config-guide.md#json) - JSON data parsing

## Source > (Apache) Kafka

### Node Description

Node that receives data from Kafka.

### Execution Mode
STREAMING: Processes data every time a new message arrives in a topic.

!!! danger "Caution"
    * Kafka nodes do not support BATCH mode.

### Property Description 

| Property Name | Default Value | Data Type | Description | Notes |
|------------------|-----------------------------------|------------------|---------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Broker Server List | - | string | Enter the Kafka broker servers. Separate multiple servers with a comma (`,`). | Refer to the `bootstrap.servers` property in the [Kafka documentation](https://kafka.apache.org/39/configuration/consumer-configs/). <br/>e.g., 10.100.1.1:9092,10.100.1.2:9092 |
| Consumer Group ID | dataflow | string | Enter the ID to identify the Kafka Consumer Group. | Refer to the `group.id` property in the [Kafka documentation](https://kafka.apache.org/39/configuration/consumer-configs/). |
| Topic List | - | array of strings | Enter the list of Kafka topics to receive messages from. |                                                                                                                                                                                                                                                                                                                                                      |
| Topic Pattern | - | string | Enter the Kafka topic pattern to receive messages from. | e.g., `*-messages` |
| Exclude Internal Topics | true | boolean | Excludes internal topics such as __consumer_offsets. | Refer to the `exclude.internal.topics` property in the [Kafka documentation](https://kafka.apache.org/39/configuration/consumer-configs/). <br/>Excludes internal topics such as `__consumer_offsets` from the list of topics to receive messages from. |
| Client ID | `dataflow` | string | Enter the ID to identify the Kafka Consumer. | Refer to the `client.id` property in the [Kafka documentation](https://kafka.apache.org/39/configuration/consumer-configs/). |
| Partition Assignment Strategy | ["RANGE", "COOPERATIVE_STICKY"] | array of strings | Determines how partitions are assigned to the consumer group when receiving messages from Kafka. | Refer to the `partition.assignment.strategy` property in the [Kafka documentation](https://kafka.apache.org/39/configuration/consumer-configs/). <br/>org.apache.kafka.clients.consumer.RangeAssignor<br/>org.apache.kafka.clients.consumer.RoundRobinAssignor<br/>org.apache.kafka.clients.consumer.StickyAssignor<br/>org.apache.kafka.clients.consumer.CooperativeStickyAssignor |
| Offset Configuration | latest | enum | Enter the criteria for configuring the consumer group offset. | Refer to the `auto.offset.reset` property in the [Kafka documentation](https://kafka.apache.org/39/configuration/consumer-configs/). <br/>All settings below retain the existing offset if the consumer group already exists.<br/>none: Returns an error if the consumer group does not exist.<br/>earliest: Initializes to the oldest offset of the partition if the consumer group does not exist.<br/>latest: Initializes to the latest offset of the partition if the consumer group does not exist. |
| Offset Commit Interval | 5000 | number | Enter the interval (ms) for updating the consumer group offset. | Refer to the `auto.commit.internal.ms` property in the [Kafka documentation](https://kafka.apache.org/39/configuration/consumer-configs/). |
| Key Deserialization Type | `STRING` | enum | Enter the type of the key of the received message. | Refer to the `key.deserializer` property in the [Kafka documentation](https://kafka.apache.org/39/configuration/consumer-configs/). |
| Generate Metadata | false | boolean | If the property value is true, generates metadata fields for the message. Metadata is generated in the `kafka_metadata` field. | The following fields are generated:<br/>topic: Topic from which the message was received<br/>groupId: Consumer group ID used to receive the message<br/>partition: Partition number of the topic from which the message was received<br/>offset: Offset of the partition from which the message was received<br/>key: Message key |
| Minimum Fetch Size | 1 | number | Enter the minimum size (bytes) of data to retrieve in a single fetch request. | Refer to the `fetch.min.bytes` property in the [Kafka documentation](https://kafka.apache.org/39/configuration/consumer-configs/). |
| Send Buffer Size | 131072 | number | Enter the size (bytes) of the TCP send buffer used for data transmission. | Refer to the `send.buffer.bytes` property in the [Kafka documentation](https://kafka.apache.org/39/configuration/consumer-configs/). |
| Retry Request Interval | 100 | number | Enter the interval (ms) for retrying a failed transmission request. | Refer to the `retry.backoff.ms` property in the [Kafka documentation](https://kafka.apache.org/39/configuration/consumer-configs/). |
| Cyclic Redundancy Check | true | boolean | Checks the CRC of the message. | Refer to the `check.crcs` property in the [Kafka documentation](https://kafka.apache.org/39/configuration/consumer-configs/). |
| Server Reconnect Interval | 100 | number | Enter the interval (ms) for retrying a failed connection to the broker server. | Refer to the `reconnect.backoff.ms` property in the [Kafka documentation](https://kafka.apache.org/39/configuration/consumer-configs/). |
| Maximum Fetch Size per Partition | 1048576 | number | Enter the maximum size (bytes) to retrieve per partition in a single fetch request. | Refer to the `max.partition.fetch.bytes` property in the [Kafka documentation](https://kafka.apache.org/39/configuration/consumer-configs/). |
| Server Request Timeout | 30000 | number | Enter the timeout (ms) for a transmission request. | Refer to the `request.timeout.ms` property in the [Kafka documentation](https://kafka.apache.org/39/configuration/consumer-configs/). |
| TCP Receive Buffer Size | 65536 | number | Enter the size (bytes) of the TCP receive buffer used for reading data. | Refer to the `receive.buffer.bytes` property in the [Kafka documentation](https://kafka.apache.org/39/configuration/consumer-configs/). |
| Session Timeout | 45000 | number | Enter the consumer session timeout (ms).<br/>If the consumer fails to send a heartbeat within this time, it is removed from the consumer group. | Refer to the `session.timeout.ms` property in the [Kafka documentation](https://kafka.apache.org/39/configuration/consumer-configs/). |
| Maximum Poll Message Count | 500 | number | Enter the maximum number of messages to retrieve in a single poll request. | Refer to the `max.poll.records` property in the [Kafka documentation](https://kafka.apache.org/39/configuration/consumer-configs/). |
| Maximum Poll Interval | 300000 | number | Enter the maximum interval (ms) between poll requests. | Refer to the `max.poll.interval.ms` property in the [Kafka documentation](https://kafka.apache.org/39/configuration/consumer-configs/). |
| Maximum Fetch Size | 52428800 | number | Enter the maximum size (bytes) to retrieve in a single fetch request. | Refer to the `fetch.max.bytes` property in the [Kafka documentation](https://kafka.apache.org/39/configuration/consumer-configs/). |
| Maximum Fetch Wait Time | 500 | number | Enter the wait time (ms) before sending a fetch request when the amount of data specified in `Minimum Fetch Size` has not been accumulated. | Refer to the `fetch.max.wait.ms` property in the [Kafka documentation](https://kafka.apache.org/39/configuration/consumer-configs/). |
| Consumer Health Check Interval | 3000 | number | Enter the interval (ms) at which the consumer sends heartbeats. | Refer to the `heartbeat.interval.ms` property in the [Kafka documentation](https://kafka.apache.org/39/configuration/consumer-configs/). |
| Metadata Refresh Interval | 300000 | number | Enter the interval (ms) for refreshing partition and broker server status. | Refer to the `metadata.max.age.ms` property in the [Kafka documentation](https://kafka.apache.org/39/configuration/producer-configs/). |
| IDLE Timeout | 540000 | number | Enter the wait time (ms) before closing a connection with no data transmission. | Refer to the `connections.max.idle.ms` property in the [Kafka documentation](https://kafka.apache.org/39/configuration/consumer-configs/). |
| Isolation level | read_committed | enum | Determines whether the consumer reads messages from uncommitted transactions or only committed messages. | [Kafka official documentation](https://kafka.apache.org/39/configuration/consumer-configs/)'s `isolation.level` property<br/>read_uncommitted: Reads all messages in offset order.<br/>read_committed: Reads only messages from committed transactions. |
| Additional Configuration | - | hash | Enter additional Consumer configuration to use for the Kafka connection. | Refer to the [Kafka documentation](https://kafka.apache.org/39/configuration/consumer-configs/). |

### Message imported by codec

Supported codec:
* [PLAIN codec](./codec-config-guide.md#plain) - Raw data string storage
* [JSON codec](./codec-config-guide.md#json) - JSON format data parsing

## Filter

Node type that defines how to handle imported data.

## Common Settings on Filter Node

| Property Name | Default Value | Data Type | Description | Notes |
| --- | --- | --- | --- | --- |
| ID | - | string | Sets the node ID.<br/>The value defined in this property is used as the node name on the chart board. |  |

## Filter > Cipher

### Node Description

* Node for decrypting message field values.
* For the encryption key, refer to symmetric keys in Secure Key Manager.
    * Secure Key Manager symmetric keys can be created through the Secure Key Manager web console or Add Key API in Secure Key Manager.
    * Even if a flow contains multiple Cipher nodes, all Cipher nodes can only refer to one Secure Key Manager's key reference.

### Property Description 

| Property Name | Default Value | Data Type | Description | Notes |
|--------|---------|---------|-------------------------------------------|------------------|
| Mode | - | enum | Select between encryption mode and decryption mode. | Select one from the list. |
| App Key | - | string | Enter the SKM app key that stores the key to use for encryption/decryption. | |
| Key ID | - | string | Enter the SKM key ID that stores the key to use for encryption/decryption. | |
| Key Version | - | string | Enter the SKM key version that stores the key to use for encryption/decryption. | |
| Source Field | - | string | Enter the field name to encrypt/decrypt. | Whether a dropdown is provided when a schema is defined |
| Output Field | - | string | Enter the field name to store the encryption/decryption result. | |
| Overwrite | false | boolean | Select whether to overwrite the value if it already exists in the specified target field. | |

### Encrypt example exercise 

#### condition

* mode → `encrypt`
* Appkey → `SKM appkey`
* Key ID → `SKM Symmetric key ID`
* Key Version → `1`
* Source Field → message
* Field to be  stored → encrypted_message

#### Input message

``` js
{ 
    "message": "this is plain message" 
}
```

#### Output message

``` js
{ 
    "message": "this is plain message", 
    "encrypted_message": "oZA6CHd4OwjPuS+MW0ydCU9NqbPQHGbPf4rll2ELzB8y5pyhxF6UhWZq5fxrt0/e" 
}
```

### decrypt example

#### condition

* mode → `decrypt`
* Appkey → `SKM appkey`
* Key ID → `SKM Symmetric key ID`
* Key Version → `1`
* Source Field → message
* Field to be  stored → decrypted_message

#### Input message

``` js
{ 
    "message": "oZA6CHd4OwjPuS+MW0ydCU9NqbPQHGbPf4rll2ELzB8y5pyhxF6UhWZq5fxrt0/e" 
}
```

#### Output message

``` js
{ 
    "message": "oZA6CHd4OwjPuS+MW0ydCU9NqbPQHGbPf4rll2ELzB8y5pyhxF6UhWZq5fxrt0/e",
    "decrypted_message": "this is plain message"
}
```

## Filter > CSV

### Node Description
Node that parses a message in CSV format and stores it in a field.

### Property Description

| Property Name | Default Value | Data Type | Description | Notes |
|-----------|--------------------------|------------------|------------------------------------------------|--------------------------|
| Output Field | - | string | Enter the field name to store the CSV parsing result. | |
| Quote | " | string | Enter the character used to delimit column fields. | |
| Ignore First Row | false | boolean | If the property value is true, ignores the column names entered in the first row of the read data. | |
| Delimiter | , | string | Enter the string used to separate columns. | |
| Source Field | - | string | Enter the field name to parse as CSV. | |
| Schema | - | hash | Enter the name and data type of each column in dictionary format. | Refer to `How to Enter a Schema` |
| Overwrite | false | boolean | If true, overwrites the output field or existing fields if the CSV parsing result conflicts with them. | |
| Delete Source Field | false | boolean | Deletes the source field when CSV parsing is complete. Retains the field if parsing fails. | |

#### How to Enter a Schema
Column types are not supported. All columns and data types are entered as a schema.


### Example of CSV parsing without data type conversion

#### Condition

* Source field → `message`
* Schema → `{"one": "string", "two": "string", "t hree": "string"}`

#### Input messages

```js
{
    "message": "hey,foo,\"bar baz\""
}
```

#### Output message

```js
{
    "message": "hey,foo,\"bar baz\"",
    "one": "hey",
    "t hree": "bar baz",
    "two": "foo"
}
```


### Examples of CSV parsing that requires data type conversion

#### Condition

* Source field → `message`
* Schema → `{"one": "string", "two": "integer", "t hree": "boolean"}`

#### Input messages

```js
{
    "message": "\"wow hello world!\", 2, false"
}
```

#### Output message

```js
{
    "message": "\"wow hello world!\", 2, false",
    "one": "wow hello world!",
    "t hree": false,
    "two": 2
}
```

## Filter > JSON

### Node Description

Node that parses a JSON string and stores it in a specified field.

### Property Description 

| Property Name | Default Value | Data Type | Description | Notes |
| --- | --- | --- | --- | --- |
| Source Field | - | string | Enter the field name to parse as a JSON string. |  |
| Output Field | - | string | Enter the field name to store the JSON parsing result.<br/>If no property value is specified, the result is stored in the root field. |  |
| Overwrite | `false` | boolean | If true, overwrites the output field or existing fields if the JSON parsing result conflicts with them. |  |
| Delete Source Field | `false` | boolean | Deletes the source field when JSON parsing is complete. Retains the field if parsing fails. |  |
| Schema | - | hash | Enter the name and data type of each column in dictionary format. | Refer to `How to Enter a Schema` |

#### How to Enter a Schema
Column types are not supported. All columns and their data types must be entered as a schema.

### Example of CSV Parsing without Data Type Conversion

#### Condition

* Source field → `message`
* Output field → `json_parsed_message`

#### Input message

```js
{
    "message": "{\"json\": \"parse\", \"example\": \"string\"}"
}
```

#### Output message

```js
{
    "json_parsed_message": {
        "json": "parse",
        "example": "string"
    },
    "message": "{\"json\": \"parse\", \"example\": \"string\"}"
}
```

### Example of CSV parsing with data type conversion

#### Condition

* Source Field → `message`
* Field to Save → `json_parsed_message`
* Schema → `{"json": "string", "example": "integer"}`

#### Input Message

```js
{
    "message": "{\"json\": \"parse\", \"example\": \"123\"}"
}
```

#### Output Message

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

### Node Description

A node that parses a date string and stores it in timestamp format.

### Property Description

| Property Name | Default Value | Data Type | Description | Notes |
|--------|----------------------------------|------------------|--------------------------------------|-----------------------------------------------------------------|
| Source Field | - | string | Enter the field name to retrieve the string from. | Whether a dropdown is provided when a schema is defined |
| Format | - | array of strings | Enter the format to retrieve the string. | The predefined formats are as follows:<br/>ISO8601, UNIX, UNIX_MS |
| Locale | ko_KR | string | Enter the locale to use for parsing the date string. | e.g., en, en-US, ko_KR |
| Output Field | - | string | Enter the field name to store the date string parsing result. | |
| Timezone | Asia/Seoul | string | Enter the timezone of the date. | e.g., Asia/Seoul |

### Example of Date String Parsing

#### Condition

* Source field → `message`
* Format → `["yyyy-MM-dd HH:mm:ssZ", "ISO8601"]`
* Output field → `time`
* Timezone → `Asia/Seoul`

#### Input message

```js
{
    "message": "2017-03-16T17:40:00"
}
```

#### Output message

```js
{
    "message": "2017-03-16T17:40:00",
    "time": 2022-04-04T09:08:01.222Z
}
```

## Filter > UUID

### Node Description

A node that creates UUIDs and stores them in a field.

### Property Description

| Property Name | Default Value | Data Type | Description | Notes |
| --- | --- | --- | --- | --- |
| UUID Output Field | - | string | Enter the field name to store the UUID generation result. |  |
| Overwrite | false | boolean | Select whether to overwrite the value if it already exists in the specified field name. |  |

### Example of Creating UUID

#### Condition

UUID output field → `userId`

#### Input Message

```js
{
    "message": "uuid test message"
}
```

#### Output message

```js
{
    "userId": "70186b1e-bdec-43d6-8086-ed0481b59370",
    "message": "uuid test message"
}
```

## Filter > Convert

### Node Description

A node that converts the data type of a specific field.

### Property Description

| Property Name | Default Value | Data Type | Description | Notes |
|-------|-----|--------|-----------------------------------------------------------------------------|----|
| Target Field | - | string | Enter the target field to convert the data type of. | Whether a dropdown is provided when a schema is defined |
| Conversion Type | - | enum | Select the data type to convert to. <br/> * Supported types: `STRING, INTEGER, FLOAT, DOUBLE, BOOLEAN` | |

### Example of Converting Data

#### Condition

* Target field → `message`
* Conversion type → `INTEGER`

#### Input message

```js
{
   "message": "2025"
}
```

#### Output message

```js
{
    "message": 2025
}
```


## Filter > Coerce

### Node Description

A node that replaces null values ​​with default values.

### Property Description

| Property Name | Default Value | Data Type | Description | Notes |
| --- | --- | --- | --- | --- |
| Target Field | - | string | Enter the field name to assign a default value to. | Whether a dropdown is provided when a schema is defined |
| Default Value | - | string | Enter the default value. |  |

### Default Setting Example

#### Condition
* Target field → `fieldname`
* Default value → `default_value`

#### Input Message

```json
{
    "fieldname": null
}
```

#### Output Message

```json
{
    "fieldname": "default_value"
}
```

## Filter > Copy

### Node Description

A node that copies an existing field to another field.

### Property Description

| Property Name | Default | Data Type | Description | Note |
| --- | --- | --- | --- | --- |
| Target Field | - | string | Enter the name of the source field to copy. | Whether a dropdown is provided when a schema is defined |
| Field to Save | - | string | Enter the name of the field to save the copied result. | |
| Overwrite | false | boolean | If true, the field to save will be overwritten if it already exists. | |

### Example

#### Condition
* Source field → `source_field`
* Field to be saved → `dest_field`

#### Input Message

```json
{
    "source_field": "Hello World!"
}

```

#### Output Message

```json
{
    "source_field": "Hello World!",
    "dest_field": "Hello World!"
}
```

## Filter > Rename

### Node Description

A node that changes the field name.

### Property Description

| Property Name | Default | Data Type | Description | Note |
| --- | --- | --- | --- | --- |
| Source Field | | string | Enter the source field to be renamed. | Whether a dropdown is provided when a schema is defined |
| Target Field | - | string | Enter the field name to be renamed | |
| Overwrite | false | boolean | If true, the target field will be overwritten if it already exists. | |

### Example

#### Condition
* Source field → `fieldname`
* Target field → `changed_fieldname`

#### Input Message

```json
{
    "fieldname": "Hello World!"
}
```

#### Output Message

```json
{
    "changed_fieldname": "Hello World!"
}
```

## Filter > Strip

### Node Description

A node that removes leading and trailing spaces from a string in a field.

### Property Description

| Property Name | Default Value | Data Type | Description | Note |
| --- | --- | --- | --- | --- |
| Target Fields | - | array of strings | Enter the target fields from which to remove blank. | Whether a dropdown is provided when a schema is defined (multiple selection) |

### Example

#### Condition
Target field → `["field1", "field2"]`

#### Input Message

```json
{
    "field1": "Hello World!   ",
    "field2": "   Hello DataFlow!"
}

```

#### Output Message

```json
{
    "field1": "Hello World!",
    "field2": "Hello DataFlow!"
}

```

## Filter > Remove Fields

### Node Description

A node to delete a field.

### Property Description

| Property Name    | Default Value | Data Type        | Description                              | Notes |
|------------------|---------------|------------------|------------------------------------------|-------|
| Fields to Delete | -             | array of strings | Enter the list of field names to delete. | Whether a dropdown is provided when a schema is defined (multiple selection) |

### Configuration Example

#### Condition
Fields to delete → `["field2", "field3"]`

#### Input Message

```json
{
    "field1": "value1",
    "field2": "value2",
    "field3": "value3",
    "field4": "value4"
}
```

#### Output Message

```json
{
    "field1": "value1",
    "field4": "value4"
}
```

## Filter > Tokenizer

### Node Description

A node that tokenizes string fields using regular expressions.

### Property Description

| Property | Default | Data type | Description | Note |
|----------|---------|-----------|-------------|------|
| Source field | - | string | Enter the name of the source field to tokenize. | |
| Target field | - | string | Enter the name of the field to store the tokenization result. | |
| Regular expression | \s+ | string | Enter the regular expression to use for tokenization. | |
| Mode | SEPARATOR | enum | Select the tokenization mode. | SEPARATOR: Uses the regular expression as a delimiter<br>MATCH: Uses the regular expression for token matching |
| Minimum token length | 1 | number | Enter the minimum length of a token. Tokens shorter than the minimum token length are excluded from the result. | |
| Overwrite | false | boolean | If true, overwrites the target field if it already exists. | |

### SEPARATOR Mode Example

#### Conditions
* Source field → `src_field`
* Target field → `target_field`
* Regular expression → `,`
* Mode → `SEPARATOR`

#### Input message

```json
{
    "src_field": "foo,bar,baz"
}
```

#### Output message

```json
{
    "src_field": "foo,bar,baz",
    "target_field": ["foo", "bar", "baz"]
}
```

### MATCH Mode Example

#### Conditions
* Source field → `src_field`
* Target field → `target_field`
* Regular expression → `[^,]+`
* Mode → `MATCH`

#### Input message

```json
{
    "src_field": "foo,bar,baz"
}
```

#### Output message

```json
{
    "src_field": "foo,bar,baz",
    "target_field": ["foo", "bar", "baz"]
}
```

## Filter > Sampling

### Node Description

* A node that selectively forwards messages to the next node at a specified ratio.
* The forwarding decision is made based on probability. Therefore, the smaller the number of messages, the greater the margin of error from the entered ratio.

### Property Description

| Property | Default | Data type | Description | Note |
| --- | --- | --- | --- | --- |
| Ratio | - | number | Enter the ratio at which messages are forwarded to the next node. | |
| Seed | - | number | Enter the seed to use for random number generation. If the seed is the same and the input messages are identical, the result will be the same. | |

## Filter > Stop Words Remover

### Node Description

A node that removes stop words from string array fields.

### Property Description

| Property | Default | Data type | Description | Note |
|---------|---------|-----------|-------------|------|
| Source field | - | string | Enter the name of the source field from which to remove stop words. | |
| Target field | - | string | Enter the name of the field to store the stop word removal result. | |
| Built-in stop word dictionary language | none | enum | Select the language of the built-in stop word dictionary to use for stop word removal. | |
| Stop word dictionary | | string | Enter the list of words to use for stop word removal. Each word is separated by a line break. | |
| Case-sensitive | false | boolean | Select whether to distinguish between uppercase and lowercase letters. | |
| Overwrite | false | boolean | If true, overwrites the target field if it already exists. | |

### Predefined Dictionaries
* The predefined dictionaries by language are as follows:
  * [ko](http://static.toastoven.net/prod_dataflow/ko/node-config-guide/stop_word_remover_dict_ko.txt)
  * [en](http://static.toastoven.net/prod_dataflow/ko/node-config-guide/stop_word_remover_dict_en.txt)

### Configuration Example

#### Conditions
* Source field → `src_field`
* Target field → `target_field`
* Dictionary
```
is
a
```

#### Input message

```json
{
    "src_field": ["hello", "world", "this", "is", "a", "test"]
}
```

#### Output message

```json
{
  "src_field": ["hello", "world", "this", "is", "a", "test"],
  "target_field": ["hello", "world", "this", "test"]
}
```

## Filter > Pattern Extractor (Grok)

### Node Description

* A node that extracts structured information from text data.
* Extracts necessary information from logs or text using pattern matching with regular expressions.
* Supports Grok pattern syntax compatible with Logstash, allowing complex log parsing to be handled with simple patterns.
* You can parse data in various formats by using built-in patterns or creating custom patterns.

### Property Description

| Property | Default | Data type | Description | Note |
|---|---|---|---|---|
| Source field | - | string | Enter the name of the original field from which to extract patterns. | |
| Target field | - | string | Enter the name of the field to store the extraction result. If not entered, the result is added directly to the root. | |
| Custom pattern | - | hash | Define additional patterns to use beyond the built-in patterns. Enter pattern names and regular expressions in key-value format. | If a pattern with the same name exists in the built-in patterns, the custom pattern takes priority and overrides the built-in pattern. |
| Pattern expression | - | string | Enter the fields and patterns to extract from the data as a Grok expression. | |
| Overwrite | false | boolean | Configure whether to overwrite the target field with the extraction result if a value already exists. | |

!!! tip "Built-in patterns"
    Frequently used patterns are predefined and provided.
    Various patterns for different situations are included, such as date/time, IP address, URL, and log level.
    Since built-in patterns have a hierarchical structure that internally references other patterns, additional fields beyond the specified field name may be generated.
    Refer to the [Built-in pattern list](https://static.toastoven.net/prod_dataflow/node-config-guide/predefined_patterns.txt).


### Example

#### Conditions
* Source field → `log_message`
* Target field → `result`
* Custom pattern → `{"CUSTOM_PHONE_NUMBER": "01[016789]-\d{3,4}-\d{4}", "CUSTOM_EMPLOYEE_ID": "EMP-\d{6}", "CUSTOM_ORDER_ID": "ORD-[A-Z]{3}-\d{8}"}`
* Pattern expression → `%{TIMESTAMP_ISO8601:timestamp} %{LOGLEVEL:level} %{CUSTOM_EMPLOYEE_ID:custom_emp_id} %{CUSTOM_PHONE_NUMBER:custom_phone_number} %{CUSTOM_ORDER_ID:custom_order_id} %{GREEDYDATA:message}`

#### Input message

```json
{
  "log_message": "2024-03-15T09:30:00.000Z INFO EMP-123456 010-1234-5678 ORD-ABC-12345678 Order processing started",
  "created_by": "DataFlow"
}
```

#### Output message

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

Type of node that defines an endpoint to load data that has completed filter operation.

## Common Settings on Sink Node

| Property name | Default value | Data type | Description | Others |
| --- | --- | --- | --- | --- |
| ID | - | string | Sets Node ID<br/>Mark node name on the chart board with values defined in this property. |  |

## Sink > (NHN Cloud) Object Storage

### Node Description

* Node for uploading data to Object Storage in NHN Cloud.
* When created using default settings without additional configuration, objects are output according to the following path format.
    * `/{bucket_name}/year={yyyy}/month={MM}/day={dd}/hour={HH}/part-{uuid}-{file_counter}`   
* JSON, LINE, and Parquet codecs are provided.

### Property Description 

| Property Name | Default Value | Data Type | Description | Notes |
|-----------------------|----------------------------------------------------|--------|--------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------|
| Region                    | -                                                  | enum   | Enter a region of the Object Storage product.                                |                                                                                                                            |
| Bucket                    | -                                                  | string | Enter a bucket name.                                                |                                                                                                                            |
| Secret key                  | -                                                  | string | Enter an S3 API credentials secret key.                                    |                                                                                                                            |
| Access key                 | -                                                  | string | Enter an S3 API credentials access key.                                   |                                                                                                                            |
| Prefix | /year=%{+YYYY}/month=%{+MM}/day=%{+dd}/hour=%{+HH} | string | Enter the prefix to prepend to the object name when uploading.<br/>You can enter a field or time format. | [Available time formats](https://joda-time.sourceforge.net/apidocs/org/joda/time/format/DateTimeFormat.html) |
| Prefix Time Field | -                                                  | string | Enter the time field to apply to the Prefix. | |
| Prefix Time Field Type | DATE_FILTER_RESULT | enum | Enter the type of the time field to apply to the Prefix. | Only DATE_FILTER_RESULT type is supported (other types to be supported in the future) |
| Prefix Timezone | UTC | string | Enter the timezone of the time field to apply to the Prefix. | |
| Prefix Time Fallback | _prefix_datetime_parse_failure                                    | string | Enter the fallback Prefix to use if applying the Prefix time fails. | |
| Time Interval | 1 | number | Sets the time interval used as the criteria for splitting objects. | |
| Object Size Threshold | 5242880 | number | Sets the size (unit: bytes) used as the criteria for splitting objects. | |
| Inactivity Interval | 1 | number | Sets the time interval for splitting objects when there is no data ingestion. | If no data is ingested during the configured time, the current object is uploaded and subsequent data is written to a new object. |

### Output Examples by Codec Type

Supported codec:
* [JSON codec](./codec-config-guide.md#json) - JSON data parsing
* [LINE codec](./codec-config-guide.md#line) - Line-by-line message processing
* [Parquet codec](./codec-config-guide.md#parquet) - Compressed into Parquet format

### Prefix Example - Field

#### Condition

* Bucket → `obs-test-container`
* Prefix → `/dataflow/%{deployment}`

#### Input Message
``` json
{
    "deployment": "production",
    "message": "example",
    "logTime": "2022-11-21T07:49:20Z"
}
```

#### Output Path

```
/obs-test-container/dataflow/production/part-378be4d8-2c59-4014-aaeb-a9bc75af2653-0
```

### Prefix Example - Hour

#### Condition

* Bucket → `obs-test-container`
* Prefix → `/dataflow/year=%{+YYYY}/month=%{+MM}/day=%{+dd}/hour=%{+HH}`
* Prefix Time Field → `logTime`
* Prefix Time Field Type → `ISO8601`
* Prefix Time Zone → `Asia/Seoul`

#### Input Message
``` json
{
    "deployment": "production",
    "message": "example",
    "logTime": "2022-11-21T07:49:20Z"
}
```

#### Output Path

```
/obs-test-container/dataflow/year=2022/month=11/day=21/hour=16/part-378be4d8-2c59-4014-aaeb-a9bc75af2653-0
```

### Prefix Example - When failed to apply time

#### Condition

* Bucket → `obs-test-container`
* Prefix → `/dataflow/year=%{+YYYY}/month=%{+MM}/day=%{+dd}/hour=%{+HH}`
* Prefix Time Field → `logTime`
* Prefix Time Field Type → `TIMESTAMP_SEC`
* Prefix Time Zone → `Asia/Seoul`
* Prefix Time Application fallback → `_failure`

#### Input Message
``` json
{
    "deployment": "production",
    "message": "example",
    "logTime": "2022-11-21T07:49:20Z"
}
```

#### Output Path

```
/obs-test-container/_failure/part-378be4d8-2c59-4014-aaeb-a9bc75af2653-0
```
## Sink > (NHN Cloud) Data Lake Storage

### Node Description
* Node that uploads data to Data Lake Storage of NHN Cloud.
* When created using default settings without additional configuration, objects are output according to the following path format.
    * `/{bucket_name}/year={yyyy}/month={MM}/day={dd}/hour={HH}/part-{uuid}-{file_counter}`   
* JSON, LINE, and Parquet codec are provided.

### Property Description
| Property name | Default value | Data type | Description | Others |
|-----------------------|----------------------------------------------------|--------|--------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------|
| Region                    | -                                                  | enum   | Enter a region of the Object Storage product.                                |                                                                                                                            |
| Bucket                    | -                                                  | string | Enter a bucket name.                                                |                                                                                                                            |
| Secret key                  | -                                                  | string | Enter an S3 API credentials secret key.                                    |                                                                                                                            |
| Access key                 | -                                                  | string | Enter an S3 API credentials access key.                                   |                                                                                                                            |
| Prefix                | /year=%{+YYYY}/month=%{+MM}/day=%{+dd}/hour=%{+HH} | string | Enter the prefix to prepend to the object name when uploading.<br/>A field or time format can be entered. | [Available time formats](https://joda-time.sourceforge.net/apidocs/org/joda/time/format/DateTimeFormat.html)                         |
| Prefix time field          | -                                                  | string | Enter the time field to apply to the Prefix.                                    |                                                                                                                            |
| Prefix time field type       | DATE_FILTER_RESULT                                 | enum   | Enter the type of the time field to apply to the Prefix.                                | Only DATE_FILTER_RESULT type is supported (other types will be supported in the future).                                                                        |
| Prefix time zone            | UTC                                                | string | Enter the time zone of the time field to apply to the Prefix.                              |                                                                                                                            |
| Prefix time application fallback | _prefix_datetime_parse_failure                                   | string | Enter the fallback Prefix to use when Prefix time application fails.                      |                                                                                                                            |
| Reference time                 | 1                                                  | number | Set the time interval used as the criterion for splitting objects.                                   |                                                                                                                            |
| Reference object size            | 5242880                                            | number | Set the size (in bytes) used as the criterion for splitting objects.                         |                                                                                                                            |
| Inactivity interval                | 1                                                  | number | Set the time interval for splitting objects when no data ingestion occurs. | If no data is ingested during the set time, the current object is uploaded, and subsequently ingested data is written to a new object.                                                     |

### Output Examples by Codec Type

Supported codec:
* [JSON codec](./codec-config-guide.md#json) - JSON data parsing
* [LINE codec](./codec-config-guide.md#line) - Line-by-line message processing
* [Parquet codec](./codec-config-guide.md#parquet) - Compressed in Parquet format

### Prefix Example - Field
#### Condition
* Bucket → `dls-test-container`
* Prefix → `/dataflow/%{deployment}`

#### Input Message
``` json
{
    "deployment": "production",
    "message": "example",
    "logTime": "2022-11-21T07:49:20Z"
}
```

#### Output Path
```
/dls-test-container/dataflow/production/part-378be4d8-2c59-4014-aaeb-a9bc75af2653-0
```

### Prefix Example - Time
#### Condition
* Bucket → `dls-test-container`
* Prefix → `/dataflow/year=%{+YYYY}/month=%{+MM}/day=%{+dd}/hour=%{+HH}`
* Prefix time field → `logTime`
* Prefix time field type → `ISO8601`
* Prefix timezone → `Asia/Seoul`

#### Input Message
``` json
{
    "deployment": "production",
    "message": "example",
    "logTime": "2022-11-21T07:49:20Z"
}
```

#### Output Path
```
/dls-test-container/dataflow/year=2022/month=11/day=21/hour=16/part-378be4d8-2c59-4014-aaeb-a9bc75af2653-0
```

### Prefix Example - When time application fails
#### Condition
* Bucket → `dls-test-container`
* Prefix → `/dataflow/year=%{+YYYY}/month=%{+MM}/day=%{+dd}/hour=%{+HH}`
* Prefix time field → `logTime`
* Prefix time field type → `ISO8601`
* Prefix timezone → `Asia/Seoul`
* Prefix fallback for time application failure → `_failure`

#### Input Message
``` json
{
    "deployment": "production",
    "message": "example",
    "logTime": "2022-11-21T07:49:20Z"
}
```

#### Output Path
```
/dls-test-container/_failure/part-378be4d8-2c59-4014-aaeb-a9bc75af2653-0
```

## Sink > (Amazon) S3

### Node Description

* Node for uploading data to Amazon S3.
* JSON, LINE, and Parquet codecs are provided.

### Property Description 
| Property Name | Default Value | Data Type | Description | Notes |
| --- | --- | --- | --- | --- |
| Region | - | enum | Enter the region of the S3 service. | [S3 region](https://docs.aws.amazon.com/general/latest/gr/s3.html) |
| Bucket | - | string | Enter the bucket name. | |
| Access Key | - | string | Enter the S3 API credentials access key. | |
| Secret Key | - | string | Enter the S3 API credentials secret key. | |
| Prefix | - | string | Enter the prefix to prepend to the object name when uploading.<br/>You can enter a field or time format. | [Available time formats](https://joda-time.sourceforge.net/apidocs/org/joda/time/format/DateTimeFormat.html) |
| Prefix Time Field | - | string | Enter the time field to apply to the Prefix. | |
| Prefix Time Field Type | DATE_FILTER_RESULT | enum | Enter the type of the time field to apply to the Prefix. | Only DATE_FILTER_RESULT type is supported (other types to be supported in the future) |
| Prefix Timezone | UTC | string | Enter the timezone of the time field to apply to the Prefix. | |
| Prefix Time Fallback | _prefix_datetime_parse_failure | string | Enter the fallback Prefix to use if applying the Prefix time fails. | |
| Time Interval | 1 | number | Sets the time interval used as the criteria for splitting objects. | |
| Object Size Threshold | 5242880 | number | Sets the size used as the criteria for splitting objects. | |
| Path-style Request | false | boolean | Determines whether to use path-style requests. | |
| Inactivity Interval | 1 | number | Sets the time interval for splitting objects when there is no data ingestion. | If no data is ingested during the configured time, the current object is uploaded and subsequent data is written to a new object. |

!!! danger "Caution"
    * If you connect to NHN Cloud Object Storage using the (Amazon) S3 node, **Path-style Request** must be set to `true`.


### Output Examples by Codec Type

Supported codec:
* [JSON codec](./codec-config-guide.md#json) - JSON data parsing
* [LINE codec](./codec-config-guide.md#line) - Line-by-line message processing
* [Parquet codec](./codec-config-guide.md#parquet) - Compressed in Parquet format

## Sink > (NHN Cloud) EasyQueue

### Node Description
Node that transfers data of EasyQueue in NHN Cloud.

### Property Description
| Property name | Default value | Data type | Description | Others |
| --- | --- | --- | --- | --- |
| Appkey | - | string | Enter an appkey of EasyQueue. |  |
| User Access Key ID | - | string | Enter a User Access Key ID of user account. |  |
| Secret Access Key | - | string | Enter a User Secret Key of user account. |  |
| Topic | - | string | Enter the name of the Kafka topic to send messages to. |  |
| Broker server list | - | string | Enter the Kafka broker servers. If there are multiple servers, separate them with a comma (`,`). | See `bootstrap.servers` in the [Kafka official documentation](https://kafka.apache.org/39/configuration/producer-configs/)<br/>Example: 10.100.1.1:9092,10.100.1.2:9092 |
| Client ID | dataflow | string | Enter the ID that identifies the Kafka Producer. | See `client.id` in the [Kafka official documentation](https://kafka.apache.org/39/configuration/producer-configs/) |
| Compression type | none | enum | Enter the method for compressing the data to be sent. | See `compression.type` in the [Kafka official documentation](https://kafka.apache.org/39/configuration/topic-level-configs/)<br/>Select one of: none, gzip, snappy, lz4, zstd |
| Message key | - | string | Enter the field to use as the message key. |  |
| Metadata refresh interval | 300000 | number | Enter the interval (in ms) for refreshing partition and broker server status. | See `metadata.max.age.ms` in the [Kafka official documentation](https://kafka.apache.org/39/configuration/producer-configs/) |
| Max request size | 1048576 | number | Enter the maximum size (in bytes) per transmission request. | See `max.request.size` in the [Kafka official documentation](https://kafka.apache.org/39/configuration/producer-configs/) |
| Server reconnect interval | 50 | number | Enter the interval (in ms) for retrying when a connection to the broker server fails. | See `reconnect.backoff.ms` in the [Kafka official documentation](https://kafka.apache.org/39/configuration/producer-configs/) |
| Batch size | 16384 | number | Enter the size (in bytes) to send per batch request. | See `batch.size` in the [Kafka official documentation](https://kafka.apache.org/39/configuration/producer-configs/) |
| Buffer memory | 33554432 | number | Enter the size (in bytes) of the buffer used for Kafka transmission. | See `buffer.memory` in the [Kafka official documentation](https://kafka.apache.org/39/configuration/producer-configs/) |
| Receive buffer size | 32768 | number | Enter the size (in bytes) of the TCP receive buffer used to read data. | See `receive.buffer.bytes` in the [Kafka official documentation](https://kafka.apache.org/39/configuration/producer-configs/) |
| Transmission delay | 0 | number | Enter the time to delay message transmission. Delayed messages are sent at once as a batch request. | See `linger.ms` in the [Kafka official documentation](https://kafka.apache.org/39/configuration/producer-configs/) |
| Server request timeout | 30000 | number | Enter the timeout (in ms) for transmission requests. | See `request.timeout.ms` in the [Kafka official documentation](https://kafka.apache.org/39/configuration/producer-configs/) |
| Send buffer size | 131072 | number | Enter the size (in bytes) of the TCP send buffer used to transmit data. | See `send.buffer.bytes` in the [Kafka official documentation](https://kafka.apache.org/39/configuration/producer-configs/) |
| ack property | all | enum | Enter the setting for confirming whether the broker server has received the message. | See `acks` in the [Kafka official documentation](https://kafka.apache.org/39/configuration/producer-configs/)<br/>0 - Does not confirm whether the message was received.<br/>1 - The topic leader responds that the message was received without waiting for the follower to replicate the data.<br/>all - The topic leader waits for the follower to replicate the data before responding that the message was received. |
| Retry request interval | 100 | number | Enter the interval (in ms) for retrying when a transmission request fails. | See `retry.backoff.ms` in the [Kafka official documentation](https://kafka.apache.org/39/configuration/producer-configs/) |
| Retry count | 2147483647 | number | Enter the maximum number of retries when a transmission request fails. | See `retries` in the [Kafka official documentation](https://kafka.apache.org/39/configuration/producer-configs/)<br/>Data loss may occur if retries exceed the configured value. |
| Delivery guarantee | EXACTLY_ONCE | enum | Select the message delivery guarantee method. | AT_LEAST_ONCE: Messages are delivered at least once, but duplicates may occur in the event of a failure. Suitable when duplicate processing can be managed by the application directly, or when duplicates are acceptable.<br/><br/>EXACTLY_ONCE: Messages are processed exactly once. Suitable for critical transactions such as payments and settlements where duplicates are not acceptable, but throughput may be slightly reduced as transactions are used internally. |
| Additional settings | - | hash | Enter additional Producer settings to use for the Kafka connection. | See the [Kafka official documentation](https://kafka.apache.org/39/configuration/producer-configs/) |

### Output Examples by Codec Type
Supported codec:
* [JSON codec](./codec-config-guide.md#json) - JSON data parsing
* [LINE codec](./codec-config-guide.md#line) - Line-by-line message processing  

## Sink > (Apache) Kafka

### Node Description

Node for sending data to Kafka.

### Property Description 

| Property Name | Default Value | Data Type | Description | Notes |
|-------------|--------------|--------|----------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Topic | - | string | Enter the name of the Kafka topic to send messages to. | |
| Broker Server List | | string | Enter the Kafka broker servers. Separate multiple servers with a comma (`,`). | Refer to the `bootstrap.servers` property in the [Kafka documentation](https://kafka.apache.org/39/configuration/producer-configs/).<br/>e.g., 10.100.1.1:9092,10.100.1.2:9092 |
| Client ID | dataflow | string | Enter the ID to identify the Kafka Producer. | Refer to the `client.id` property in the [Kafka documentation](https://kafka.apache.org/39/configuration/producer-configs/). |
| Compression Type | none | enum | Enter the compression method for the data to send. | Refer to the `compression.type` property in the [Kafka documentation](https://kafka.apache.org/39/configuration/topic-level-configs/).<br/>Select one of: none, gzip, snappy, lz4, zstd |
| Message Key | - | string | Enter the field to use as the message key. | |
| Metadata Refresh Interval | 300000 | number | Enter the interval (ms) for refreshing partition and broker server status. | Refer to the `metadata.max.age.ms` property in the [Kafka documentation](https://kafka.apache.org/39/configuration/producer-configs/). |
| Maximum Request Size | 1048576 | number | Enter the maximum size (bytes) per transmission request. | Refer to the `max.request.size` property in the [Kafka documentation](https://kafka.apache.org/39/configuration/producer-configs/). |
| Server Reconnect Interval | 50 | number | Enter the interval (ms) for retrying a failed connection to the broker server. | Refer to the `reconnect.backoff.ms` property in the [Kafka documentation](https://kafka.apache.org/39/configuration/producer-configs/). |
| Batch Size | 16384 | number | Enter the size (bytes) to send in a batch request. | Refer to the `batch.size` property in the [Kafka documentation](https://kafka.apache.org/39/configuration/producer-configs/). |
| Buffer Memory | 33554432 | number | Enter the size (bytes) of the buffer used for Kafka transmission. | Refer to the `buffer.memory` property in the [Kafka documentation](https://kafka.apache.org/39/configuration/producer-configs/). |
| Receive Buffer Size | 32768 | number | Enter the size (bytes) of the TCP receive buffer used for reading data. | Refer to the `receive.buffer.bytes` property in the [Kafka documentation](https://kafka.apache.org/39/configuration/producer-configs/). |
| Transmission Delay | 0 | number | Enter the delay time for sending messages. Delayed messages are sent together in a batch request. | Refer to the `linger.ms` property in the [Kafka documentation](https://kafka.apache.org/39/configuration/producer-configs/). |
| Server Request Timeout | 30000 | number | Enter the timeout (ms) for a transmission request. | Refer to the `request.timeout.ms` property in the [Kafka documentation](https://kafka.apache.org/39/configuration/producer-configs/). |
| Send Buffer Size | 131072 | number | Enter the size (bytes) of the TCP send buffer used for data transmission. | Refer to the `send.buffer.bytes` property in the [Kafka documentation](https://kafka.apache.org/39/configuration/producer-configs/). |
| ack Property | all | enum | Enter the configuration for confirming whether the broker server has received the message. | Refer to the `acks` property in the [Kafka documentation](https://kafka.apache.org/39/configuration/producer-configs/).<br/>0 - Does not confirm whether the message was received.<br/>1 - The topic leader responds that the message was received without waiting for followers to replicate the data.<br/>all - The topic leader responds that the message was received after waiting for followers to replicate the data. |
| Retry Request Interval | 100 | number | Enter the interval (ms) for retrying a failed transmission request. | Refer to the `retry.backoff.ms` property in the [Kafka documentation](https://kafka.apache.org/39/configuration/producer-configs/). |
| Retry Count | 2147483647 | number | Enter the maximum number of retries for a failed transmission request. | Refer to the `retries` property in the [Kafka documentation](https://kafka.apache.org/39/configuration/producer-configs/).<br/>Data loss may occur if retries exceed the configured value. |
| Delivery guarantee | EXACTLY_ONCE | enum | Select the message delivery guarantee method. | AT_LEAST_ONCE: Messages are delivered at least once, but duplicates may occur in the event of a failure. Suitable when duplicate processing can be managed by the application directly, or when duplicates are acceptable.<br/>EXACTLY_ONCE: Messages are processed exactly once. Suitable for critical transactions such as payments and settlements where duplicates are not acceptable, but throughput may be slightly reduced as transactions are used internally. |
| Additional Configuration | - | hash | Enter additional Producer configuration to use for the Kafka connection. | Refer to the [Kafka documentation](https://kafka.apache.org/39/configuration/producer-configs/). |

#### Output Examples by Codec Type

Supported codec:
* [JSON codec](./codec-config-guide.md#json) - JSON data parsing
* [LINE codec](./codec-config-guide.md#line) - Line-by-line message processing  

## Sink > Stdout

### Node Description

* Node that outputs messages to standard output.
* This is useful for checking the data processed by the Source, Filter nodes.

### Example output by codec

Supported codec:
* [JSON codec](./codec-config-guide.md#json) - JSON data parsing
* [LINE codec](./codec-config-guide.md#line) - Line-by-line message processing  

## Branch

Node type that defines flow Quarter in accordance with imported data value.

## Branch > IF

### Node Description

Node for filtering messages with conditional sentence.

### Property Description 

| Property name | Default value | Data type | Description | Others |
| --- | --- | --- | --- | --- |
| conditional sentence. | - | string | Enter the conditions for message filtering. | See the examples below.|

#### Available operators
* Comparison: ==, !=, <, >, <=, >=
* Regular expression: =~ (checks the left-hand string against the pattern given on the right-hand side)
* Inclusion: =~, !~, .contains()
* Logical operators: &&, ||, not
* Negation operators: !, not

### Filtering example exercise - first depth field reference

#### condition
Conditional → `logLevel == "ERROR"`

#### Pass message

``` json
{ 
    "logLevel": "ERROR"
}
```

#### Missed message

``` json
{ 
    "logLevel": "INFO"
}
```

### Filtering example exercise - second depth field reference

#### condition

Conditional → `response.status == 200` or `response["status"] == 200`

#### Passed message

``` json
{ 
    "response": { 
        "status": 200 
    } 
}
```

#### Missed message

``` json
{ 
    "response": { 
        "status": 404 
    } 
}
```

## Branch > Dataset Split

### Node Description

* A node that splits events into multiple branches according to configured ratios.
* Can be used for purposes such as machine learning dataset splitting (e.g., training/test/validation).
* Each branch can be connected to one downstream node.

### Property Description

| Property | Default | Data type | Description | Note |
| --- | --- | --- | --- | --- |
| Seed | - | number | Enter the seed to use for random number generation. If the seed is the same and the input messages are identical, the result will be the same. | |
| Split settings | - | hash | Enter branch names and ratios in JSON format. The sum of all ratios must be `1.0`. | e.g., `{"train": 0.6, "test": 0.3, "sampling": 0.1}` |

### Event Split Example

#### Conditions

* Seed → `42`
* Split settings → `{"train": 0.6, "test": 0.3, "sampling": 0.1}`

#### Behavior

Input events are forwarded to each branch according to the configured ratios.

* Downstream node connected to the `train` branch: Approximately 60% of all events are forwarded.
* Downstream node connected to the `test` branch: Approximately 30% of all events are forwarded.
* Downstream node connected to the `sampling` branch: Approximately 10% of all events are forwarded.