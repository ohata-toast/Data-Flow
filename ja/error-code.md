## Data & Analytics > DataFlow > エラーコードガイド

| エラーコード | 説明 |
|-----------------------------|-------------------------------------------------------------------------------------|
| SERVICE_INITIALIZING | ユーザー環境を初期化しています。3分後にもう一度お試しください。 |
| FLOW_GRAPH_DISCONNECTED | フローのグラフが切断されています。 |
| FLOW_GRAPH_CYCLE | フローに定義されたノードの接続線(フロー)は、前のノードに戻ることはできません。 |
| FLOW_NODE_DUPLICATED | フローに重複したノードIDがあります。 |
| FLOW_INCOMPLETE | ノードがないか、ノードを接続するリンクがありません。各ノードの作成後にノードを接続してください。 |
| FLOW_INVALID_PROPERTY | ノードのプロパティが正しくありません。 |
| FLOW_INVALID_DSL | ノードのDSLが正しくありません。 |
| FLOW_LOG_RETRIEVAL_ERROR | フローログを照会できません。 |
| FLOW_INVALID_EXECUTION_MODE | Sourceノードの実行モードが設定された実行モードと一致しません。 |
| SKM_INVALID_APPKEY | Cipherノードのアプリキー情報が有効ではありません。 |
| SKM_INVALID_KEY_ID | CipherノードのキーID情報が有効ではありません。 |
| SKM_INVALID_KEY_VERSION | Cipherノードのキーバージョン情報が有効ではありません。 |
| SKM_ERROR | SKMの接続に失敗しました。 |
| LNCS_UNAUTHENTICATED | Log & Crash Searchノードのアプリキー、シークレットキーが有効ではありません。 |
| LNCS_SEARCH_LIMIT | Log & Crash Searchノードが検索制限に達しました。カスタマーサポートにお問い合わせください。 |
| LNCS_SERVICE_UNSTABLE | Log & Crash Searchサービスが不安定な状態です。カスタマーサポートにお問い合わせください。 |
| LNCS_INVALID_PROPERTY | Log & Crash Searchノードのプロパティが正しくありません。 |
| KAFKA_ENDPOINT_INVALID | Kafkaエンドポイントにアクセスできません。 |
| S3_UNAUTHENTICATED | S3、Object Storageノードのアクセスキーまたはシークレットキーが有効ではありません。 |
| S3_ACCESS_DENIED | S3、Object Storageリポジトリへのアクセスが拒否されました。S3、Object Storageノードのアクセスキーに付与されたACLを確認してください。 |
| S3_NO_SUCH_BUCKET | S3、Object Storageリポジトリにバケットが存在しません。 |
| S3_SERVICE_ERROR | S3、Object Storageリポジトリが不安定な状態です。該当リポジトリにお問い合わせください。 |
| S3_INVALID_ENDPOINT | S3、Object Storageノードのエンドポイントまたはリージョンが入力されていません。 |
| S3_INVALID_CREDENTIAL | S3、Object Storageノードのアクセスキーまたはシークレットキーが入力されていません。 |
| CLOUDTRAIL_UNAUTHENTICATED | CloudTrailノードのアプリキーが有効ではありません。 |
| CLOUDTRAIL_INVALID_PROPERTY | CloudTrailノードのプロパティが正しくありません。 |
| JDBC_CONNECT_FAILED | JDBCの接続に失敗しました。 |
| JDBC_UNSUPPORTED_DRIVER | サポートされていないJDBCドライバーです。 |
| FLOW_ALREADY_STARTED | フローはすでに開始されています。 |
| EASY_QUEUE_ENDPOINT_INVALID | EasyQueueエンドポイントにアクセスできません。 |
| EASY_QUEUE_OAUTH_FAILED | EasyQueue OAuth2認証に失敗しました。 |
| EASY_QUEUE_TOPIC_NOT_FOUND | EasyQueueトピックが見つかりません。 |
| EASY_QUEUE_INVALID_PROPERTY | EasyQueueノードのプロパティが正しくありません。 |
| FLOW_ALREADY_STOPPED | フローはすでに終了しています。 |
| ERROR | サービス内部エラーまたは定義されていないエラーです。カスタマーサポートにお問い合わせください。 |
