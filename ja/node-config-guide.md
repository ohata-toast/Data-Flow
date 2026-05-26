## Data & Analytics > DataFlow > ノード設定ガイド

* ノードタイプは、手軽にフローを作成できるように事前定義されたテンプレートです。
* ノードタイプの種類には、Source、Filter、Branch、Sinkがあります。
* Source、Sinkノードタイプは、テストを実行してエンドポイント情報が有効であるか確認することをおすすめします。
* アクセス制御が設定されたデータソースへの接続時には、DataFlow IP固定機能を使用する必要があります。
    * DataFlow IP固定機能を使用するには、**カスタマーサポート > お問い合わせ**からお問い合わせください。

### Object Storage接続時の注意事項
リージョンまたはプロジェクトが異なるObject Storageでありながら、バケット名が同じ場合、1つのフローで一緒に使用することはできません。

!!! tip "不可能な接続設定の例"
    * 例1
        * 1つ目の接続対象Object Storage情報
            * リージョン: KR1
            * バケット名: Data
            * プロジェクト: TEST
        * 2つ目の接続対象Object Storage情報
            * リージョン: JP1
            * バケット名: Data
            * プロジェクト: TEST
        * リージョンが異なるため2つのバケットは別々のバケットですが、DataFlowのフローでは一緒に使用不可
    * 例2
        * 1つ目の接続対象Object Storage情報
            * リージョン: KR1
            * バケット名: Data
            * プロジェクト: TEST_1
        * 2つ目の接続対象Object Storage情報
            * リージョン: KR1
            * バケット名: Data
            * プロジェクト: TEST_2
        * プロジェクトが異なるため2つのバケットは別々のバケットですが、DataFlowのフローでは一緒に使用不可


## Domain Specific Language(DSL)の定義

フロー実行に必要なDSL定義です。

### Variable

* `{{ executionTime }}`
    * フロー実行時間
* 時間の単位 ( unit )
    * 分 - `{{ MINUTE }}`
    * 時 - `{{ HOUR }}`
    * 日 - `{{ DAY }}`
    * 月 - `{{ MONTH }}`
    * 年 - `{{ YEAR }}`

### Filter

* `{{ time | startOf: unit }}`
    * 与えられた時間から`unit`で定義された時間帯の開始時間を返します。
    * [注意] 韓国時間を基準として計算します。
    * 例：\{\{ executionTime \| startOf: MINUTE \}\}
    * 例：\{\{ "2022\-11\-04T13:31:28Z" \| startOf: MINUTE \}\}
        * → 2022-11-04T13:31:00Z
    * 例：\{\{ "2022\-11\-04T13:31:28Z" \| startOf: HOUR \}\}
        * → 2022-11-04T13:00:00Z
    * 例：\{\{ "2022\-11\-04T13:31:28Z" \| startOf: DAY \}\}
        * → 2022-11-04T00:00:00Z
    * 例：\{\{ "2022\-11\-04T13:31:28Z" \| startOf: MONTH \}\}
        * → 2022-11-01T00:00:00Z
    * 例：\{\{ "2022\-11\-04T13:31:28Z" \| startOf: YEAR \}\}
        * → 2022-01-01T00:00:00Z
* `{{ time | endOf: unit }}`
    * 与えられた時間から`unit`で定義された時間帯の終了時間を返します。
    * [注意] 韓国時間を基準として計算します。
    * 例：\{\{ executionTime \| endOf: MINUTE \}\}
    * 例：\{\{ "2022\-11\-04T13:31:28Z" \| endOf: MINUTE \}\}
        * → 2022-11-04T13:31:59.999999999Z
    * 例：\{\{ "2022\-11\-04T13:31:28Z" \| endOf: HOUR \}\}
        * → 2022-11-04T13:59:59.999999999Z
    * 例：\{\{ "2022\-11\-04T13:31:28Z" \| endOf: DAY \}\}
        * → 2022-11-04T23:59:59.999999999Z
    * 例：\{\{ "2022\-11\-04T13:31:28Z" \| endOf: MONTH \}\}
        * → 2022-11-30T23:59:59.999999999Z
    * 例：\{\{ "2022\-11\-04T13:31:28Z" \| endOf: YEAR \}\}
        * → 2022-12-31T23:59:59.999999999Z
* `{{ time | subTime: delta, unit }}`
    * 与えられた時間から`unit`で定義された時間帯の`delta`分だけ引いた時間を返します。
    * 例：\{\{ executionTime \| subTime: 10, MINUTE \}\}
    * 例：\{\{ "2022\-11\-04T13:31:28Z" \| subTime: 10, MINUTE \}\}
        * → 2022-11-04T13:21:28Z
* `{{ time | addTime: delta, unit }}`
    * 与えられた時間から`unit`で定義された時間帯の`delta`分だけ足した時間を返します。
    * 例：\{\{ executionTime \| addTime: 10, MINUTE \}\}
    * 例：\{\{ "2022\-11\-04T13:31:28Z" \| addTime: 10, MINUTE \}\}
        * → 2022-11-04T13:41:28Z
* `{{ time | format: formatStr }}`
    * 与えられた時間を`formatStr`の形式で返します。
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
    * 例：\{\{ executionTime \| format: 'yyyy' \}\}
    * 例：\{\{ "2022\-11\-04T13:31:28Z" \| format: 'yyyy' \}\}
        * → 2022
* nested filterの例
    * フローの実行が開始された日の03時のDSL表現
        * → \{\{ executionTime \| startOf: DAY \| addTime: 3\, HOUR \}\}

## データタイプ別の入力方法
### string
文字列を入力します。

### number
* 0以上の数値を入力します。
* 入力欄の右側にある矢印を使用して、値を1ずつ調整できます。

### boolean
プルダウンメニューから`TRUE`または`FALSE`を選択します。

### enum
プルダウンメニューから項目を選択します。

### array of strings
* 配列に入れる文字列を1つずつ入力します。
* 文字列を入力した後、`+`ボタンをクリックすると配列に文字列が挿入されます。
* 例: `["message" , "yyyy-MM-dd HH:mm:ssZ", "ISO8601"]`を入力するには、`message`、`yyyy-MM-dd HH:mm:ssZ`、`ISO8601`の順で配列に文字列を挿入します。

### hash
JSON形式の文字列を入力します。

## スキーマ

### 概要

* Sourceノードで出力スキーマ(フィールド名とデータタイプ)を定義すると、定義されたフィールドのみを選択的に読み込みます。
* 定義されたスキーマは、DAGグラフに沿って下位ノードに自動的に伝播されます。
* Filterノードでフィールドを入力する際、スキーマに定義されたフィールドをプルダウンで選択できます。
* スキーマを定義しない場合は、従来と同様に全てのフィールドを読み込みます。

### サポートするデータタイプ

| データタイプ | 説明 |
|---|---|
| String | 文字列 |
| Integer | 32ビット整数 |
| Long | 64ビット整数 |
| Float | 32ビット浮動小数点 |
| Double | 64ビット浮動小数点 |
| Boolean | 真/偽 |
| Timestamp | 日付と時間 |
| Array | 配列 |

### スキーマの定義

* Sourceノードの**Codec**タブでスキーマを定義できます。
* 次のコーデックを使用する場合、Sourceノードでスキーマを定義できます。
    * JSON
* PLAINコーデックはデータが`message`フィールドに固定でマッピングされるため、該当フィールドのみ定義可能です。
* フィールド名とデータタイプを追加してスキーマを構成します。
* スキーマを定義すると、フローの実行時に定義されたフィールドのみを選択的にパースします。

### スキーマの伝播及び変換

* Sourceノードで定義したスキーマは、接続された下位ノードに自動的に伝播されます。
* Filterノードのプロパティに応じてスキーマが自動的に変換されます。

### スキーマベースのフィールド選択

* 上位の全てのSourceノードでスキーマが定義されている場合、フィールド入力時にプルダウンでフィールド一覧が表示されます。
* スキーマが定義されていない場合は、従来と同様にテキストで直接入力します。

## Source

フローにデータを取り込むエンドポイントを定義するノードタイプです。

### 実行モード

* Sourceノードには実行モードが存在し、BATCHモードとSTREAMINGモードに分かれます。
    * STREAMINGモード: フローを終了せずにリアルタイムでデータを処理します。
    * BATCHモード: 決められたデータを処理した後、フローを終了します。
* Sourceノードごとにサポートする実行モードが異なります。

### Sourceノードの共通設定

| プロパティ名 | デフォルト値 | データタイプ | 説明 | 備考 |
| --- | --- | --- | --- | --- |
| ID | - | string | ノードのIDを設定します。<br/>このプロパティに定義された値で、チャートボードにノード名を表示します。 |  |

## Source > (NHN Cloud) Log & Crash Search

### ノードの説明

* (NHN Cloud) Log & Crash Searchノードは、Log & Crash Searchからログを読み込むノードです。
* ノードにログの照会開始時間を設定できます。設定しない場合は、フローを開始した時点からログを読み込みます。
* ノードに終了時間を入力しない場合は、ストリーミング形式でログを読み込みます。終了時間を入力した場合は、終了時間までのログを読み込み、フローを終了します。
* 現在、セッションログとクラッシュログはサポートしていません。
* Log & Crash Searchのログ検索APIのトークンに影響を受けます。
    * トークンが不足している場合は、Log & Crash Searchサービスにお問い合わせください。

### 実行モード
* STREAMING: `照会開始時間`以降のデータを継続して処理します。
* BATCH: `照会開始時間`、`照会終了時間`の間に該当するデータを全て処理し、フローを終了します。


### プロパティの説明

