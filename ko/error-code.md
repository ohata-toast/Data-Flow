## Data & Analytics > DataFlow > 오류 코드 가이드

| 오류 코드                       | 설명                                                                                 |
|-----------------------------|------------------------------------------------------------------------------------|
| SERVICE_INITIALIZING        | 사용자 환경을 초기화하고 있습니다. 3분 후 다시 시도하세요.                                                 |  
| FLOW_GRAPH_DISCONNECTED     | 플로우의 그래프가 끊어져 있습니다.                                                                |  
| FLOW_GRAPH_CYCLE            | 플로우에 정의된 노드의 연결선(흐름)은 이전 노드로 되돌아갈 수 없습니다.                                          |
| FLOW_NODE_DUPLICATED        | 플로우에 중복된 노드 ID가 있습니다.                                                              |
| FLOW_INCOMPLETE             | 노드가 없거나 노드를 연결하는 링크가 없습니다. 각 노드 생성 후 노드를 연결하세요.                                    |
| FLOW_INVALID_PROPERTY       | 노드의 속성이 올바르지 않습니다.                                                                 | 
| FLOW_INVALID_DSL            | 노드의 DSL이 올바르지 않습니다.                                                                | 
| FLOW_LOG_RETRIEVAL_ERROR    | 플로우 로그를 조회할 수 없습니다.                                                                |
| FLOW_INVALID_EXECUTION_MODE | Source 노드의 실행 모드가 설정된 실행 모드와 일치하지 않습니다.                                            |
| SKM_INVALID_APPKEY          | Cipher 노드의 앱키 정보가 유효하지 않습니다.                                                       |
| SKM_INVALID_KEY_ID          | Cipher 노드의 키 ID 정보가 유효하지 않습니다.                                                     |
| SKM_INVALID_KEY_VERSION     | Cipher 노드의 키 버전 정보가 유효하지 않습니다.                                                     |
| SKM_ERROR                   | SKM 연결이 실패했습니다.                                                                    |
| LNCS_UNAUTHENTICATED        | Log & Crash Search 노드의 앱키, 비밀 키가 유효하지 않습니다.                                        |
| LNCS_SEARCH_LIMIT           | Log & Crash Search 노드가 검색 제한에 도달했습니다.    고객문의로 문의하세요.                             |
| LNCS_SERVICE_UNSTABLE       | Log & Crash Search 서비스가 불안정한 상태입니다.   고객문의로 문의하세요.                                |
| LNCS_INVALID_PROPERTY       | Log & Crash Search 노드의 속성이 올바르지 않습니다.                                              |
| KAFKA_ENDPOINT_INVALID      | Kafka 엔드포인트에 접근할 수 없습니다.                                                           |
| S3_UNAUTHENTICATED          | S3, Object Storage 노드의 액세스 키 또는 비밀 키가 유효하지 않습니다.                                   |
| S3_ACCESS_DENIED            | S3, Object Storage 저장소에 접근이 거부되었습니다. S3, Object Storage 노드의 액세스 키에 부여된 ACL을 확인하세요. |
| S3_NO_SUCH_BUCKET           | S3, Object Storage 저장소에 버킷이 존재하지 않습니다.                                             |
| S3_SERVICE_ERROR            | S3, Object Storage 저장소가 불안정한 상태입니다. 해당 저장소로 문의하세요.                                 |
| S3_INVALID_ENDPOINT         | S3, Object Storage 노드의 엔드포인트 또는 리전이 입력되지 않았습니다.                                    |
| S3_INVALID_CREDENTIAL       | S3, Object Storage 노드의 액세스 키 또는 비밀 키가 입력되지 않았습니다.                                  |
| CLOUDTRAIL_UNAUTHENTICATED  | CloudTrail 노드의 앱키가 유효하지 않습니다.                                                      |
| CLOUDTRAIL_INVALID_PROPERTY | CloudTrail 노드의 속성이 올바르지 않습니다.                                                      |
| JDBC_CONNECT_FAILED         | JDBC 연결이 실패했습니다.                                                                   |
| JDBC_UNSUPPORTED_DRIVER     | 지원하지 않는 JDBC 드라이버입니다.                                                              |
| FLOW_ALREADY_STARTED        | 플로우가 이미 시작되었습니다.                                                                   |
| FLOW_ALREADY_STOPPED        | 플로우가 이미 종료되었습니다.                                                                   |
| ERROR                       | 서비스 내부 오류 또는 정의되지 않은 오류입니다. 고객문의로 문의하세요.                                          |
