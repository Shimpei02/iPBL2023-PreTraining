# MQTT プロトコルの説明

# MQTT は何ですか？

MQTT は、メッセージキューイングテレメトリトランスポートの略であり、IoT（モノのインターネット）環境のデバイス間での簡単で効率的な通信のために作られたメッセージングプロトコルです。

MQTT プロトコルは、デバイスが特定のトピックにメッセージを公開し、他のデバイスがそのトピックに購読してメッセージを受信することにより、効率的なデータ伝送を実現するためにパブリッシュ-サブスクライブモデルを使用します。これにより、ネットワーク帯域幅の使用量が削減されます。

MQTT の主な特徴には、以下が含まれます：

1. 軽量性: MQTT は軽量なプロトコルとして設計されており、処理能力や帯域幅が制約されたデバイスに適しています。
2. 非同期通信：MQTT はデバイスがメッセージを非同期で送受信できるため、信頼性の低いネットワーク環境でも効率的な通信を実現します。
3. MQTT は、メッセージの信頼性によって配信される 3 つの品質サービス（QoS）レベルを持っています。これらのレベルは、QoS 0、QoS 1、および QoS 2 です。
   - QoS 0 (最大 1 回)
   - QoS 1 (少なくとも 1 回)
   - QoS 2 (1 回のみ)
4. スケーラビリティ：MQTT は非常にスケーラブルであり、多数のデバイスとサブスクライバーをサポートすることができるため、IoT の展開に最適です。
5. 効率的なネットワーク利用：MQTT はコンパクトなバイナリメッセージ形式を使用しており、ネットワーク帯域幅の使用を最小限に抑えるのに役立ちます。

MQTT は、シンプルさ、効率性、および様々なデバイスやプラットフォームとの連携能力により、IoT 業界で人気を得ています。効率的かつ信頼性の高い通信が不可欠なホームオートメーション、産業監視、およびテレマティクスなどのアプリケーションに広く利用されています。

# MQTT はどのように動作しますか？

MQTT の動作原理を理解するには、以下の基本的な概念をマスターする必要があります。

## MQTT クライアント

- どんなアプリやデバイスでも、MQTT クライアントライブラリを実行していれば、MQTT クライアントとなります。たとえば、MQTT を使用するチャットメッセージングアプリはクライアントですし、データを報告するために MQTT を使用するセンサーもクライアントです。また、MQTT のテストツールもクライアントとなります。

## MQTTブローカー

- ブローカーは接続、切断、購読、解約のリクエストとメッセージのルーティングを処理します。

## パブリッシュ-サブスクライブパターン

- 名前の通り、MQTT メッセージの送受信のためのパターンです。パブリッシャーとサブスクライバーは、MQTT ブローカーがすべてのメッセージのルーティングと配信を担当するため、直接接続を確立する必要はありません。
- 以下の図は、MQTT のパブリッシュ/サブスクライブプロセスを示しています。温度センサーはクライアントとして MQTT サーバーに接続し、温度データをトピック（例：`temp`）にパブリッシュします。そして、サーバーはメッセージを受信し、`temp`トピックにサブスクライブしているすべてのクライアントに転送します。

![pub-sub.png](../assets/pub-sub.png)

## トピック

MQTT プロトコルは、メッセージをルーティングするためにトピックを使用し、これらのトピックはスラッシュ（`/`）を使用して階層的に整理されます。例えば、

- `chat/room/1`
- `sensor/10/temperature`
- `sensor/+/temperature`

MQTT のトピックは、以下のワイルドカードをサポートしています：`+` と `#`

- `+` は、ワイルドカードの 1 レベルを示します。たとえば、`a/+` は `a/x` または `a/y` と一致します。
- `#` は、`a/x` および `a/b/c/d` に一致する `a/#` など、複数のワイルドカード レベルを示します。

MQTT トピックの詳細については、Case によるブログ「[Understanding MQTT Topics & Wildcards](https://www.emqx.com/en/blog/advanced-features-of-mqtt-topics)」をご確認ください。

## サービスの品質 (QoS)

MQTT は 3 種類のサービス品質を提供し、さまざまなネットワーク環境でのメッセージングの信頼性を保証します。

- QoS 0: メッセージは最大 1 回届けられます。クライアントが現在利用できない場合、このメッセージは失われます。
- QoS 1: メッセージは少なくとも 1 回配信されます。重複したメッセージが含まれている可能性があります。
- QoS 2: メッセージは一度だけ配信されます。

MQTT QoS の詳細については、ブログ「[MQTT QoS（Quality of Service）入門](https://www.emqx.com/en/blog/introduction-to-mqtt-qos)」をご確認ください。

# MQTT ワークフロー

MQTT の基本的なコンポーネントを理解したので、一般的なワークフローがどのように機能するかを見てみましょう。

1. クライアントは、セキュアな通信のためにオプションの TLS/SSL 暗号化を使用して、TCP/IP 経由でブローカーに接続を開始します。クライアントは認証資格情報を提供し、クリーンまたは永続的なセッションを指定します。
2. クライアントは、特定のトピックにメッセージを投稿するか、トピックに対して購読してメッセージを受け取ります。メッセージを発行するクライアントは、ブローカーにメッセージを送信しますが、メッセージを受け取ることに興味を持つクライアントは、特定のトピックでメッセージを受け取る意思を示します。
3. ブローカーはパブリッシュされたメッセージを受信し、関連するトピックにサブスクライブしているすべてのクライアントにそれらを転送します。指定されたサービス品質 (QoS) レベルに従って信頼性の高いメッセージ配信を保証し、セッション タイプに基づいて切断されたクライアントのメッセージ ストレージを管理します。