| プロパティ名 | デフォルト値 | データタイプ | 説明 | 備考 |
|-----------|---------------------|--------|--------------------------------------------------------------------------------------------------------------------------------------------------------|----|
| Appkey | - | string | Log & Crash Searchのアプリキーを入力します。 |  |
| SecretKey | - | string | Log & Crash Searchのシークレットキーを入力します。 |  |
| 照会開始時間 | {{executionTime}} | string | ログ照会の開始時間を入力します。オフセットが含まれたISO 8601形式、または[DSL](#domain-specific-languagedsl)形式で入力する必要があります。<br/>例: 2025-07-23T11:23:00+09:00、{{ executionTime }} |  |
| 照会終了時間 | - | string | ログ照会の終了時間を入力します。オフセットが含まれたISO 8601形式、または[DSL](#domain-specific-languagedsl)形式で入力する必要があります。<br/>例: 2025-07-23T11:23:00+09:00、{{ executionTime }} |  |
| 検索クエリ | * | string | Log & Crash Searchの照会リクエスト時に使用する検索クエリを入力します。詳細なクエリの作成方法については、Log & Crash Searchサービスの「Luceneクエリガイド」をご参照ください。 |  |

### コーデック別のメッセージ取り込み

* Log & Crash Searchは基本的にJSON形式のデータを扱います。
* Log & Crash Searchログの各フィールドを活用したい場合は、JSONコーデックを使用することをおすすめします。

サポートするコーデック
* [JSONコーデック](./codec-config-guide.md#json) - JSON形式データのパース

## Source > (NHN Cloud) CloudTrail

### ノードの説明

* (NHN Cloud) CloudTrailは、CloudTrailからデータを読み込むノードです。
* ノードにデータの照会開始時間を設定できます。設定しない場合は、フローを開始した時点からデータを読み込みます。
* ノードに終了時間を入力しない場合は、ストリーミング形式でデータを読み込みます。終了時間を入力した場合は、終了時間までのデータを読み込み、フローを終了します。

### 実行モード

* STREAMING: `照会開始時間`以降のデータを継続して処理します。
* BATCH: `照会開始時間`、`照会終了時間`の間に該当するデータを全て処理し、フローを終了します。

### プロパティの説明

| プロパティ名 | デフォルト値 | データタイプ | 説明 | 備考 |
|--------------------|---------------------|--------|---------------------------------------------------------------------------------------------------------------------------------------------------------|----|
| Appkey | - | string | CloudTrailのアプリキーを入力します。 |  |
| User Access Key ID | - | string | ユーザーアカウントのUser Access Key IDを入力します。 |  |
| Secret Access Key | - | string | ユーザーアカウントのUser Secret Keyを入力します。 |  |
| 照会開始時間 | {{executionTime}} | string | データ照会の開始時間を入力します。オフセットが含まれたISO 8601形式、または[DSL](#domain-specific-languagedsl)形式で入力する必要があります。<br/>例: 2025-07-23T11:23:00+09:00、{{ executionTime }} |  |
| 照会終了時間 | - | string | データ照会の終了時間を入力します。オフセットが含まれたISO 8601形式、または[DSL](#domain-specific-languagedsl)形式で入力する必要があります。<br/>例: 2025-07-23T11:23:00+09:00、{{ executionTime }} |  |
| イベントタイプ | * | string | 照会するイベントIDを入力します。 |  |

### コーデック別のメッセージ取り込み

* CloudTrailは基本的にJSON形式のデータを扱います。
* CloudTrailデータの各フィールドを活用したい場合は、JSONコーデックを使用することを推奨します。

サポートするコーデック
* [JSONコーデック](./codec-config-guide.md#json) - JSON形式データのパース

## Source > (NHN Cloud) Object Storage

### ノードの説明

* NHN CloudのObject Storageからデータを入力として受け取るノードです。
* オブジェクトの作成時間を基準に、最も早く作成されたオブジェクトからデータを読み込みます。

### 実行モード
* STREAMING: `リストの更新周期`ごとにオブジェクトのリストを更新し、新しく追加されたオブジェクトを読み込んでデータを処理します。
* BATCH: フローの開始時点でオブジェクトのリストを一度読み込んだ後、オブジェクトを読み込んでデータを処理し、フローを終了します。

### プロパティの説明

| プロパティ名 | デフォルト値 | データタイプ | 説明 | 備考 |
| --- |---------| --- | --- | --- |
| バケット | - | string | データを読み込むバケット名を入力します。 |  |
| リージョン | - | string | ストレージに設定されたリージョン情報を入力します。 |  |
| シークレットキー | - | string | S3が発行した認証情報のシークレットキーを入力します。 |  |
| アクセスキー | - | string | S3が発行した認証情報のアクセスキーを入力します。 |  |
| リストの更新周期 | 60 | number | バケットに含まれるオブジェクトリストの更新周期を入力します。 |  |
| Prefix | - | string | 読み込むオブジェクトのプレフィックスを入力します。 |  |
| 除外するキーパターン | - | string | 読み込まないオブジェクトのパターンを入力します。 |  |

### コーデック別のメッセージ取り込み

サポートするコーデック
* [PLAINコーデック](./codec-config-guide.md#plain) - オリジナルデータの文字列の保存
* [JSONコーデック](./codec-config-guide.md#json) - JSON形式データのパース

## Source > (NHN Cloud) Data Lake Storage

### ノードの説明
* NHN CloudのData Lake Storageからデータを入力するノードです。

### 実行モード
* STREAMING：`リスト更新サイクル`ごとにオブジェクトリストを更新し、新しく追加されたオブジェクトを読み込んでデータを処理します。
* BATCH：フロー開始時点にオブジェクトリストを一度読み込んだ後、オブジェクトを読み込んでデータを処理し、フローを終了します。

### プロパティの説明
| プロパティ名 | デフォルト値 | データ型 | 説明 | 備考 |
| --- |---------| --- | --- | --- |
| バケット | - | string | データを読み取るバケット名を入力します。 | |
| リージョン | - | string | リポジトリに設定されたリージョン情報を入力します。 | |
| シークレットキー | - | string | S3が発行した認証情報のシークレットキーを入力します。 | |
| アクセスキー | - | string | S3が発行した認証情報のアクセスキーを入力します。 | |
| リスト更新サイクル | 60 | number | バケットに含まれるオブジェクトリストの更新サイクルを入力します。 | |
| Prefix | - | string | 読み取るオブジェクトのプレフィックスを入力します。 | |
| 除外するキーパターン | - | string | 読み取らないオブジェクトのパターンを入力します。 | |

### コーデック別のメッセージ入力
サポートコーデック
* [PLAINコーデック](./codec-config-guide.md#plain) - 元データ文字列の保存
* [JSONコーデック](./codec-config-guide.md#json) - JSON形式データの解析

## Source > (Amazon) S3

### ノードの説明

* S3からデータを入力として受け取るノードです。
* オブジェクトの作成時間を基準に、最も早く作成されたオブジェクトからデータを読み込みます。

### 実行モード
* STREAMING: `リストの更新周期`ごとにオブジェクトのリストを更新し、新しく追加されたオブジェクトを読み込んでデータを処理します。
* BATCH: フローの開始時点でオブジェクトリストを一度更新した後、オブジェクトを読み込んでデータを処理し、フローを終了します。

### プロパティの説明

| プロパティ名 | デフォルト値 | データタイプ | 説明 | 備考 |
|---------------|--------------------------------|---------|------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| エンドポイント | - | string | S3ストレージのエンドポイントを入力します。 | HTTP、HTTPS URLの形式でのみ入力可能です。 |
| バケット | - | string | データを読み込むバケット名を入力します。 |  |
| リージョン | - | string | ストレージに設定されたリージョン情報を入力します。 |  |
| シークレットキー | - | string | S3が発行した認証情報のシークレットキーを入力します。 |  |
| アクセスキー | - | string | S3が発行した認証情報のアクセスキーを入力します。 |  |
| リストの更新周期 | 60 | number | バケットに含まれるオブジェクトリストの更新周期を入力します。 |  |
| Prefix | - | string | 読み込むオブジェクトのプレフィックスを入力します。 |  |
| 除外するキーパターン | - | string | 読み込まないオブジェクトのパターンを入力します。 |  |
| パス方式のリクエスト | false | boolean | パス方式のリクエストを使用するかどうかを決定します。 |  |

!!! danger "注意"
    * (Amazon) S3ノードを使用してNHN Cloud Object Storageに接続する場合は、**パス方式のリクエスト**を`true`に設定する必要があります。


### コーデック別のメッセージ取り込み

サポートするコーデック
* [PLAINコーデック](./codec-config-guide.md#plain) - オリジナルデータの文字列の保存
* [JSONコーデック](./codec-config-guide.md#json) - JSON形式データのパース

## Source > (NHN Cloud) EasyQueue

### ノードの説明
NHN CloudのEasyQueueからデータを受信するノードです。

### 実行モード
STREAMING：キューに新しいメッセージが到着するたびにデータを処理します。

### プロパティの説明
| プロパティ名 | デフォルト値 | データ型 | 説明 | 備考 |
| --- | --- | --- | --- | --- |
| アプリキー | - | string | EasyQueueのアプリキーを入力します。 | |
| User Access Key ID | - | string | ユーザーアカウントのUser Access Key IDを入力します。 | |
| Secret Access Key | - | string | ユーザーアカウントのUser Secret Keyを入力します。 | |
| ブローカーサーバー一覧 | - | string | Kafkaブローカーサーバーを入力します。サーバーが複数ある場合はカンマ(`,`)で区切ります。 | [Kafka公式ドキュメント](https://kafka.apache.org/39/configuration/consumer-configs/)の`bootstrap.servers`プロパティを参照<br/>例：10.100.1.1:9092,10.100.1.2:9092 |
| コンシューマーグループID | dataflow | string | Kafka Consumer Groupを識別するIDを入力します。 | [Kafka公式ドキュメント](https://kafka.apache.org/39/configuration/consumer-configs/)の`group.id`プロパティを参照 |
| トピック一覧 | - | array of strings | メッセージを受信するKafkaトピック一覧を入力します。 | |
| トピックパターン | - | string | メッセージを受信するKafkaトピックパターンを入力します。 | 例：`*-messages` |
| 内部トピックの除外有無 | true | boolean | __consumer_offsetsなどの内部トピックを除外します。 | [Kafka公式ドキュメント](https://kafka.apache.org/39/configuration/consumer-configs/)の`exclude.internal.topics`プロパティを参照<br/>受信対象から`__consumer_offsets`などの内部トピックを除外します。 |
| クライアントID | dataflow | string | Kafka Consumerを識別するIDを入力します。 | [Kafka公式ドキュメント](https://kafka.apache.org/39/configuration/consumer-configs/)の`client.id`プロパティを参照 |
| 分離レベル | read_committed | enum | コンシューマーがトランザクションがコミットされていないメッセージまで読み取るか、コミットされたメッセージのみ読み取るかを決定します。 | [Kafka公式ドキュメント](https://kafka.apache.org/39/configuration/consumer-configs/)の`isolation.level`プロパティを参照<br/>read_uncommitted：全てのメッセージをオフセット順に読み取ります。<br/>read_committed：コミットされたトランザクションのメッセージのみ読み取ります。 |
| パーティション割り当てポリシー | ["RANGE", "COOPERATIVE_STICKY"] | array of strings | Kafkaからメッセージを受信する際、コンシューマーグループにどのようにパーティションを割り当てるかを決定します。 | [Kafka公式ドキュメント](https://kafka.apache.org/39/configuration/consumer-configs/)の`partition.assignment.strategy`プロパティを参照<br/>org.apache.kafka.clients.consumer.RangeAssignor<br/>org.apache.kafka.clients.consumer.RoundRobinAssignor<br/>org.apache.kafka.clients.consumer.StickyAssignor<br/>org.apache.kafka.clients.consumer.CooperativeStickyAssignor |
| オフセット設定 | latest | enum | コンシューマーグループのオフセットを設定する基準を入力します。 | [Kafka公式ドキュメント](https://kafka.apache.org/39/configuration/consumer-configs/)の`auto.offset.reset`プロパティを参照<br/>以下の設定は全て、コンシューマーグループがすでに存在する場合は既存のオフセットを維持します。<br/>none：コンシューマーグループがなければエラーを返却します。<br/>earliest：コンシューマーグループがなければパーティションの最も古いオフセットで初期化します。<br/>latest：コンシューマーグループがなければパーティションの最も新しいオフセットで初期化します。 |
| キー逆シリアル化タイプ | STRING | enum | 受信するメッセージのキーのタイプを入力します。 | [Kafka公式ドキュメント](https://kafka.apache.org/39/configuration/consumer-configs/)の`key.deserializer`プロパティを参照 |
| メタデータ生成の有無 | false | boolean | プロパティ値がtrueの場合、メッセージに対するメタデータフィールドを生成します。メタデータは`kafka_metadata`フィールドに生成されます。 | 生成されるフィールドは次のとおりです。<br/>topic：メッセージを受信したトピック<br/>groupId：メッセージを受信するために使用したコンシューマーグループID<br/>partition：メッセージを受信したトピックのパーティション番号<br/>offset：メッセージを受信したパーティションのオフセット<br/>key：メッセージキー |
| Fetch最小サイズ | 1 | number | 1回のfetchリクエストで取得するデータの最小サイズ(byte)を入力します。 | [Kafka公式ドキュメント](https://kafka.apache.org/39/configuration/consumer-configs/)の`fetch.min.bytes`プロパティを参照 |
| 送信バッファサイズ | 131072 | number | データを送信するために使用するTCP sendバッファのサイズ(byte)を入力します。 | [Kafka公式ドキュメント](https://kafka.apache.org/39/configuration/consumer-configs/)の`send.buffer.bytes`プロパティを参照 |
| 再試行リクエストサイクル | 100 | number | 送信リクエストが失敗したときに再試行するサイクル(ms)を入力します。 | [Kafka公式ドキュメント](https://kafka.apache.org/39/configuration/consumer-configs/)の`retry.backoff.ms`プロパティを参照 |
| 巡回冗長検査 | true | boolean | メッセージのCRCを検査します。 | [Kafka公式ドキュメント](https://kafka.apache.org/39/configuration/consumer-configs/)の`check.crcs`プロパティを参照 |
| サーバー再接続サイクル | 50 | number | ブローカーサーバーへの接続が失敗したときに再試行するサイクル(ms)を入力します。 | [Kafka公式ドキュメント](https://kafka.apache.org/39/configuration/consumer-configs/)の`reconnect.backoff.ms`プロパティを参照 |
| パーティションあたりのFetch最大サイズ | 1048576 | number | パーティションあたり1回のfetchリクエストで取得する最大サイズ(byte)を入力します。 | [Kafka公式ドキュメント](https://kafka.apache.org/39/configuration/consumer-configs/)の`max.partition.fetch.bytes`プロパティを参照 |
| サーバーリクエストタイムアウト | 30000 | number | 送信リクエストに対するタイムアウト(ms)を入力します。 | [Kafka公式ドキュメント](https://kafka.apache.org/39/configuration/consumer-configs/)の`request.timeout.ms`プロパティを参照 |
| TCP受信バッファサイズ | 65536 | number | データを読み取るために使用するTCP receiveバッファのサイズ(byte)を入力します。 | [Kafka公式ドキュメント](https://kafka.apache.org/39/configuration/consumer-configs/)の`receive.buffer.bytes`プロパティを参照 |
| セッションタイムアウト | 45000 | number | コンシューマーのセッションタイムアウト(ms)を入力します。<br/>コンシューマーが該当時間内にheartbeatを送信できない場合、コンシューマーグループから除外します。 | [Kafka公式ドキュメント](https://kafka.apache.org/39/configuration/consumer-configs/)の`session.timeout.ms`プロパティを参照 |
| 最大pollメッセージ数 | 500 | number | 1回のpollリクエストで取得する最大メッセージ数を入力します。 | [Kafka公式ドキュメント](https://kafka.apache.org/39/configuration/consumer-configs/)の`max.poll.records`プロパティを参照 |
| 最大pollサイクル | 300000 | number | pollリクエスト間の最大サイクル(ms)を入力します。 | [Kafka公式ドキュメント](https://kafka.apache.org/39/configuration/consumer-configs/)の`max.poll.interval.ms`プロパティを参照 |
| Fetch最大サイズ | 52428800 | number | 1回のfetchリクエストで取得する最大サイズ(byte)を入力します。 | [Kafka公式ドキュメント](https://kafka.apache.org/39/configuration/consumer-configs/)の`fetch.max.bytes`プロパティを参照 |
| Fetch最大待機時間 | 500 | number | `Fetch最小サイズ`の設定分のデータが集まらない場合、fetchリクエストを送信する待機時間(ms)を入力します。 | [Kafka公式ドキュメント](https://kafka.apache.org/39/configuration/consumer-configs/)の`fetch.max.wait.ms`プロパティを参照 |
| コンシューマーヘルスチェックサイクル | 3000 | number | コンシューマーがheartbeatを送信するサイクル(ms)を入力します。 | [Kafka公式ドキュメント](https://kafka.apache.org/39/configuration/consumer-configs/)の`heartbeat.interval.ms`プロパティを参照 |
| メタデータ更新サイクル | 300000 | number | パーティション、ブローカーサーバーの状態などを更新するサイクル(ms)を入力します。 | [Kafka公式ドキュメント](https://kafka.apache.org/39/configuration/producer-configs/)の`metadata.max.age.ms`プロパティを参照 |
| IDLEタイムアウト | 540000 | number | データ送信がない接続を閉じる待機時間(ms)を入力します。 | [Kafka公式ドキュメント](https://kafka.apache.org/39/configuration/consumer-configs/)の`connections.max.idle.ms`プロパティを参照 |
| 追加設定 | - | hash | Kafka接続に使用する追加のConsumer設定を入力します。 | [Kafka公式ドキュメント](https://kafka.apache.org/39/configuration/consumer-configs/)を参照 |

### コーデック別のメッセージ入力
サポートコーデック
* [PLAINコーデック](./codec-config-guide.md#plain) - 元データ文字列の保存
* [JSONコーデック](./codec-config-guide.md#json) - JSON形式データの解析

## Source > (Apache) Kafka

### ノードの説明

Kafkaからデータを受信するノードです。

### 実行モード
STREAMING: トピックに新しいメッセージが届くたびにデータを処理します。

!!! danger "注意"
    * KafkaノードはBATCHモードをサポートしていません。

### プロパティの説明

| プロパティ名 | デフォルト値 | データタイプ | 説明 | 備考 |
|------------------|-----------------------------------|------------------|---------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ブローカーサーバーの一覧 | - | string | Kafkaブローカーサーバーを入力します。サーバーが複数ある場合はコンマ(`,`)で区切ります。 | [Kafka公式ドキュメント](https://kafka.apache.org/39/configuration/consumer-configs/)の`bootstrap.servers`プロパティを参照 <br/>例: 10.100.1.1:9092,10.100.1.2:9092 |
| コンシューマーグループID | dataflow | string | Kafka Consumer Groupを識別するIDを入力します。 | [Kafka公式ドキュメント](https://kafka.apache.org/39/configuration/consumer-configs/)の`group.id`プロパティを参照 |
| トピック一覧 | - | array of strings | メッセージを受信するKafkaトピックの一覧を入力します。 |  |
| トピックパターン | - | string | メッセージを受信するKafkaトピックのパターンを入力します。 | 例: `*-messages` |
| 内部トピックの除外 | true | boolean | __consumer_offsetsなどの内部トピックを除外します。 | [Kafka公式ドキュメント](https://kafka.apache.org/39/configuration/consumer-configs/)の`exclude.internal.topics`プロパティを参照 <br/>受信対象から`__consumer_offsets`などの内部トピックを除外します。 |
| クライアントID | dataflow | string | Kafka Consumerを識別するIDを入力します。 | [Kafka公式ドキュメント](https://kafka.apache.org/39/configuration/consumer-configs/)の`client.id`プロパティを参照 |
| パーティション割り当てポリシー | ["RANGE", "COOPERATIVE_STICKY"] | array of strings | Kafkaからメッセージを受信する際、コンシューマーグループにパーティションをどのように割り当てるかを決定します。 | [Kafka公式ドキュメント](https://kafka.apache.org/39/configuration/consumer-configs/)の`partition.assignment.strategy`プロパティを参照 <br/>org.apache.kafka.clients.consumer.RangeAssignor<br/>org.apache.kafka.clients.consumer.RoundRobinAssignor<br/>org.apache.kafka.clients.consumer.StickyAssignor<br/>org.apache.kafka.clients.consumer.CooperativeStickyAssignor |
| オフセット設定 | latest | enum | コンシューマーグループのオフセットを設定する基準を入力します。 | [Kafka公式ドキュメント](https://kafka.apache.org/39/configuration/consumer-configs/)の`auto.offset.reset`プロパティを参照 <br/>以下の設定は全て、コンシューマーグループがすでに存在する場合は既存のオフセットを維持します。<br/>none: コンシューマーグループが存在しない場合はエラーを返します。<br/>earliest: コンシューマーグループが存在しない場合は、パーティションの最も古いオフセットで初期化します。<br/>latest: コンシューマーグループが存在しない場合は、パーティションの最も新しいオフセットで初期化します。 |
| キーデシリアライズのタイプ | STRING | enum | 受信するメッセージのキーのタイプを入力します。 | [Kafka公式ドキュメント](https://kafka.apache.org/39/configuration/consumer-configs/)の`key.deserializer`プロパティを参照 |
| メタデータ生成の有無 | false | boolean | プロパティ値がtrueの場合、メッセージに対するメタデータフィールドを生成します。メタデータは`kafka_metadata`フィールドに生成されます。 | 生成されるフィールドは次のとおりです。<br/>topic: メッセージを受信したトピック<br/>groupId: メッセージの受信に使用したコンシューマーグループID<br/>partition: メッセージを受信したトピックのパーティション番号<br/>offset: メッセージを受信したパーティションのオフセット<br/>key: メッセージキー |
| Fetchの最小サイズ | 1 | number | 1回のfetchリクエストで取得するデータの最小サイズ(byte)を入力します。 | [Kafka公式ドキュメント](https://kafka.apache.org/39/configuration/consumer-configs/)の`fetch.min.bytes`プロパティを参照 |
| 送信バッファーサイズ | 131072 | number | データの送信に使用するTCP sendバッファーのサイズ(byte)を入力します。 | [Kafka公式ドキュメント](https://kafka.apache.org/39/configuration/consumer-configs/)の`send.buffer.bytes`プロパティを参照 |
| 再試行リクエスト周期 | 100 | number | 送信リクエストが失敗した場合に再試行する周期(ms)を入力します。 | [Kafka公式ドキュメント](https://kafka.apache.org/39/configuration/consumer-configs/)の`retry.backoff.ms`プロパティを参照 |
| 巡回冗長検査 | true | boolean | メッセージのCRCを検査します。 | [Kafka公式ドキュメント](https://kafka.apache.org/39/configuration/consumer-configs/)の`check.crcs`プロパティを参照 |
| サーバー再接続周期 | 50 | number | ブローカーサーバーへの接続に失敗した場合に再試行する周期(ms)を入力します。 | [Kafka公式ドキュメント](https://kafka.apache.org/39/configuration/consumer-configs/)の`reconnect.backoff.ms`プロパティを参照 |
| パーティションあたりのFetch最大サイズ | 1048576 | number | パーティションあたり1回のfetchリクエストで取得する最大サイズ(byte)を入力します。 | [Kafka公式ドキュメント](https://kafka.apache.org/39/configuration/consumer-configs/)の`max.partition.fetch.bytes`プロパティを参照 |
| サーバーリクエストのタイムアウト | 30000 | number | 送信リクエストに対するタイムアウト(ms)を入力します。 | [Kafka公式ドキュメント](https://kafka.apache.org/39/configuration/consumer-configs/)の`request.timeout.ms`プロパティを参照 |
| TCP受信バッファーサイズ | 65536 | number | データの読み込みに使用するTCP receiveバッファーのサイズ(byte)を入力します。 | [Kafka公式ドキュメント](https://kafka.apache.org/39/configuration/consumer-configs/)の`receive.buffer.bytes`プロパティを参照 |
| セッションタイムアウト | 45000 | number | コンシューマーのセッションタイムアウト(ms)を入力します。<br/>コンシューマーが該当の時間内にheartbeatを送信できない場合、コンシューマーグループから除外されます。 | [Kafka公式ドキュメント](https://kafka.apache.org/39/configuration/consumer-configs/)の`session.timeout.ms`プロパティを参照 |
| 最大pollメッセージ数 | 500 | number | 1回のpollリクエストで取得する最大メッセージ数を入力します。 | [Kafka公式ドキュメント](https://kafka.apache.org/39/configuration/consumer-configs/)の`max.poll.records`プロパティを参照 |
| 最大poll周期 | 300000 | number | pollリクエスト間の最大周期(ms)を入力します。 | [Kafka公式ドキュメント](https://kafka.apache.org/39/configuration/consumer-configs/)の`max.poll.interval.ms`プロパティを参照 |
| Fetch最大サイズ | 52428800 | number | 1回のfetchリクエストで取得する最大サイズ(byte)を入力します。 | [Kafka公式ドキュメント](https://kafka.apache.org/39/configuration/consumer-configs/)の`fetch.max.bytes`プロパティを参照 |
| Fetchの最大待機時間 | 500 | number | `Fetchの最小サイズ`設定分のデータが集まらなかった場合、fetchリクエストを送信する待機時間(ms)を入力します。 | [Kafka公式ドキュメント](https://kafka.apache.org/39/configuration/consumer-configs/)の`fetch.max.wait.ms`プロパティを参照 |
| コンシューマーヘルスチェック周期 | 3000 | number | コンシューマーがheartbeatを送信する周期(ms)を入力します。 | [Kafka公式ドキュメント](https://kafka.apache.org/39/configuration/consumer-configs/)の`heartbeat.interval.ms`プロパティを参照 |
| メタデータ更新周期 | 300000 | number | パーティション、ブローカーサーバーの状態などを更新する周期(ms)を入力します。 | [Kafka公式ドキュメント](https://kafka.apache.org/39/configuration/producer-configs/)の`metadata.max.age.ms`プロパティを参照 |
| IDLEタイムアウト | 540000 | number | データ送信がない接続を閉じる待機時間(ms)を入力します。 | [Kafka公式ドキュメント](https://kafka.apache.org/39/configuration/consumer-configs/)の`connections.max.idle.ms`プロパティを参照 |
| 分離レベル | read_committed | enum | コンシューマーがトランザクションがコミットされていないメッセージまで読み取るか、コミットされたメッセージのみ読み取るかを決定します。 | [Kafka公式ドキュメント](https://kafka.apache.org/39/configuration/consumer-configs/)の`isolation.level`プロパティを参照<br/>read_uncommitted：全てのメッセージをオフセット順に読み取ります。<br/>read_committed：コミットされたトランザクションのメッセージのみ読み取ります。 |
| 追加設定 | - | hash | Kafka接続に使用する追加のConsumer設定を入力します。 | [Kafka公式ドキュメント](https://kafka.apache.org/39/configuration/consumer-configs/)を参照 |

### コーデック別のメッセージ取り込み

サポートするコーデック
* [PLAINコーデック](./codec-config-guide.md#plain) - オリジナルデータの文字列の保存
* [JSONコーデック](./codec-config-guide.md#json) - JSON形式データのパース

## Filter

取り込まれたデータをどのように処理するかを定義するノードタイプです。

### Filterノードの共通設定

| プロパティ名 | デフォルト値 | データタイプ | 説明 | 備考 |
| --- | --- | --- | --- | --- |
| ID | - | string | ノードのIDを設定します。<br/>このプロパティに定義された値で、チャートボードにノード名を表示します。 |  |

## Filter > Cipher

### ノードの説明

* メッセージフィールドの値を暗号化/復号するノードです。
* 暗号化キーはSecure Key Managerの共通鍵を参照します。
    * Secure Key Managerの共通鍵は、Secure Key Manager WebコンソールやSecure Key Managerのキー追加APIで生成できます。
    * 1つのフローに複数のCipherノードが含まれる場合でも、全てのCipherノードは必ず1つのSecure Key Managerキーの参照のみを参照できます。

### プロパティの説明

| プロパティ名 | デフォルト値 | データタイプ | 説明 | 備考 |
|--------|---------|---------|-------------------------------------------|------------------|
| モード | - | enum | 暗号化モードと復号モードのいずれかを選択します。 | 一覧から1つを選択します。 |
| アプリキー | - | string | 暗号化/復号に使用するキーを保存したSKMのアプリキーを入力します。 |  |
| キーID | - | string | 暗号化/復号に使用するキーを保存したSKMのキーIDを入力します。 |  |
| キーバージョン | - | string | 暗号化/復号に使用するキーを保存したSKMのキーバージョンを入力します。 |  |
| ソースフィールド | - | string | 暗号化/復号するフィールド名を入力します。 | スキーマ定義時にプルダウンを提供 |
| 保存するフィールド | - | string | 暗号化/復号の結果を保存するフィールド名を入力します。 |  |
| 上書き | false | boolean | 指定した対象フィールド名に値が存在する場合、その値を上書きするかどうかを選択します。 |  |

### encryptの例

#### 条件

* mode → `encrypt`
* アプリキー → `SKMのアプリキー`
* キーID → `SKM共通鍵のID`
* キーバージョン → `1`
* ソースフィールド → message
* 保存するフィールド → encrypted\_message

#### 入力メッセージ

``` js
{
    "message": "this is plain message"
}
```

#### 出力メッセージ

``` js
{
    "message": "this is plain message",
    "encrypted_message": "oZA6CHd4OwjPuS+MW0ydCU9NqbPQHGbPf4rll2ELzB8y5pyhxF6UhWZq5fxrt0/e"
}
```

### decryptの例

#### 条件

* mode → `decrypt`
* アプリキー → `SKMのアプリキー`
* キーID → `SKM共通鍵のID`
* キーバージョン → `1`
* ソースフィールド → message
* 保存するフィールド → decrypted\_message

#### 入力メッセージ

``` js
{
    "message": "oZA6CHd4OwjPuS+MW0ydCU9NqbPQHGbPf4rll2ELzB8y5pyhxF6UhWZq5fxrt0/e"
}
```

#### 出力メッセージ

``` js
{
    "message": "oZA6CHd4OwjPuS+MW0ydCU9NqbPQHGbPf4rll2ELzB8y5pyhxF6UhWZq5fxrt0/e",
    "decrypted_message": "this is plain message"
}
```

## Filter > CSV

### ノードの説明
CSV形式のメッセージをパースしてフィールドに保存するノードです。

### プロパティの説明

| プロパティ名 | デフォルト値 | データタイプ | 説明 | 備考 |
|-----------|--------------------------|------------------|------------------------------------------------|--------------------------|
| 保存するフィールド | - | string | CSVのパース結果を保存するフィールド名を入力します。 |  |
| Quote | " | string | カラムフィールドを区切る文字を入力します。 |  |
| 先頭行を無視するかどうか | false | boolean | プロパティ値がtrueの場合、読み込んだデータのうち先頭行に入力されたカラム名を無視します。 |  |
| 区切り文字 | , | string | カラムを区切る文字列を入力します。 |  |
| ソースフィールド | - | string | CSVパースするフィールド名を入力します。 |  |
| スキーマ | - | hash | 各カラム名とデータタイプをdictionary形式で入力します。 | `スキーマ入力方法`を参照 |
| 上書き | false | boolean | trueの場合、CSVのパース結果が保存するフィールドや既存のフィールドと重複する場合は上書きします。 |  |
| 元のフィールドの削除 | false | boolean | CSVのパースが完了したらソースフィールドを削除します。パースに失敗した場合は維持します。 |  |

#### スキーマ入力方法
カラムタイプはサポートしておらず、全てのカラムとデータタイプをスキーマとして入力します。


### データタイプの変換が不要なCSVパースの例

#### 条件

* ソースフィールド → `message`
* スキーマ → `{"one": "string", "two": "string", "t hree": "string"}`

#### 入力メッセージ

```js
{
    "message": "hey,foo,\"bar baz\""
}
```

#### 出力メッセージ

```js
{
    "message": "hey,foo,\"bar baz\"",
    "one": "hey",
    "t hree": "bar baz",
    "two": "foo"
}
```


### データタイプの変換が必要なCSVパースの例

#### 条件

* ソースフィールド → `message`
* スキーマ → `{"one": "string", "two": "integer", "t hree": "boolean"}`

#### 入力メッセージ

```js
{
    "message": "\"wow hello world!\", 2, false"
}
```

#### 出力メッセージ

```js
{
    "message": "\"wow hello world!\", 2, false",
    "one": "wow hello world!",
    "t hree": false,
    "two": 2
}
```

## Filter > JSON

### ノードの説明

JSON文字列をパースして指定されたフィールドに保存するノードです。

### プロパティの説明

| プロパティ名 | デフォルト値 | データタイプ | 説明 | 備考 |
| --- | --- | --- | --- | --- |
| ソースフィールド | - | string | JSON文字列をパースするフィールド名を入力します。 |  |
| 保存するフィールド | - | string | JSONのパース結果を保存するフィールド名を入力します。<br/>プロパティ値を指定しない場合、結果はrootフィールドに保存されます。 |  |
| 上書き | false | boolean | trueの場合、JSONのパース結果が保存するフィールドや既存のフィールドと重複すれば上書きします。 |  |
| 元のフィールドの削除 | false | boolean | JSONのパースが完了するとソースフィールドを削除します。パースに失敗した場合は維持します。 |  |
| スキーマ | - | hash | 各フィールド名とデータタイプをdictionary形式で入力します。 | `スキーマ入力方法`を参照 |

#### スキーマ入力方法
カラムタイプはサポートしておらず、全てのカラムとデータタイプをスキーマとして入力します。

### データタイプの変換が不要なJSONパースの例

#### 条件

* ソースフィールド → `message`
* 保存するフィールド → `json_parsed_message`

#### 入力メッセージ

```js
{
    "message": "{\"json\": \"parse\", \"example\": \"string\"}"
}
```

#### 出力メッセージ

```js
{
    "json_parsed_message": {
        "json": "parse",
        "example": "string"
    },
    "message": "{\"json\": \"parse\", \"example\": \"string\"}"
}
```

### データタイプの変換が必要なJSONパースの例

#### 条件

* ソースフィールド → `message`
* 保存するフィールド → `json_parsed_message`
* スキーマ → `{"json": "string", "example": "integer"}`

#### 入力メッセージ

```js
{
    "message": "{\"json\": \"parse\", \"example\": \"123\"}"
}
```

#### 出力メッセージ

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

### ノードの説明

Date文字列をパースしてtimestamp形式で保存するノードです。

### プロパティの説明

| プロパティ名 | デフォルト値 | データタイプ | 説明 | 備考 |
|--------|----------------------------------|------------------|--------------------------------------|-----------------------------------------------------------------|
| ソースフィールド | - | string | 文字列を取得するためのフィールド名を入力します。 | スキーマ定義時にプルダウンを提供 |
| 形式 | - | array of strings | 文字列を取得するための形式を入力します。 | 事前に定義された形式は次のとおりです。<br/>ISO8601, UNIX, UNIX_MS |
| Locale | ko_KR | string | Date文字列の分析に使用するLocaleを入力します。 | 例: en, en-US, ko_KR |
| 保存するフィールド | - | string | Date文字列のパース結果を保存するフィールド名を入力します。 |  |
| タイムゾーン | Asia/Seoul | string | 日付のタイムゾーンを入力します。 | 例: Asia/Seoul |

### Date文字列のパース例

#### 条件

* ソースフィールド → `message`
* 形式 → `["yyyy-MM-dd HH:mm:ssZ", "ISO8601"]`
* 保存するフィールド → `time`
* タイムゾーン → `Asia/Seoul`

#### 入力メッセージ

```js
{
    "message": "2017-03-16T17:40:00"
}
```

#### 出力メッセージ

```js
{
    "message": "2017-03-16T17:40:00",
    "time": 2022-04-04T09:08:01.222Z
}
```

## Filter > UUID

### ノードの説明

UUIDを生成してフィールドに保存するノードです。

### プロパティの説明

| プロパティ名 | デフォルト値 | データタイプ | 説明 | 備考 |
| --- | --- | --- | --- | --- |
| UUID保存フィールド | - | string | UUID生成の結果値を保存するフィールド名を入力します。 |  |
| 上書き | false | boolean | 指定したフィールド名に値が存在する場合、その値を上書きするかどうかを選択します。 |  |

### UUID生成の例

#### 条件

UUID保存フィールド → `userId`

#### 入力メッセージ

```js
{
    "message": "uuid test message"
}
```

#### 出力メッセージ

```js
{
    "userId": "70186b1e-bdec-43d6-8086-ed0481b59370",
    "message": "uuid test message"
}
```

## Filter > Convert

### ノードの説明

特定のフィールドのデータタイプを変換するノードです。

### プロパティの説明

| プロパティ名 | デフォルト値 | データタイプ | 説明 | 備考 |
|-------|-----|--------|-----------------------------------------------------------------------------|----|
| 対象フィールド | - | string | データタイプを変換する対象フィールドを入力します。 | スキーマ定義時にプルダウンを提供 |
| 変換タイプ | - | enum | 変換するデータタイプを選択します。 <br/> * 提供するタイプ: `STRING, INTEGER, FLOAT, DOUBLE, BOOLEAN` |  |

### データ変換の例

#### 条件

* 対象フィールド → `message`
* 変換タイプ → `INTEGER`

#### 入力メッセージ

```js
{
    "message": "2025"
}
```

#### 出力メッセージ

```js
{
    "message": 2025
}
```


## Filter > Coerce

### ノードの説明

null値をデフォルト値に置き換えるノードです。

### プロパティの説明

| プロパティ名 | デフォルト値 | データタイプ | 説明 | 備考 |
| --- | --- | --- | --- | --- |
| 対象フィールド | - | string | デフォルト値を指定するフィールド名を入力します。 | スキーマ定義時にプルダウンを提供 |
| デフォルト値 | - | string | デフォルト値を入力します。 |  |

### デフォルト値設定の例

#### 条件
* 対象フィールド → `fieldname`
* デフォルト値 → `default_value`

#### 入力メッセージ

```json
{
    "fieldname": null
}
```

#### 出力メッセージ

```json
{
    "fieldname": "default_value"
}
```

## Filter > Copy

### ノードの説明

既存のフィールドを別のフィールドにコピーするノードです。

### プロパティの説明

| プロパティ名 | デフォルト値 | データタイプ | 説明 | 備考 |
| --- | --- | --- | --- | --- |
| 対象フィールド | - | string | コピーするソースフィールド名を入力します。 | スキーマ定義時にプルダウンを提供 |
| 保存するフィールド | - | string | コピーした結果を保存するフィールド名を入力します。 |  |
| 上書き | false | boolean | trueの場合、保存するフィールドがすでに存在すれば上書きします。 |  |

### 例

#### 条件
* 対象フィールド → `source_field`
* 保存するフィールド → `dest_field`

#### 入力メッセージ

```json
{
    "source_field": "Hello World!"
}

```

#### 出力メッセージ

```json
{
    "source_field": "Hello World!",
    "dest_field": "Hello World!"
}
```

## Filter > Rename

### ノードの説明

フィールド名を変更するノードです。

### プロパティの説明

| プロパティ名 | デフォルト値 | データタイプ | 説明 | 備考 |
| --- | --- | --- | --- | --- |
| ソースフィールド |  | string | 名前を変更するソースフィールドを入力します。 | スキーマ定義時にプルダウンを提供 |
| 対象フィールド | - | string | 変更するフィールド名を入力します。 |  |
| 上書き | false | boolean | trueの場合、対象フィールドがすでに存在すれば上書きします。 |  |

### 例

#### 条件
* ソースフィールド → `fieldname`
* 対象フィールド → `changed_fieldname`

#### 入力メッセージ

```json
{
    "fieldname": "Hello World!"
}
```

#### 出力メッセージ

```json
{
    "changed_fieldname": "Hello World!"
}
```

## Filter > Strip

### ノードの説明

フィールドの文字列の先頭と末尾の空白を削除するノードです。

### プロパティの説明

| プロパティ名 | デフォルト値 | データタイプ | 説明 | 備考 |
| --- | --- | --- | --- | --- |
| 対象フィールド | - | array of strings | 空白を削除する対象フィールドを入力します。 | スキーマ定義時にプルダウンを提供(複数選択) |

### 例

#### 条件
対象フィールド → `["field1", "field2"]`

#### 入力メッセージ

```json
{
    "field1": "Hello World!   ",
    "field2": "   Hello DataFlow!"
}

```

#### 出力メッセージ

```json
{
    "field1": "Hello World!",
    "field2": "Hello DataFlow!"
}

```

## Filter > Remove Fields

### ノードの説明

フィールドを削除するノードです。

### プロパティの説明

| プロパティ名 | デフォルト値 | データタイプ | 説明 | 備考 |
|--------|-----|------------------|--------------------|----|
| 削除するフィールド | - | array of strings | 削除するフィールド名の一覧を入力します。 | スキーマ定義時にプルダウンを提供(複数選択) |

### 設定例

#### 条件
削除するフィールド → `["field2", "field3"]`

#### 入力メッセージ

```json
{
    "field1": "value1",
    "field2": "value2",
    "field3": "value3",
    "field4": "value4"
}
```

#### 出力メッセージ

```json
{
    "field1": "value1",
    "field4": "value4"
}
```

## Filter > Tokenizer

### ノードの説明

正規表現を使用して文字列フィールドをトークン化するノードです。

### プロパティの説明

| プロパティ名 | デフォルト値 | データタイプ | 説明 | 備考 |
|----------|-------------|---------|-------------------------------------------------|--------------------------------------------------|
| ソースフィールド | - | string | トークン化するソースフィールド名を入力します。 |  |
| 保存するフィールド | - | string | トークン化の結果を保存するフィールド名を入力します。 |  |
| 正規表現 | \s+ | string | トークン化に使用する正規表現を入力します。 |  |
| モード | SEPARATOR | enum | トークン化のモードを選択します。 | SEPARATOR: 正規表現を区切り文字として使用<br>MATCH: 正規表現をトークンのマッチングに使用 |
| 最小トークン長 | 1 | number | トークンの最小長を入力します。最小トークン長よりも短いトークンは結果から除外されます。 |  |
| 上書き | false | boolean | trueの場合、保存するフィールドがすでに存在すれば上書きします。 |  |

### SEPARATORモードの例

#### 条件
* ソースフィールド → `src_field`
* 保存するフィールド → `target_field`
* 正規表現 → `,`
* モード → `SEPARATOR`

#### 入力メッセージ

```json
{
    "src_field": "foo,bar,baz"
}
```

#### 出力メッセージ

```json
{
    "src_field": "foo,bar,baz",
    "target_field": ["foo", "bar", "baz"]
}
```

### MATCHモードの例

#### 条件
* ソースフィールド → `src_field`
* 保存するフィールド → `target_field`
* 正規表現 → `[^,]+`
* モード → `MATCH`

#### 入力メッセージ

```json
{
    "src_field": "foo,bar,baz"
}
```

#### 出力メッセージ

```json
{
    "src_field": "foo,bar,baz",
    "target_field": ["foo", "bar", "baz"]
}
```

## Filter > Sampling

### ノードの説明

* メッセージを一定の比率で選別し、次のノードに伝達するノードです。
* 確率ベースで伝達するかどうかを決定します。したがって、メッセージの数が少ないほど、入力した比率との誤差が大きくなります。

### プロパティの説明

| プロパティ名 | デフォルト値 | データタイプ | 説明 | 備考 |
| --- | --- | --- | --- | --- |
| 比率 | - | number | メッセージを次のノードに伝達する比率を入力します。 |  |
| シード | - | number | 乱数生成時に使用するシードを入力します。シードが同じで入力メッセージが同じであれば、結果は同じになります。 |  |

## Filter > Stop Words Remover(ストップワードの削除)

### ノードの説明

文字列配列フィールドに含まれるストップワード(Stop Word)を削除するノードです。

### プロパティの説明

| プロパティ名 | デフォルト値 | データタイプ | 説明 | 備考 |
|-----------------|---------|---------|----------------------------------------------|----|
| ソースフィールド | - | string | ストップワードを削除するソースフィールド名を入力します。 |  |
| 保存するフィールド | - | string | ストップワード削除の結果を保存するフィールド名を入力します。 |  |
| 基本提供ストップワード辞書の言語 | none | enum | ストップワード削除に使用する、基本提供ストップワード辞書の言語を選択します。 |  |
| ストップワード辞書 |  | string | ストップワード削除に使用する単語一覧を入力します。各単語は改行で区切られます。 |  |
| 大文字と小文字を区別するかどうか | false | boolean | 大文字と小文字を区別するかどうかを選択します。 |  |
| 上書き | false | boolean | trueの場合、保存するフィールドがすでに存在すれば上書きします。 |  |

### 事前定義辞書
* 言語別の事前定義辞書は次のとおりです。
  * [ko](http://static.toastoven.net/prod_dataflow/ko/node-config-guide/stop_word_remover_dict_ko.txt)
  * [en](http://static.toastoven.net/prod_dataflow/ko/node-config-guide/stop_word_remover_dict_en.txt)

### 設定例

#### 条件
* ソースフィールド → `src_field`
* 保存するフィールド → `target_field`
* 辞書
```
is
a
```

#### 入力メッセージ

```json
{
    "src_field": ["hello", "world", "this", "is", "a", "test"]
}
```

#### 出力メッセージ

```json
{
  "src_field": ["hello", "world", "this", "is", "a", "test"],
  "target_field": ["hello", "world", "this", "test"]
}
```

## Filter > Pattern Extractor (Grok)

### ノードの説明

* テキストデータから構造化された情報を抽出するノードです。
* 正規表現を活用したパターンマッチングにより、ログやテキストから必要な情報を抽出します。
* Logstashと互換性のあるGrokパターン構文をサポートしており、複雑なログパースをシンプルなパターンで処理できます。
* 基本提供パターンを活用したり、カスタムパターンを作成したりすることで、様々な形式のデータをパースできます。


### プロパティの説明

| プロパティ名 | デフォルト値 | データタイプ | 説明 | 備考 |
|---|---|---|---|---|
| ソースフィールド | - | string | パターンを抽出する元のフィールド名を入力します。 |  |
| 対象フィールド | - | string | 抽出結果を保存するフィールド名を入力します。入力しない場合、結果は直接rootに追加されます。 |  |
| カスタムパターン | - | hash | 基本提供パターン以外に追加で使用するパターンを定義します。パターン名と正規表現をkey-value形式で入力します。 | 同じパターン名が基本提供パターンに存在する場合、カスタムパターンが優先的に適用され、組み込みパターンを再定義できます。 |
| パターン表現式 | - | string | データから抽出するフィールドとパターンをGrok式で入力します。 |  |
| 上書き | false | boolean | 対象フィールドにすでに値が存在する場合、抽出結果で上書きするかどうかを設定します。 |  |

!!! tip "基本提供パターン"
    頻繁に使用されるパターンを事前定義して提供します。
    日付/時間、IPアドレス、URL、ログレベルなど、様々な状況で必要となる多彩なパターンが含まれています。
    基本提供パターンは内部的に他のパターンを参照する階層構造を持つため、指定したフィールド名以外に追加フィールドが生成される場合があります。
    [基本提供パターン一覧](https://static.toastoven.net/prod_dataflow/node-config-guide/predefined_patterns.txt)を参照


### 例

#### 条件
* ソースフィールド → `log_message`
* 対象フィールド → `result`
* カスタムパターン → `{"CUSTOM_PHONE_NUMBER": "01[016789]-\d{3,4}-\d{4}", "CUSTOM_EMPLOYEE_ID": "EMP-\d{6}", "CUSTOM_ORDER_ID": "ORD-[A-Z]{3}-\d{8}"}`
* パターン表現式 → `%{TIMESTAMP_ISO8601:timestamp} %{LOGLEVEL:level} %{CUSTOM_EMPLOYEE_ID:custom_emp_id} %{CUSTOM_PHONE_NUMBER:custom_phone_number} %{CUSTOM_ORDER_ID:custom_order_id} %{GREEDYDATA:message}`

#### 入力メッセージ

```json
{
  "log_message": "2024-03-15T09:30:00.000Z INFO EMP-123456 010-1234-5678 ORD-ABC-12345678 Order processing started",
  "created_by": "DataFlow"
}

```

#### 出力メッセージ

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

Filter操作を終えたデータを書き込むエンドポイントを定義するノードタイプです。

### Sinkノードの共通設定

| プロパティ名 | デフォルト値 | データタイプ | 説明 | 備考 |
| --- | --- | --- | --- | --- |
| ID | - | string | ノードのIDを設定します。<br/>このプロパティに定義された値で、チャートボードにノード名を表示します。 |  |

## Sink > (NHN Cloud) Object Storage

### ノードの説明

* NHN CloudのObject Storageにデータをアップロードするノードです。
* 他の設定を行わずに基本設定のみで作成すると、オブジェクトは次のパスフォーマットに合わせて出力されます。
    * `/{bucket_name}/year={yyyy}/month={MM}/day={dd}/hour={HH}/part-{uuid}-{file_counter}`   
* 提供するコーデックは、JSON、LINE、Parquetです。

### プロパティの説明

| プロパティ名 | デフォルト値 | データタイプ | 説明 | 備考 |
|-----------------------|------------------------------------------------------|--------|--------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------|
| リージョン | - | enum | Object Storage商品のリージョンを入力します。 |  |
| バケット | - | string | バケット名を入力します。 |  |
| シークレットキー | - | string | S3 API認証情報のシークレットキーを入力します。 |  |
| アクセスキー | - | string | S3 API認証情報のアクセスキーを入力します。 |  |
| Prefix | /year=%{+YYYY}/month=%{+MM}/day=%{+dd}/hour=%{+HH} | string | オブジェクトのアップロード時に名前の前に付けるプレフィックスを入力します。<br/>フィールドまたは時間形式を入力できます。 | [使用可能な時間形式](https://joda-time.sourceforge.net/apidocs/org/joda/time/format/DateTimeFormat.html) |
| Prefix時間フィールド | - | string | Prefixに適用する時間フィールドを入力します。 |  |
| Prefix時間フィールドのタイプ | DATE_FILTER_RESULT | enum | Prefixに適用する時間フィールドのタイプを入力します。 | DATE_FILTER_RESULTタイプのみ可能(今後他のタイプもサポート予定) |
| Prefix時間帯 | UTC | string | Prefixに適用する時間フィールドのタイムゾーンを入力します。 |  |
| Prefix時間適用fallback | _prefix_datetime_parse_failure | string | Prefixの時間の適用に失敗した場合に代替するPrefixを入力します。 |  |
| 基準時刻 | 1 | number | オブジェクトを分割する基準となる時間を設定します。 |  |
| 基準オブジェクトサイズ | 5242880 | number | オブジェクトを分割する基準となる大きさ(単位： byte)を設定します。 |  |
| 非アクティブ間隔 | 1 | number | データ取り込みがない状態が続く場合、オブジェクトを分割する基準時間を設定します。 | 設定した時間の間データの取り込みがないと現在のオブジェクトがアップロードされ、その後新しく取り込まれるデータは新しいオブジェクトに書き込まれます。 |

### コーデック別の出力例

サポートするコーデック
* [JSONコーデック](./codec-config-guide.md#json) - JSON形式データのパース
* [LINEコーデック](./codec-config-guide.md#line) - 行単位でのメッセージ処理
* [Parquetコーデック](./codec-config-guide.md#parquet) - データをParquet形式で圧縮 

### Prefixの例 - フィールド

#### 条件

* バケット → `obs-test-container`
* Prefix → `/dataflow/%{deployment}`

#### 入力メッセージ
``` json
{
    "deployment": "production",
    "message": "example",
    "logTime": "2022-11-21T07:49:20Z"
}
```

#### 出力パス

```
/obs-test-container/dataflow/production/part-378be4d8-2c59-4014-aaeb-a9bc75af2653-0
```

### Prefixの例 - 時間

#### 条件

* バケット → `obs-test-container`
* Prefix → `/dataflow/year=%{+YYYY}/month=%{+MM}/day=%{+dd}/hour=%{+HH}`
* Prefix時間フィールド → `logTime`
* Prefix時間フィールドのタイプ → `ISO8601`
* Prefix時間帯 → `Asia/Seoul`

#### 入力メッセージ
``` json
{
    "deployment": "production",
    "message": "example",
    "logTime": "2022-11-21T07:49:20Z"
}
```

#### 出力パス

```
/obs-test-container/dataflow/year=2022/month=11/day=21/hour=16/part-378be4d8-2c59-4014-aaeb-a9bc75af2653-0
```

### Prefixの例 - 時間の適用に失敗した場合

#### 条件

* バケット → `obs-test-container`
* Prefix → `/dataflow/year=%{+YYYY}/month=%{+MM}/day=%{+dd}/hour=%{+HH}`
* Prefix時間フィールド → `logTime`
* Prefix時間フィールドのタイプ → `TIMESTAMP_SEC`
* Prefix時間帯 → `Asia/Seoul`
* Prefix時間適用fallback → `_failure`

#### 入力メッセージ
``` json
{
    "deployment": "production",
    "message": "example",
    "logTime": "2022-11-21T07:49:20Z"
}
```

#### 出力パス

```
/obs-test-container/_failure/part-378be4d8-2c59-4014-aaeb-a9bc75af2653-0
```

## Sink > (NHN Cloud) Data Lake Storage

### ノードの説明
* NHN CloudのData Lake Storageにデータをアップロードするノードです。
* 他の設定を行わずにデフォルト設定のみで作成すると、オブジェクトは次のパスフォーマットに合わせて出力されます。
    * `/{bucket_name}/year={yyyy}/month={MM}/day={dd}/hour={HH}/part-{uuid}-{file_counter}`   
* 提供コーデックはJSON、LINE、Parquetです。

### プロパティの説明
| プロパティ名 | デフォルト値 | データ型 | 説明 | 備考 |
|-----------------------|----------------------------------------------------|--------|--------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------|
| リージョン | - | enum | Data Lake Storage商品のリージョンを入力します。 | |
| バケット | - | string | バケット名を入力します。 | |
| シークレットキー | - | string | S3 API認証情報のシークレットキーを入力します。 | |
| アクセスキー | - | string | S3 API認証情報のアクセスキーを入力します。 | |
| Prefix | /year=%{+YYYY}/month=%{+MM}/day=%{+dd}/hour=%{+HH} | string | オブジェクトアップロード時に名前の前に付けるプレフィックスを入力します。<br/>フィールドまたは時間形式を入力できます。 | [使用可能な時間形式](https://joda-time.sourceforge.net/apidocs/org/joda/time/format/DateTimeFormat.html) |
| Prefix時間フィールド | - | string | Prefixに適用する時間フィールドを入力します。 | |
| Prefix時間フィールドタイプ | DATE_FILTER_RESULT | enum | Prefixに適用する時間フィールドのタイプを入力します。 | DATE_FILTER_RESULTタイプのみ可能(今後他のタイプをサポート予定) |
| Prefixタイムゾーン | UTC | string | Prefixに適用する時間フィールドのタイムゾーンを入力します。 | |
| Prefix時間適用fallback | _prefix_datetime_parse_failure | string | Prefix時間の適用に失敗した場合に代替するPrefixを入力します。 | |
| 基準時刻 | 1 | number | オブジェクトを分割する基準となる時間を設定します。 | |
| 基準オブジェクトサイズ | 5242880 | number | オブジェクトを分割する基準となるサイズ(単位：byte)を設定します。 | |
| 非アクティブ間隔 | 1 | number | データの入力がない状態が続く場合、オブジェクトを分割する基準時間を設定します。 | 設定された時間内にデータ入力がない場合、現在のオブジェクトがアップロードされ、その後新しく入力されるデータは新しいオブジェクトに書き込まれます。 |

### コーデック別の出力例
サポートコーデック
* [JSONコーデック](./codec-config-guide.md#json) - JSON形式データの解析
* [LINEコーデック](./codec-config-guide.md#line) - 行単位のメッセージ処理
* [Parquetコーデック](./codec-config-guide.md#parquet) - データをParquet形式で圧縮

### Prefixの例 - フィールド
#### 条件
* バケット → `dls-test-container`
* Prefix → `/dataflow/%{deployment}`

#### 入力メッセージ
``` json
{
    "deployment": "production",
    "message": "example",
    "logTime": "2022-11-21T07:49:20Z"
}
```

#### 出力パス
```
/dls-test-container/dataflow/production/part-378be4d8-2c59-4014-aaeb-a9bc75af2653-0
```

### Prefixの例 - 時間
#### 条件
* バケット → `dls-test-container`
* Prefix → `/dataflow/year=%{+YYYY}/month=%{+MM}/day=%{+dd}/hour=%{+HH}`
* Prefix時間フィールド → `logTime`
* Prefix時間フィールドタイプ → `ISO8601`
* Prefixタイムゾーン → `Asia/Seoul`

#### 入力メッセージ
``` json
{
    "deployment": "production",
    "message": "example",
    "logTime": "2022-11-21T07:49:20Z"
}
```

#### 出力パス
```
/dls-test-container/dataflow/year=2022/month=11/day=21/hour=16/part-378be4d8-2c59-4014-aaeb-a9bc75af2653-0
```

### Prefixの例 - 時間の適用に失敗した場合
#### 条件
* バケット → `dls-test-container`
* Prefix → `/dataflow/year=%{+YYYY}/month=%{+MM}/day=%{+dd}/hour=%{+HH}`
* Prefix時間フィールド → `logTime`
* Prefix時間フィールドタイプ → `ISO8601`
* Prefixタイムゾーン → `Asia/Seoul`
* Prefix時間適用fallback → `_failure`

#### 入力メッセージ
``` json
{
    "deployment": "production",
    "message": "example",
    "logTime": "2022-11-21T07:49:20Z"
}
```

#### 出力パス
```
/dls-test-container/_failure/part-378be4d8-2c59-4014-aaeb-a9bc75af2653-0
```

## Sink > (Amazon) S3

### ノードの説明

* Amazon S3にデータをアップロードするノードです。
* 提供するコーデックは、JSON、LINE、Parquetです。

### プロパティの説明
| プロパティ名 | デフォルト値 | データタイプ | 説明 | 備考 |
| --- | --- | --- | --- | --- |
| リージョン | - | enum | S3商品のリージョンを入力します。 | [s3 region](https://docs.aws.amazon.com/general/latest/gr/s3.html) |
| バケット | - | string | バケット名を入力します。 |  |
| アクセスキー | - | string | S3 API認証情報のアクセスキーを入力します。 |  |
| シークレットキー | - | string | S3 API認証情報のシークレットキーを入力します。 |  |
| Prefix | - | string | オブジェクトのアップロード時に名前の前に付けるプレフィックスを入力します。<br/>フィールドまたは時間形式を入力できます。 | [使用可能な時間形式](https://joda-time.sourceforge.net/apidocs/org/joda/time/format/DateTimeFormat.html) |
| Prefix時間フィールド | - | string | Prefixに適用する時間フィールドを入力します。 |  |
| Prefix時間フィールドのタイプ | DATE_FILTER_RESULT | enum | Prefixに適用する時間フィールドのタイプを入力します。 | DATE_FILTER_RESULTタイプのみ可能(今後他のタイプもサポート予定) |
| Prefix時間帯 | UTC | string | Prefixに適用する時間フィールドのタイムゾーンを入力します。 |  |
| Prefix時間適用fallback | _prefix_datetime_parse_failure | string | Prefixの時間の適用に失敗した場合に代替するPrefixを入力します。 |  |
| 基準時刻 | 1 | number | オブジェクトを分割する基準となる時間を設定します。 |  |
| 基準オブジェクトサイズ | 5242880 | number | オブジェクトを分割する基準となるサイズを設定します。 |  |
| パス方式のリクエスト | false | boolean | パス方式のリクエストを使用するかどうかを決定します。 |  |
| 非アクティブ間隔 | 1| number | データ取り込みがない状態が続く場合、オブジェクトを分割する基準時間を設定します。 | 設定した時間の間データの取り込みがないと現在のオブジェクトがアップロードされ、その後新しく取り込まれるデータは新しいオブジェクトに書き込まれます。 |

!!! danger "注意"
    * (Amazon) S3ノードを使用してNHN Cloud Object Storageに接続する場合は、**パス方式のリクエスト**を`true`に設定する必要があります。


### コーデック別の出力例

サポートするコーデック
* [JSONコーデック](./codec-config-guide.md#json) - JSON形式データのパース
* [LINEコーデック](./codec-config-guide.md#line) - 行単位でのメッセージ処理
* [Parquetコーデック](./codec-config-guide.md#parquet) - データをParquet形式で圧縮 

## Sink > (NHN Cloud) EasyQueue

### ノードの説明
NHN CloudのEasyQueueにデータを送信するノードです。

### プロパティの説明
| プロパティ名 | デフォルト値 | データ型 | 説明 | 備考 |
| --- | --- | --- | --- | --- |
| アプリキー | - | string | EasyQueueのアプリキーを入力します。 | |
| User Access Key ID | - | string | ユーザーアカウントのUser Access Key IDを入力します。 | |
| Secret Access Key | - | string | ユーザーアカウントのUser Secret Keyを入力します。 | |
| トピック | - | string | メッセージを送信するKafkaトピック名を入力します。 | |
| ブローカーサーバー一覧 | - | string | Kafkaブローカーサーバーを入力します。サーバーが複数ある場合はカンマ(`,`)で区切ります。 | [Kafka公式ドキュメント](https://kafka.apache.org/39/configuration/producer-configs/)の`bootstrap.servers`プロパティを参照<br/>例：10.100.1.1:9092,10.100.1.2:9092 |
| クライアントID | dataflow | string | Kafka Producerを識別するIDを入力します。 | [Kafka公式ドキュメント](https://kafka.apache.org/39/configuration/producer-configs/)の`client.id`プロパティを参照 |
| 圧縮タイプ | none | enum | 送信するデータを圧縮する方法を入力します。 | [Kafka公式ドキュメント](https://kafka.apache.org/39/configuration/topic-level-configs/)の`compression.type`プロパティを参照<br/>none、gzip、snappy、lz4、zstdから選択 |
| メッセージキー | - | string | メッセージキーとして使用するフィールドを入力します。 | |
| メタデータ更新サイクル | 300000 | number | パーティション、ブローカーサーバーの状態などを更新するサイクル(ms)を入力します。 | [Kafka公式ドキュメント](https://kafka.apache.org/39/configuration/producer-configs/)の`metadata.max.age.ms`プロパティを参照 |
| 最大リクエストサイズ | 1048576 | number | 送信リクエストあたりの最大サイズ(byte)を入力します。 | [Kafka公式ドキュメント](https://kafka.apache.org/39/configuration/producer-configs/)の`max.request.size`プロパティを参照 |
| サーバー再接続サイクル | 50 | number | ブローカーサーバーへの接続が失敗したときに再試行するサイクル(ms)を入力します。 | [Kafka公式ドキュメント](https://kafka.apache.org/39/configuration/producer-configs/)の`reconnect.backoff.ms`プロパティを参照 |
| バッチサイズ | 16384 | number | バッチリクエストで送信するサイズ(byte)を入力します。 | [Kafka公式ドキュメント](https://kafka.apache.org/39/configuration/producer-configs/)の`batch.size`プロパティを参照 |
| バッファメモリ | 33554432 | number | Kafkaの送信に使用するバッファのサイズ(byte)を入力します。 | [Kafka公式ドキュメント](https://kafka.apache.org/39/configuration/producer-configs/)の`buffer.memory`プロパティを参照 |
| 受信バッファサイズ | 32768 | number | データを読み取るために使用するTCP receiveバッファのサイズ(byte)を入力します。 | [Kafka公式ドキュメント](https://kafka.apache.org/39/configuration/producer-configs/)の`receive.buffer.bytes`プロパティを参照 |
| 送信遅延時間 | 0 | number | メッセージの送信を遅延する時間を入力します。遅延されたメッセージはバッチリクエストで一度に送信します。 | [Kafka公式ドキュメント](https://kafka.apache.org/39/configuration/producer-configs/)の`linger.ms`プロパティを参照 |
| サーバーリクエストタイムアウト | 30000 | number | 送信リクエストに対するタイムアウト(ms)を入力します。 | [Kafka公式ドキュメント](https://kafka.apache.org/39/configuration/producer-configs/)の`request.timeout.ms`プロパティを参照 |
| 送信バッファサイズ | 131072 | number | データを送信するために使用するTCP sendバッファのサイズ(byte)を入力します。 | [Kafka公式ドキュメント](https://kafka.apache.org/39/configuration/producer-configs/)の`send.buffer.bytes`プロパティを参照 |
| ackプロパティ | all | enum | ブローカーサーバーでメッセージを受信したか確認する設定を入力します。 | [Kafka公式ドキュメント](https://kafka.apache.org/39/configuration/producer-configs/)の`acks`プロパティを参照<br/>0 - メッセージの受信有無を確認しません。<br/>1 - トピックのleaderがfollowerによるデータのコピーを待たずに、メッセージを受信したという応答を返します。<br/>all - トピックのleaderがfollowerによるデータのコピーを待った後、メッセージを受信したという応答を返します。 |
| 再試行リクエストサイクル | 100 | number | 送信リクエストが失敗したときに再試行するサイクル(ms)を入力します。 | [Kafka公式ドキュメント](https://kafka.apache.org/39/configuration/producer-configs/)の`retry.backoff.ms`プロパティを参照 |
| 再試行回数 | 2147483647 | number | 送信リクエストが失敗したときに再試行する最大回数を入力します。 | [Kafka公式ドキュメント](https://kafka.apache.org/39/configuration/producer-configs/)の`retries`プロパティを参照<br/>設定値を超過して再試行する場合、データの消失が発生する可能性があります。 |
| 配信保証方式 | EXACTLY_ONCE | enum | メッセージの配信保証方式を選択します。 | AT_LEAST_ONCE：メッセージが少なくとも1回は配信されますが、障害発生時に重複が発生する可能性があります。重複処理をアプリケーションで直接管理できる場合や、重複が許容される場合に適しています。<br/><br/>EXACTLY_ONCE：メッセージが正確に1回だけ処理されます。重複が許容されない決済や精算などのコアトランザクションに適していますが、内部的にトランザクションを使用するため、処理量がやや低下する可能性があります。 |
| 追加設定 | - | hash | Kafka接続に使用する追加のProducer設定を入力します。 | [Kafka公式ドキュメント](https://kafka.apache.org/39/configuration/producer-configs/)を参照 |

### コーデック別の出力例
サポートコーデック
* [JSONコーデック](./codec-config-guide.md#json) - JSON形式データの解析
* [LINEコーデック](./codec-config-guide.md#line) - 行単位のメッセージ処理

## Sink > (Apache) Kafka

### ノードの説明

Kafkaにデータを送信するノードです。

### プロパティの説明

| プロパティ名 | デフォルト値 | データタイプ | 説明 | 備考 |
|-------------|--------------|--------|----------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| トピック | - | string | メッセージを送信するKafkaトピック名を入力します。 |  |
| ブローカーサーバーの一覧 |  | string | Kafkaブローカーサーバーを入力します。サーバーが複数ある場合はコンマ(`,`)で区切ります。 | [Kafka公式ドキュメント](https://kafka.apache.org/39/configuration/producer-configs/)の`bootstrap.servers`プロパティを参照<br/>例: 10.100.1.1:9092,10.100.1.2:9092 |
| クライアントID | dataflow | string | Kafka Producerを識別するIDを入力します。 | [Kafka公式ドキュメント](https://kafka.apache.org/39/configuration/producer-configs/)の`client.id`プロパティを参照 |
| 圧縮タイプ | none | enum | 送信するデータを圧縮する方法を入力します。 | [Kafka公式ドキュメント](https://kafka.apache.org/39/configuration/topic-level-configs/)の`compression.type`プロパティを参照<br/>none、gzip、snappy、lz4、zstdから選択 |
| メッセージキー | - | string | メッセージキーとして使用するフィールドを入力します。 |  |
| メタデータ更新周期 | 300000 | number | パーティション、ブローカーサーバーの状態などを更新する周期(ms)を入力します。 | [Kafka公式ドキュメント](https://kafka.apache.org/39/configuration/producer-configs/)の`metadata.max.age.ms`プロパティを参照 |
| 最大リクエストサイズ | 1048576 | number | 送信リクエストあたりの最大サイズ(byte)を入力します。 | [Kafka公式ドキュメント](https://kafka.apache.org/39/configuration/producer-configs/)の`max.request.size`プロパティを参照 |
| サーバー再接続周期 | 50 | number | ブローカーサーバーへの接続に失敗した場合に再試行する周期(ms)を入力します。 | [Kafka公式ドキュメント](https://kafka.apache.org/39/configuration/producer-configs/)の`reconnect.backoff.ms`プロパティを参照 |
| バッチサイズ | 16384 | number | バッチリクエストで送信するサイズ(byte)を入力します。 | [Kafka公式ドキュメント](https://kafka.apache.org/39/configuration/producer-configs/)の`batch.size`プロパティを参照 |
| バッファーメモリ | 33554432 | number | Kafkaへの送信に使用するバッファーのサイズ(byte)を入力します。 | [Kafka公式ドキュメント](https://kafka.apache.org/39/configuration/producer-configs/)の`buffer.memory`プロパティを参照 |
| 受信バッファーサイズ | 32768 | number | データの読み込みに使用するTCP receiveバッファーのサイズ(byte)を入力します。 | [Kafka公式ドキュメント](https://kafka.apache.org/39/configuration/producer-configs/)の`receive.buffer.bytes`プロパティを参照 |
| 送信遅延時間 | 0 | number | メッセージの送信を遅延させる時間を入力します。遅延されたメッセージはバッチリクエストで一度に送信されます。 | [Kafka公式ドキュメント](https://kafka.apache.org/39/configuration/producer-configs/)の`linger.ms`プロパティを参照 |
| サーバーリクエストのタイムアウト | 30000 | number | 送信リクエストに対するタイムアウト(ms)を入力します。 | [Kafka公式ドキュメント](https://kafka.apache.org/39/configuration/producer-configs/)の`request.timeout.ms`プロパティを参照 |
| 送信バッファーサイズ | 131072 | number | データの送信に使用するTCP sendバッファーのサイズ(byte)を入力します。 | [Kafka公式ドキュメント](https://kafka.apache.org/39/configuration/producer-configs/)の`send.buffer.bytes`プロパティを参照 |
| ackプロパティ | all | enum | ブローカーサーバーでメッセージを受信したか確認する設定を入力します。 | [Kafka公式ドキュメント](https://kafka.apache.org/39/configuration/producer-configs/)の`acks`プロパティを参照<br/>0 - メッセージの受信有無を確認しません。<br/>1 - トピックのleaderがfollowerによるデータのコピーを待たずにメッセージを受信したと応答します。<br/>all - トピックのleaderがfollowerによるデータのコピーを待ってからメッセージを受信したと応答します。 |
| 再試行リクエスト周期 | 100 | number | 送信リクエストが失敗した場合に再試行する周期(ms)を入力します。 | [Kafka公式ドキュメント](https://kafka.apache.org/39/configuration/producer-configs/)の`retry.backoff.ms`プロパティを参照 |
| 再試行回数 | 2147483647 | number | 送信リクエストが失敗した場合に再試行する最大回数を入力します。 | [Kafka公式ドキュメント](https://kafka.apache.org/39/configuration/producer-configs/)の`retries`プロパティを参照<br/>設定値を超えて再試行した場合、データの消失が発生する可能性があります。 |
| 配信保証方式 | EXACTLY_ONCE | enum | メッセージの配信保証方式を選択します。 | AT_LEAST_ONCE：メッセージが少なくとも1回は配信されますが、障害発生時に重複が発生する可能性があります。重複処理をアプリケーションで直接管理できる場合や、重複が許容される場合に適しています。<br/>EXACTLY_ONCE：メッセージが正確に1回だけ処理されます。重複が許容されない決済や精算などのコアトランザクションに適していますが、内部的にトランザクションを使用するため、処理量がやや低下する可能性があります。 |
| 追加設定 | - | hash | Kafka接続に使用する追加のProducer設定を入力します。 | [Kafka公式ドキュメント](https://kafka.apache.org/39/configuration/producer-configs/)を参照 |

### コーデック別の出力例

サポートするコーデック
* [JSONコーデック](./codec-config-guide.md#json) - JSON形式データ出力
* [LINEコーデック](./codec-config-guide.md#line) - 行単位メッセージ出力

## Sink > Stdout

### ノードの説明

* 標準出力にメッセージを出力するノードです。
* Source、Filterノードで処理されたデータを確認する際に便利に使用できます。

### コーデック別の出力例

サポートするコーデック
* [JSONコーデック](./codec-config-guide.md#json) - JSON形式データ出力
* [LINEコーデック](./codec-config-guide.md#line) - 行単位メッセージ出力

## Branch

取り込まれたデータの値に応じてフローの分岐を定義するノードタイプです。

## Branch > IF

### ノードの説明

条件文でメッセージをフィルタリングするノードです。

### プロパティの説明

| プロパティ名 | デフォルト値 | データタイプ | 説明 | 備考 |
| --- | --- | --- | --- | --- |
| 条件文 | - | string | メッセージをフィルタリングする条件を入力します。 | 以下の例をご参照ください。 |

#### 使用可能な演算子
* 比較: ==、!=、<、>、<=、>=
* 正規表現: =~ (右辺で指定されたパターンで左辺の文字列を検査)
* 包含: =~、!~、.contains()
* 論理演算子: &&、||、not
* 否定演算子: !、not

### フィルタリングの例 - first depth field reference

#### 条件
条件文 → `logLevel == "ERROR"`

#### 通過メッセージ

``` json
{
    "logLevel": "ERROR"
}
```

#### 除外メッセージ

``` json
{
    "logLevel": "INFO"
}
```

### フィルタリングの例 - second depth field reference

#### 条件

条件文 → `response.status == 200` または `response["status"] == 200`

#### 通過メッセージ

``` json
{
    "response": {
        "status": 200
    }
}
```

#### 除外メッセージ

``` json
{
    "response": {
        "status": 404
    }
}
```

## Branch > Dataset Split

### ノードの説明

* イベントを設定された比率に基づいて複数の分岐に分割するノードです。
* 機械学習データセットの分割(例: 学習/テスト/検証)などの目的に活用できます。
* 各分岐には1つの下位ノードを接続できます。

### プロパティの説明

| プロパティ名 | デフォルト値 | データタイプ | 説明 | 備考 |
| --- | --- | --- | --- | --- |
| シード | - | number | 乱数生成時に使用するシードを入力します。シードが同じで入力メッセージが同じであれば、結果は同じになります。 |  |
| 分割設定 | - | hash | 分岐名と比率をJSON形式で入力します。全ての比率の合計は`1.0`である必要があります。 | 例: `{"train": 0.6, "test": 0.3, "sampling": 0.1}` |

### イベント分割の例

#### 条件

* シード → `42`
* 分割設定 → `{"train": 0.6, "test": 0.3, "sampling": 0.1}`

#### 動作

入力されたイベントが設定された比率に基づいて各分岐に伝達されます。

* `train` 分岐に接続された下位ノード: 全イベントの約60%が伝達されます。
* `test` 分岐に接続された下位ノード: 全イベントの約30%が伝達されます。
* `sampling` 分岐に接続された下位ノード: 全イベントの約10%が伝達されます。
