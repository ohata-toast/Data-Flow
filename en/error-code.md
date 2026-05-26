## Data & Analytics > DataFlow > Error Code Guide

| Error Code              | Description                                                                                                               |
|-------------------------|---------------------------------------------------------------------------------------------------------------------------|
| SERVICE_INITIALIZING        | The user environment is being initialized. Try again in 3 minutes.                                                  |
| FLOW_GRAPH_DISCONNECTED     | The flow graph is disconnected.                                                                 |
| FLOW_GRAPH_CYCLE            | The connection lines (flow) between nodes defined in the flow cannot loop back to a previous node.                                           |
| FLOW_NODE_DUPLICATED        | The flow contains duplicate node IDs.                                                               |
| FLOW_INCOMPLETE             | There are no nodes or no links connecting the nodes. Create each node and then connect them.                                     |
| FLOW_INVALID_PROPERTY       | The node property is invalid.                                                                  |
| FLOW_INVALID_DSL            | The node DSL is invalid.                                                                 |
| FLOW_LOG_RETRIEVAL_ERROR    | Failed to retrieve the flow log.                                                                 |
| FLOW_INVALID_EXECUTION_MODE | The execution mode of the Source node does not match the configured execution mode.                                             |
| SKM_INVALID_APPKEY          | The appkey information for the Cipher node is invalid.                                                        |
| SKM_INVALID_KEY_ID          | The key ID information for the Cipher node is invalid.                                                      |
| SKM_INVALID_KEY_VERSION     | The key version information for the Cipher node is invalid.                                                      |
| SKM_ERROR                   | Failed to connect to SKM.                                                                     |
| LNCS_UNAUTHENTICATED        | The appkey or secret key for the Log & Crash Search node is invalid.                                         |
| LNCS_SEARCH_LIMIT           | The Log & Crash Search node has reached the search limit. Contact customer support.                               |
| LNCS_SERVICE_UNSTABLE       | The Log & Crash Search service is unstable. Contact customer support.                                  |
| LNCS_INVALID_PROPERTY       | The property of the Log & Crash Search node is invalid.                                               |
| KAFKA_ENDPOINT_INVALID      | The Kafka endpoint is not accessible.                                                            |
| S3_UNAUTHENTICATED          | The access key or secret key for the S3 or Object Storage node is invalid.                                    |
| S3_ACCESS_DENIED            | Access to the S3 or Object Storage repository has been denied. Check the ACL granted to the access key of the S3 or Object Storage node.  |
| S3_NO_SUCH_BUCKET           | The bucket does not exist in the S3 or Object Storage repository.                                              |
| S3_SERVICE_ERROR            | The S3 or Object Storage repository is unstable. Contact the relevant storage provider.                                  |
| S3_INVALID_ENDPOINT         | The endpoint or region for the S3 or Object Storage node has not been entered.                                     |
| S3_INVALID_CREDENTIAL       | The access key or secret key for the S3 or Object Storage node has not been entered.                                   |
| CLOUDTRAIL_UNAUTHENTICATED  | The appkey for the CloudTrail node is invalid.                                                       |
| CLOUDTRAIL_INVALID_PROPERTY | The property of the CloudTrail node is invalid.                                                       |
| JDBC_CONNECT_FAILED         | Failed to connect to JDBC.                                                                    |
| JDBC_UNSUPPORTED_DRIVER     | The JDBC driver is not supported.                                                               |
| FLOW_ALREADY_STARTED        | The flow has already started.                                                                    |
| EASY_QUEUE_ENDPOINT_INVALID | The EasyQueue endpoint is not accessible.                                                        |
| EASY_QUEUE_OAUTH_FAILED     | Failed to authenticate with EasyQueue OAuth2.                                                        |
| EASY_QUEUE_TOPIC_NOT_FOUND  | The EasyQueue topic could not be found.                                                            |
| EASY_QUEUE_INVALID_PROPERTY | The property of the EasyQueue node is invalid.                                                        |
| FLOW_ALREADY_STOPPED        | The flow has already stopped.                                                                    |
| ERROR                       | An internal service error or undefined error has occurred. Contact customer support.                                            |