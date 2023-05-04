[![FIWARE Banner](https://fiware.github.io/tutorials.Media-Streams/img/fiware.png)](https://www.fiware.org/developers)

[![FIWARE Media Streams](https://nexus.lab.fiware.org/repository/raw/public/badges/chapters/media-streams.svg)](https://github.com/FIWARE/catalogue/blob/master/processing/README.md)
[![License: MIT](https://img.shields.io/github/license/fiware/tutorials.Media-Streams.svg)](https://opensource.org/licenses/MIT)
[![Kurento 6.7.1](https://img.shields.io/badge/Kurento-6.7.1-4f3495.svg)](http://doc-kurento.readthedocs.io/)
[![Support badge](https://img.shields.io/badge/tag-fiware-orange.svg?logo=stackoverflow)](https://stackoverflow.com/questions/tagged/fiware)
<br/> [![Documentation](https://img.shields.io/readthedocs/fiware-tutorials.svg)](https://fiware-tutorials.rtfd.io)

<!-- prettier-ignore -->

これは、[WebRTC](https://webrtc.org/) を介したビデオ・ストリームの分析と拡張に使
用されるメディア・サーバの Generic Enabler である
、[FIWARE Kurento](http://kurento.readthedocs.io/) 入門チュートリアルです。この
チュートリアルでは、ストリーム指向のシステムのアーキテクチャについて説明し
、Node.js で書かれたコードについて議論することで、ビデオ・ストリームの背後にある
主要な概念を示します。Java およびクライアントサイドの JavaScript で書かれた別の
コード例も利用できます。

このチュートリアルでは、[Docker](https://www.docker.com) コンテナから直接実行で
きる一連の演習を紹介していますが、HTTP コールは必要ありません。

[![Open in Gitpod](https://gitpod.io/button/open-in-gitpod.svg)](https://gitpod.io/#https://github.com/FIWARE/tutorials.Media-Streams/tree/NGSI-v2)

## 内容

<details>
<summary>詳細 <b>(クリックして拡大)</b></summary>

-   [メディア・ストリームとは何ですか？](#what-are-media-streams)
    -   [このチュートリアルの指導目標](#the-teaching-goal-of-this-tutorial)
-   [前提条件](#prerequisites)
    -   [Docker](#docker)
    -   [Cygwin](#cygwin)
-   [起動](#start-up)
-   [アーキテクチャ](#architecture)
    -   [Kurento 設定](#kurento-configuration)
    -   [アプリケーション・サーバの設定](#application-server-configuration)
-   [メディア・サーバへの接続](#connecting-to-a-media-sever)
    -   [Hello World - スタート・アップ](#hello-world---start-up)
        -   [サービスの健全性](#service-health)
        -   [Hello World - サンプルの実行](#hello-world---running-the-example)
    -   [Hello World - コードの分析](#hello-world---analyzing-the-code)
        -   [バック・エンド - WebSocket 接続](#back-end---websocket-connection)
        -   [バック・エンド - Kurento への接続](#back-end---connecting-to-kurento)
        -   [バック・エンド - メディア・パイプラインの作成](#back-end---creating-a-media-pipeline)
        -   [フロント・エンド - レンダリングされたページの JavaScript](#front-end---javascript-on-the-rendered-page)
-   [メディア・ストリームの変更](#altering-media-streams)
    -   [マジック・ミラー - 起動](#magic-mirror---start-up)
        -   [マジック・ミラー - 例を実行](#magic-mirror---running-the-example)
    -   [マジック・ミラー - コードの分析](#magic-mirror---analyzing-the-code)
        -   [バック・エンド - ビルトイン・フィルタをメディア・パイプラインに追加](#back-end---adding-a-built-in-filter-to-a-media-pipeline)
-   [コンテキスト・イベントの発生](#raising-context-events)
    -   [プレート検出器 - 起動](#plate-detector----start-up)
        -   [プレート検出器 - サンプルの実行](#plate-detector---running-the-example)
    -   [プレート検出器 - コードの分析](#plate-detector---analyzing-the-code)
        -   [バック・エンド - カスタム・フィルタをメディア・パイプラインに追加](#back-end---adding-a-custom-filter-to-a-media-pipeline)
        -   [フロント・エンド - レンダリングされたページの JavaScript](#front-end---javascript-on-the-rendered-page-1)

</details>

<a name="what-are-media-streams"></a>

# メディア・ストリームとは何ですか？

> "Thank You Mario, But Our Princess is in Another Castle."
>
> — Toad (Super Mario Bros.)

[WebRTC](https://webrtc.org/) は、ブラウザやモバイル・アプリケーション間で直接ピ
ア・ツー・ピアのリアルタイム通信 (RTC) を可能にする一連のプロトコルです。これに
より、ユーザは別の遠隔地の人物に直接ビデオ通話を行うことができます。しかし、直接
1 対 1 通信を使用して作成できるアプリケーションのタイプは限られており、2 つのク
ライアント間にインテリジェントなミドルウェア・アプリケーションを導入することによ
って WebRTC の範囲を拡張できます。

FIWARE の **Kurento** Generic Enabler は WebRTC Media Server です。各クライアン
トはサーバに直接接続し、サーバはデータ・ストリームをインターセプトし、通信を別の
クライアントに渡します。このモデルは、グループ・コミュニケーションや放送などの追
加機能を可能にするだけでなく、受信したメディア・ストリームを処理して解釈すること
が可能であるため、オブジェクト検出を可能にし、したがってトランスコード、録音また
はミキシングと同様にコンテキスト・イベントを発生させることができます。

<a name="the-teaching-goal-of-this-tutorial"></a>

## このチュートリアルの指導目標

このチュートリアルの目的は、**Kurento Media Server** のインストールと使用方法に
関する簡単なスタート・ガイドを提供することです。この目的のために、単純な Node.js
Express アプリケーションについて説明します。ここで重要な点は、**Kurento** を
FIWARE システム内の Generic Enabler として統合し、コンテキストを変更する方法にあ
ります。

ここでの意図は、Node.js を使用してメディア・ストリームを操作する方法をユーザに教
えることではなく、Java などの選択肢も同様に選択できます。サンプルのプログラミン
グ言語を使用して、メディア・ストリームを分析して変更し、事象を起こしたり
、**"powered by FIWARE"** プロダクトのコンテキストを変更したりする方法を示すだけ
です。

デモ用のすべてのコードは
、[kurento-examples](https://github.com/FIWARE/tutorials.Media-Streams/tree/master/kurento-examples)
ディレクトリ内の `nodejs` フォルダ内にあります。代わりに
、`client-side-javascript`と `java` の例もあります。明らかに、プログラミング言語
の選択は、あなた自身のビジネス・ニーズに依存します。以下のコードを読んで、この点
を念頭に置いて Node.js を独自のプログラミング言語に置き換えてください。

追加の非コンテキスト関連の **Kurento** の例がありますが、このチュートリアルの範
囲を超えています。詳細は
、[Kurento チュートリアルの公式ドキュメント](https://doc-kurento.readthedocs.io/en/stable/user/tutorials.html)を
参照してください。**Kurento Media Server** は、スタンド・アロン製品であり、また
、一般的なメディア・サーバとして FIWARE エコシステムの外で使用することができます
。

<a name="prerequisites"></a>

# 前提条件

<a name="docker"></a>

## Docker

物事を単純にするために、両方のコンポーネントが [Docker](https://www.docker.com)
を使用して実行されます。**Docker** は、さまざまコンポーネントをそれぞれの環境に
分離することを可能にするコンテナ・テクノロジです。

-   Docker Windows にインストールするには
    、[こちら](https://docs.docker.com/docker-for-windows/)の手順に従ってくださ
    い
-   Docker Mac にインストールするには
    、[こちら](https://docs.docker.com/docker-for-mac/)の手順に従ってください
-   Docker Linux にインストールするには
    、[こちら](https://docs.docker.com/install/)の手順に従ってください

**Docker Compose** は、マルチコンテナ Docker アプリケーションを定義して実行する
ためのツールです
。[YAML file](https://raw.githubusercontent.com/Fiware/tutorials.Entity-Relationships/master/docker-compose.yml)
ファイルは、アプリケーションのために必要なサービスを構成するために使用します。つ
まり、すべてのコンテナ・サービスは 1 つのコマンドで呼び出すことができます
。Docker Compose は、デフォルトで Docker for Windows と Docker for Mac の一部と
してインストールされますが、Linux ユーザ
は[ここ](https://docs.docker.com/compose/install/)に記載されている手順に従う必要
があります。

<a name="cygwin"></a>

## Cygwin

シンプルな bash スクリプトを使用してサービスを開始します。Windows ユーザは
[cygwin](http://www.cygwin.com/) をダウンロードして、Windows 上の Linux ディスト
リビューションと同様のコマンドライン機能を提供する必要があります。

<a name="start-up"></a>

# 起動

インストールを開始するには、次の手順を実行します :

```console
git clone https://github.com/FIWARE/tutorials.Media-Streams.git
cd tutorials.Media-Streams
git checkout NGSI-v2
git submodule update --init --recursive

./services create
```

> **注** Docker イメージの最初の作成には最大 3 分かかります

その後、リポジトリ内で提供される
[services](https://github.com/FIWARE/tutorials.Media-Streams/blob/NGSI-v2/services)
Bash スクリプトを実行することによって、コマンドラインからすべてのサービスを初期
化することができます :

```console
./services <command>
```

ここで、`<command>`は、私たちがアクティベートしたいエクササイズに応じてかわりま
す。

> :information_source: **注:** クリーンアップをやり直したい場合は、次のコマンド
> を使用して再起動することができます :
>
> ```console
> ./services stop
> ```

<a name="architecture"></a>

# アーキテクチャ

このアプリケーションは、1 つの FIWARE コンポーネントの
[Kurento Media Server](http://kurento.readthedocs.io/) のみを使用します。アプリ
ケーションが _"Powered by FIWARE"_ の資格を得るには、メディア・サーバだけでは不
十分です。

全体的なアーキテクチャは、次の要素で構成されます :

-   1 つの **FIWARE Generic Enabler** :

    -   FIWARE [Kurento](http://kurento.readthedocs.io/) は
        、[WebRTC](https://webrtc.org/) トラフィックをインターセプトするメディア
        ・サーバとして機能し、必要に応じて追加の処理が追加されます

-   1 つの**アプリケーション・サーバ** (例) :

    -   Web ページを表示し、ユーザがカメラの電源を入れて操作できるようにします
    -   WebRTC メディア・ストリームを送信し、結果 (受信した処理済みビデオまたは
        検出されたイベント) を表示します

要素間のすべての対話は HTTP リクエストによって開始されるため、エンティティはコン
テナ化され、公開されたポートから実行されます。

![](https://fiware.github.io/tutorials.Media-Streams/img/architecture.png)

チュートリアルの各セクションの具体的なアーキテクチャについては、以下で説明します
。

<a name="kurento-configuration"></a>

## Kurento 設定

```yaml
kurento:
    image: quay.io/fiware/stream-oriented-kurento
    hostname: kurento
    container_name: fiware-kurento
    expose:
        - "8888"
    ports:
        - 8888:8888
    networks:
        - default
```

`kurento` コンテナは、単一ポートで待機しています :

-   ポート `8888` は Websocket 通信のために公開されており、接続を確認できるよう
    になっています

<a name="application-server-configuration"></a>

## アプリケーション・サーバの設定

```yaml
kurento-examples:
    image: quay.io/fiware/kurento-examples
    container_name: examples-kurento
    depends_on:
        - kurento
    build:
        context: ../kurento-examples
        dockerfile: Dockerfile
    ports:
        - 8443:8443
    networks:
        - default
    environment:
        - "MEDIA_SERVER_HOST=kurento"
        - "MEDIA_SERVER_PORT=8888"
        - "APP_SERVER_HOST=kurento-examples"
        - "APP_SERVER_PORT=8443"
        - "TUTORIAL_NAME=${TUTORIAL_NAME}"
```

`kurento-examples` コンテナは、単一のポートでリッスンしている、Web アプリケーシ
ョン・サーバです :

-   ポート `8443` が HTTPS トラフィック用に公開されているため、Web ページを表示
    できます

`kurento-examples` コンテナは、以下に示すように、環境変数によってドライブされま
す。

| キー              | 値                 | 説明                                                                                                       |
| ----------------- | ------------------ | ---------------------------------------------------------------------------------------------------------- |
| MEDIA_SERVER_HOST | `kurento`          | メディア・ストリームを受信するメディア・サーバのホスト名                                                   |
| MEDIA_SERVER_PORT | `8888`             | websocket トラフィックを使用する Media Server で使用されるポート                                           |
| APP_SERVER_HOST   | `kurento-examples` | Web ページを表示するため、およびイベント・コールバックを受信するための、アプリケーション・サーバのホスト名 |
| APP_SERVER_PORT   | `8443`             | アプリケーション・サーバで使用されるポート                                                                 |
| TUTORIAL_NAME     | none               | 実行するサンプルの名前                                                                                     |

<a name="connecting-to-a-media-sever"></a>

# メディア・サーバへの接続

適切なコンテキスト関連の例を説明する前に、最初に最小セットアップ "Hello World"
の例を調べて、**Kurento Media server** に接続してビデオ・ストリームを送受信でき
ることを確認します。これは WebRTC ループ・バックを実装する非常にシンプルな
[WebRTC](https://webrtc.org/) アプリケーションです。メディア・ストリームは
web-cam から生成されます。これはブラウザに表示されますが、**Kurento Media
server** にも送信され、変更されずにクライアント・アプリケーションにリダイレクト
されます。

![](https://fiware.github.io/tutorials.Media-Streams/img/hello-world.png)

結果として、2 つの同一のビデオ要素が同じ画面上に表示されます。それらの間には最小
限の遅延があります。これは有用なアプリケーションではありませんが、メディア・サー
バにアクセスしてビデオをストリーミングできることを実証します。

<a name="hello-world---start-up"></a>

## Hello World - スタート・アップ

**Kurento** を簡単に統合してシステムを起動するには、次のコマンドを実行します :

```console
./services hello-world
```

<a name="service-health"></a>

### サービスの健全性

**Kurento Media Server** が稼働しているかどうかは、公開されているポート `8888`
に HTTP リクエストを行うことで確認できます

```console
curl -iX GET \
  http://localhost:8888
```

期待される応答は次のとおりです :

```
HTTP/1.1 426 Upgrade Required
Server: WebSocket++/0.7.0
```

レスポンス・コード `426` は、**Kurento Media Server** が指定されたポートで応答し
ているが、HTTP リクエストに正常に応答しないことを示します。メディア・サーバは
WebSocket トラフィックにのみ正常に応答します。

> **`Failed to connect to localhost port 8888: Connection refused` 応答が返って
> きた場合の対応は?**
>
> `Connection refused` の応答を受け取った場合、メディア・サーバをこのチュートリ
> アルで必要な場所で見つけることができません。各 cUrl コマンドの URL とポートを
> 正しい IP アドレスで置き換える必要があります。すべての cUrl コマンドのチュート
> リアルでは、`localhost:8888` でメディア・サーバが使用可能であると想定していま
> す。
>
> 以下の対応策を試してください :
>
> -   Docker コンテナが動作していることを確認するには、次のようにしてください :
>
> ```
> docker ps
> ```
>
> 実行中の 2 つのコンテナが表示されます。メディア・サーバが実行されていない場合
> は、必要に応じてコンテナを再起動できます。このコマンドは、オープンしているポー
> ト情報も表示します。

<a name="hello-world---running-the-example"></a>

### Hello World - サンプルの実行

この例を実行するには、`https://localhost:8443`で WebRTC 互換ブラウザ を開き、ペ
ージをオープンするために、HTTPS トラフィックを受け入れます。アプリケーションは
、2 つの HTML5 `<video>` タグを含む、1 つの HTML Web ページで構成されています。1
つはローカル Web-cam でキャプチャされたローカル・ストリームと、もう 1 つはメディ
ア・サーバからクライアントに送信されたリモート・ストリームです。"Start" ボタンを
クリックすると、両方の `<video>` 要素に同じビデオが表示されます。

![](https://fiware.github.io/tutorials.Media-Streams/img/hello-world-screenshot.png)

[Hello world の例](https://www.youtube.com/watch?v=vGEnkSOp_xc)のデモを見るには
リンクをクリックしてください。

メディア・サーバを停止すると、リモート・ストリームがリダイレクトされたことを確認
できます :

```console
docker stop fiware-kurento
```

右の画像がフリーズします。

Kurento メディア・サーバ を再起動するには、次のコマンドを実行します :

```console
docker start fiware-kurento
```

<a name="hello-world---analyzing-the-code"></a>

## Hello World - コードの分析

議論中のコードは、Git リポジトリ内の `kurento-hello-world` ディレクトリ内にあり
ます。このデモのメインスクリプトは、`server.js` が呼び出されます。簡単にするため
、すべての URL はハード・コードされており、読みやすいように、エラー処理が議論中
のコード・スニペットから削除されています。

<a name="back-end---websocket-connection"></a>

### バック・エンド - WebSocket 接続

フロント・エンドでレンダリングされた Web ページとバック・エンド・アプリケーショ
ン・サーバ間の動的通信は
、[WebSockets](https://www.html5rocks.com/en/tutorials/websockets/basics/) を使
用して行われます。サーバでの接続を処理するコードを以下に示します :

```javascript
var ws = require("ws");

var wss = new ws.Server({
    server: server,
    path: "/helloworld"
});

wss.on("connection", function (ws) {
    var sessionId = null;
    var request = ws.upgradeReq;
    var response = {
        writeHead: {}
    };

    sessionHandler(request, response, function (err) {
        sessionId = request.session.id;
    });

    ws.on("error", (error) => {
        stop(sessionId);
    });

    ws.on("close", () => {
        stop(sessionId);
    });

    ws.on("message", (_message) => {
        var message = JSON.parse(_message);

        switch (message.id) {
            case "start":
                sessionId = request.session.id;
                start(sessionId, ws, message.sdpOffer, function (error, sdpAnswer) {
                    ws.send(
                        JSON.stringify({
                            id: "startResponse",
                            sdpAnswer: sdpAnswer
                        })
                    );
                });
                break;

            case "stop":
                stop(sessionId);
                break;

            case "onIceCandidate":
                onIceCandidate(sessionId, message.candidate);
                break;
        }
    });
});
```

最初の接続である `wss.on('connection', ...)` の後、処理すべきいくつかのメッセー
ジ・タイプがあります。処理すべき主要な 2 つのメッセージ・タイプは、**Kuerento
Media Server** に接続する `start` と、`onIceCandidate` です。`start()` 関数と
`onIceCandidate()` 関数は下記を参照してください。接続の停止、終了、エラー処理は
標準的な方法で処理されるため、ここでは説明しません。

<a name="back-end---connecting-to-kurento"></a>

### バック・エンド - Kurento への接続

**Kurento** は、明確に定義されてた
[WebSocket API](https://kurento.readthedocs.io/en/stable/doc/open_spec.html) を提供します。 WebSocket
接続を確立するには、クライアントは WebSocket ハンドシェイク要求を `/kurento` エ
ンドポイントに送信する必要があり、メディア・サーバは WebSocket ハンドシェイク応
答を返します。

プログラム的には、アプリケーション・サーバで `KurentoClient` インスタンスを作成
する必要があります。これは、指定された環境変数値を使用して作成されます。たとえば
、Kurento が `kurento-server` でホストされ、ポート `8888` でリッスンしている場合
です。

```javascript
const kurento = require("kurento-client");

function getKurentoClient(callback) {
    if (kurentoClient !== null) {
        return callback(null, kurentoClient);
    }

    kurento("ws://kurento-server:8888/kurento", (error, _kurentoClient) => {
        kurentoClient = _kurentoClient;
        callback(null, kurentoClient);
    });
}
```

`KurentoClient` は、メディア要素とメディア・パイプラインを操作するための単純化さ
れたインターフェイスを提供します。Node.js `npm` ライブラリと Java `jar` ファイル
が利用可能です。

<a name="back-end---creating-a-media-pipeline"></a>

### バック・エンド - メディア・パイプラインの作成

入力ビデオを操作するには、メディア・パイプラインを作成する必要があります。これは
、ある要素によって生成されたソースが別の要素の入力で使用される処理ウィジェットで
す。パイプライン要素は、一緒に連結することができ、従って、メディア・ストリーム上
の一連の操作を表します。

Hello World の例では、自分自身に戻って (すなわちループバックで) 接続する単一の
`WebRtcEndpoint` が必要になります。

これらの関数は `start()` 関数で呼び出され、`start` メッセージを受け取ったときに
起動されます

```javascript
function start(sessionId, ws, sdpOffer, callback) {
    getKurentoClient((error, kurentoClient) => {
        // Create a Media Pipeline
        kurentoClient.create("MediaPipeline", (error, pipeline) => {
            createMediaElements(pipeline, ws, (error, webRtcEndpoint) => {
                if (candidatesQueue[sessionId]) {
                    while (candidatesQueue[sessionId].length) {
                        const candidate = candidatesQueue[sessionId].shift();
                        webRtcEndpoint.addIceCandidate(candidate);
                    }
                }
                // Connect it back on itself (i.e. in loopback)
                connectMediaElements(webRtcEndpoint, (error) => {
                    webRtcEndpoint.on("OnIceCandidate", function (event) {
                        const candidate = kurento.getComplexType("IceCandidate")(event.candidate);
                        ws.send(
                            JSON.stringify({
                                id: "iceCandidate",
                                candidate: candidate
                            })
                        );
                    });

                    webRtcEndpoint.processOffer(sdpOffer, (error, sdpAnswer) => {
                        sessions[sessionId] = {
                            pipeline: pipeline,
                            webRtcEndpoint: webRtcEndpoint
                        };
                        return callback(null, sdpAnswer);
                    });

                    webRtcEndpoint.gatherCandidates((error) => {
                        if (error) {
                            return callback(error);
                        }
                    });
                });
            });
        });
    });
}
```

ここで、`createMediaElements()` と `connectMediaElements()` は、次のコールバック
関数です :

```javascript
function createMediaElements(pipeline, ws, callback) {
    pipeline.create("WebRtcEndpoint", (error, webRtcEndpoint) => {
        if (error) {
            return callback(error);
        }
        return callback(null, webRtcEndpoint);
    });
}
```

```javascript
function connectMediaElements(webRtcEndpoint, callback) {
    webRtcEndpoint.connect(webRtcEndpoint, (error) => {
        if (error) {
            return callback(error);
        }
        return callback(null);
    });
}
```

メディア要素間の接続は、WebRTC ピア間で
[ICE](https://tools.ietf.org/html/rfc5245.html) 候補を交換することによってネゴシ
エートされます。これは、次の定型文を使用して候補を使用および保存できます :

```javascript
let candidatesQueue = {};

[...]

function onIceCandidate(sessionId, _candidate) {
    let candidate = kurento.getComplexType('IceCandidate')(_candidate);

    if (sessions[sessionId]) {
        let webRtcEndpoint = sessions[sessionId].webRtcEndpoint;
        webRtcEndpoint.addIceCandidate(candidate);
    }
    else {
        if (!candidatesQueue[sessionId]) {
            candidatesQueue[sessionId] = [];
        }
        candidatesQueue[sessionId].push(candidate);
    }
}
```

<a name="front-end---javascript-on-the-rendered-page"></a>

### フロント・エンド - レンダリングされたページの JavaScript

レンダリングされた Web ページ上でのビデオ・ストリームの接続とレンダリングは 、ク
ライアント側の JavaScript ライブラリと連携した JavaScript WebSocket API を使用し
て実現されます。kurento-utils.js は、アプリケーション・サーバと WebRTC とのやり
とりを簡略化するために使用されます。完全な JavaScript は `static/js/index.js` で
見つけることができます。

```javascript
var ws = new WebSocket("wss://" + location.host + "/helloworld");
var videoInput = document.getElementById("videoInput");
var videoOutput = document.getElementById("videoOutput");
var webRtcPeer = null;
```

```javascript
function start() {
    var options = {
        localVideo: videoInput,
        remoteVideo: videoOutput,
        onicecandidate: onIceCandidate
    };

    webRtcPeer = kurentoUtils.WebRtcPeer.WebRtcPeerSendrecv(options, function (error) {
        if (error) return onError(error);
        this.generateOffer(onOffer);
    });
}

function onIceCandidate(candidate) {
    sendMessage({
        id: "onIceCandidate",
        candidate: candidate
    });
}

function onOffer(error, offerSdp) {
    sendMessage({
        id: "start",
        sdpOffer: offerSdp
    });
}
```

WebSocket メッセージが受信されると、通信の開始、ICE 候補の変更、またはエラー状態
のいずれかが発生するたびに、適切な処置がとられます。

```javascript
ws.onmessage = function (message) {
    var parsedMessage = JSON.parse(message.data);

    switch (parsedMessage.id) {
        case "startResponse":
            webRtcPeer.processAnswer(message.sdpAnswer);
            break;
        case "iceCandidate":
            webRtcPeer.addIceCandidate(parsedMessage.candidate);
            break;
        case "error":
            // Error Handling
            break;
    }
};
```

コードの詳しい説明は、**Kurento** のドキュメンテーションにあります。

<a name="altering-media-streams"></a>

# メディア・ストリームの変更

コンテキストを変更するには、メディア・ストリームを処理できる必要があります。この
2 番目の例は、以前の WebRTC ループバック・ビデオ通信をベースにしていますが、顔を
検出し、検出された顔の上に帽子を置くことによって、メディア・ストリームを分析し変
更します。これは、Computer Vision と Augmented Reality のフィルタの例です。

![](https://fiware.github.io/tutorials.Media-Streams/img/magic-mirror.png)

<a name="magic-mirror---start-up"></a>

## マジック・ミラー - 起動

メディア・ストリームを変更する **Kurento** の例でシステムを起動するには、次のコ
マンドを実行します :

```console
./services magic-mirror
```

<a name="magic-mirror---running-the-example"></a>

### マジック・ミラー - 例を実行

この例を実行するには、WebRTC 互換ブラウザを`https://localhost:8443` で開き、ペー
ジをオープンするために、HTTPS トラフィックを受け入れます。アプリケーションは、2
つの HTML5 `<video>` タグを含む、1 つの HTML Web ページで構成されています。1 つ
はローカル Web サーバでキャプチャされたローカル・ストリームと、もう 1 つはメディ
ア・サーバからクライアントに送信されたリモート・ストリームです。"Start "ボタンを
クリックすると、修正されたビデオが右側に表示されます。

![](https://fiware.github.io/tutorials.Media-Streams/img/magic-mirror-screenshot.png)

[マジック・ミラーのデモ](https://www.youtube.com/watch?v=h84HFkvWGgw "Magic Mirror")
を見るにはリンクをクリックしてください。

<a name="magic-mirror---analyzing-the-code"></a>

## マジック・ミラー - コードの分析

議論中のコードは、Git リポジトリ内の `kurento-magic-mirror` ディレクトリ内にあり
ます。この例は、以前の `hello world` 例と、多くの一般的で定型の接続で構築されて
います。Web ページとアプリケーション・サーバの間の WebSocket 接続、および アプリ
ケーション・サーバと **Kurento Media Server** 間の接続は同じです。上記のセクショ
ンを参照して理解を深めてください :

-   Back-End - WebSocket 接続
-   Back-End - Kurento への接続
-   Front-End - レンダリングされたページの JavaScript

<a name="back-end---adding-a-built-in-filter-to-a-media-pipeline"></a>

### バック・エンド - ビルトイン・フィルタをメディア・パイプラインに追加

前の例と比較して主な違いは 、ビデオ出力を Web ページに送る前にビデオ出力を変更す
る**フィルタ**を追加することです。この `kms-filters` モジュールは、デフォルトで
**Kurento Media Server** の一部としてロードされます。このモジュールには、次の組
み込
み[フィルタ](https://doc-kurento.readthedocs.io/en/latest/features/kurento_api.html#filters)が
含まれています :

-   `ZBarFilter` フィルタは、ビデオ・ストリームで QR バーコードを検出します。コ
    ードが見つかると、フィルタは `CodeFoundEvent` を上げます
-   `FaceOverlayFilter` フィルタは、ビデオ・ストリーム内の顔を検出し、設定可能が
    画像でオーバーレイします
-   `GStreamerFilter` は、**Kento** Media Pipelines で
    [GStreamer](https://gstreamer.freedesktop.org/) フィルタを使用できる汎用フィ
    ルタ・インタフェースです

`FaceOverlayFilter` のようなビルトイン・フィルタは、`pipeline.create()` 関数を使
用して作ることができます。したがって、以下のように `createMediaElements()` と
`connectMediaElements()` 関数を拡張する必要があります：

```javascript
function createMediaElements(pipeline, ws, callback) {
    pipeline.create("WebRtcEndpoint", (error, webRtcEndpoint) => {
        if (error) {
            return callback(error);
        }
        // Once the WebRtcEndpoint is created create a FaceOverlayFilter
        pipeline.create("FaceOverlayFilter", (error, faceOverlayFilter) => {
            if (error) {
                return callback(error);
            }
            // This adds the Mario hat
            faceOverlayFilter.setOverlayedImage(
                url.format(asUrl) + "img/mario-wings.png",
                -0.35,
                -1.2,
                1.6,
                1.6,
                function (error) {
                    if (error) {
                        return callback(error);
                    }
                    return callback(null, webRtcEndpoint, faceOverlayFilter);
                }
            );
        });
    });
}
```

```javascript
function connectMediaElements(webRtcEndpoint, faceOverlayFilter, callback) {
    webRtcEndpoint.connect(faceOverlayFilter, (error) => {
        if (error) {
            return callback(error);
        }
        faceOverlayFilter.connect(webRtcEndpoint, (error) => {
            if (error) {
                return callback(error);
            }

            return callback(null);
        });
    });
}
```

`start()` 関数で呼び出される関数は同じままです。メディア・ストリームは接続され
、Web ページに戻される前にパイプラインを通過します。その結果、ビデオ・ストリーム
が今度はデモされたようにインターセプトされ、変更されます。

<a name="raising-context-events"></a>

# コンテキスト・イベントの発生

また、メディア・ストリームを分析して、コンテキスト関連のイベントを発生させること
もできます。このチュートリアルの最後の例では、WebRTC ビデオ通信に車両ナンバー・
プレート検出フィルター要素を追加します。

![](https://fiware.github.io/tutorials.Media-Streams/img/plate-detector.png)

発生したイベントはコンテキスト・エンティティに関連する可能性があるため、FIWARE
エコシステム内への統合に適しています。たとえば、**Kurento** がセキュリティ・カメ
ラに接続されている場合、Orion Context Broker に PATCH リクエストを送信してエンテ
ィティ **Camera X** のコンテキストを更新して、車両登録プレート XXX-xxx-XXX が
、yyy-yyy-yyy 時に検出されたことを示すをコードを追加します。

<a name="plate-detector----start-up"></a>

## プレート検出器 - 起動

**Kurento** イベントを発生させる例でシステムを起動するには、次のコマンドを実行し
ます :

```console
./services plate-detection
```

<a name="plate-detector---running-the-example"></a>

### プレート検出器 - サンプルの実行

この例を実行するには、WebRTC 互換ブラウザを `https://localhost:8443` で開き、ペ
ージをオープンするために HTTPS トラフィックを受け入れます。アプリケーションは、2
つの HTML5 `<video>` タグを含む 1 つの HTML Web ページで構成されています。1 つは
ローカル Web サーバでキャプチャされたローカル・ストリームと、もう 1 つはメディア
・サーバからクライアントに送信されたリモート・ストリームです。"Start" ボタンをク
リックすると、両方の `<video>` 要素に同じビデオが表示されます。

下のリストから車両登録プレートの画像を選択し、それをあなたのスマホで表示し、プレ
ートが検出されているかどうかを確認してください

-   [アルゼンチン](https://fiware.github.io/tutorials.Media-Streams/img/vrn-argentina.jpg)
-   [ボツワナ](https://fiware.github.io/tutorials.Media-Streams/img/vrn-botswana.jpg)
-   [オーストラリア (NSW)](https://fiware.github.io/tutorials.Media-Streams/img/vrn-new-south-wales.jpg)
-   [オーストラリア (WA)](https://fiware.github.io/tutorials.Media-Streams/img/vrn-western-australia.jpg)
-   [カナダ](https://fiware.github.io/tutorials.Media-Streams/img/vrn-new-brunswick.jpg)
-   [フィンランド](https://fiware.github.io/tutorials.Media-Streams/img/vrn-finland.jpg)
-   [インド](https://fiware.github.io/tutorials.Media-Streams/img/vrn-india-kolkata.jpg)
-   [ロシア](https://fiware.github.io/tutorials.Media-Streams/img/vrn-russia.jpg)
-   [スウェーデン](https://fiware.github.io/tutorials.Media-Streams/img/vrn-sweden.jpg)

[Wikipedia](https://en.wikipedia.org/wiki/Vehicle_registration_plate) ではさらに
車両登録プレートの画像が入手できます

![](https://fiware.github.io/tutorials.Media-Streams/img/plate-detector-screenshot.png)

たとえば、上のスクリーン・ショットに表示されている車両登録プレートの場合、次の出
力が得られます。

```
License plate detected --8886AJR
```

検出の信頼性は、使用するカメラとフィルタによって異なります。

<a name="plate-detector---analyzing-the-code"></a>

## プレート検出器 - コードの分析

議論中のコードは、Git リポジトリ内の `kurento-platedetector` ディレクトリ内にあ
ります。

ここでも、以前の例によく似た定型的な接続 - Web ページとアプリケーション・サーバ
間の WebSocket 接続コードと、アプリケーション・サーバと **Kurento Media Server**
間の接続は変更されていません。上記のセクションを参照して理解を深めてください。

-   Back-End - WebSocket 接続
-   Back-End - Kurento への接続
-   Front-End - レンダリングされたページの JavaScript

<a name="back-end---adding-a-custom-filter-to-a-media-pipeline"></a>

### バック・エンド - カスタム・フィルタをメディア・パイプラインに追加

提供される基本的な `kms-filters` フィルタ以外に、ビデオを処理したり、イベントを
検出したりす
る[独自のカスタム・コードを記述する](https://doc-kurento.readthedocs.io/en/stable/user/writing_modules.html)必
要があり ます。これらのカスタム・フィルタは、メディア・サーバのセットアップ時に
通常インストールされ
る[モジュール](https://doc-kurento.readthedocs.io/en/stable/features/kurento_modules.html)で
す。

独自のカスタム・モジュールを最初から作成するは、このチュートリアルの範囲を超えて
いるで、Kurento 開発チームが作成済の 4 つの自由に配布可能なカスタムモジュールを
使用します :

-   `kms-pointerdetector`: カラー・トラッキングに基づいてビデオ・ストリーム内の
    ポインタを検出するフィルタ
-   `kms-chroma`: 最上部のレイヤーで色の範囲を取り、それを透明にして、後ろの別の
    画像を明らかにするフィルタ
-   `kms-crowddetector`: ビデオ・ストリームで人の集団を検出するフィルタ。
-   `kms-platedetector`: ビデオ・ストリームで車のプレートを検出するプロトタイプ
    ・フィルタ - 本番用ではありません

モジュールをインストールするには、カスタム
[Dockerfile](https://github.com/FIWARE/tutorials.Media-Streams/blob/master/docker-compose/Dockerfile)
を使用してデフォルトの `fiware/stream-oriented-kurento` Docker イメージを拡張す
る必要があります。この Dockerfile は、以下のようにモジュールをインストールします
:

```bash
apt-get -y install kms-pointerdetector-6.0 \
&& apt-get -y install kms-crowddetector-6.0 \
&& apt-get -y install kms-platedetector-6.0 \
&& apt-get -y install kms-chroma-6.0 \
```

この例では、フィルタを使用可能にするために `kurento-module-platedetector` モジュ
ールを登録する必要があります :

```javascript
const kurento = require("kurento-client");
kurento.register("kurento-module-platedetector");
```

`platedetector.PlateDetectorFilter` は、`pipeline.create()` 関数を使用して作成す
ることができます。メディア・パイプラインにフィルタを追加するには、次のように
`createMediaElements()` と `connectMediaElements()` 関数を拡張します :

```javascript
function createMediaElements(pipeline, ws, callback) {
    pipeline.create("WebRtcEndpoint", (error, webRtcEndpoint) => {
        if (error) {
            return callback(error);
        }

        pipeline.create("platedetector.PlateDetectorFilter", (error, filter) => {
            if (error) {
                return callback(error);
            }

            return callback(null, webRtcEndpoint, filter);
        });
    });
}
```

```javascript
function connectMediaElements(webRtcEndpoint, filter, callback) {
    webRtcEndpoint.connect(filter, (error) => {
        if (error) {
            return callback(error);
        }

        filter.connect(webRtcEndpoint, (error) => {
            if (error) {
                return callback(error);
            }

            return callback(null);
        });
    });
}
```

<a name="front-end---javascript-on-the-rendered-page-1"></a>

### フロント・エンド - レンダリングされたページの JavaScript

前の例の標準的な定型処理に加えて、`platedetector.PlateDetectorFilter` によって生
成された `plateDectected` イベントを処理するために、WebSocket 処理に case 句が追
加されます :

```javascript
ws.onmessage = function(message) {
	var parsedMessage = JSON.parse(message.data);
	console.info('Received message: ' + message.data);

	switch (parsedMessage.id) {
...etc
	case 'plateDetected':
		plateDetected(parsedMessage);
		break;
	}
```

```javascript
function plateDetected(message) {
    console.log("License plate detected " + message.data.plate);
}
```

結果は、車両登録プレートの詳細が画面に表示されます。

# 次のステップ

高度な機能を追加することで、アプリケーションに複雑さを加える方法を知りたいですか
？このシリーズ
の[他のチュートリアル](https://www.letsfiware.jp/fiware-tutorials)を読むことで見
つけることができます

**Kurento Media Server** の機能の詳細については
、[Kurento チュートリアル・ドキュメント](https://doc-kurento.readthedocs.io/)を
参照してください。

---

## ライセンス

[MIT](LICENSE) © 2018-2023 FIWARE Foundation e.V.

プログラムには、ライセンスの下で入手した追加のサブモジュールが含まれています :

-   [kurento-example/nodejs](https://github.com/Kurento/kurento-tutorial-node) -
    © [Kurento](http://kurento.org) **Apache 2.0 license**
-   [kurento-example/java](https://github.com/Kurento/kurento-tutorial-java) - ©
    [Kurento](http://kurento.org) **Apache 2.0 license**
-   [kurento-example/client-side-javascript](https://github.com/Kurento/kurento-tutorial-js) -
    © [Kurento](http://kurento.org) **Apache 2.0 license**

車両登録プレートの画像は、パブリック・ドメインであるか、またはライセンスの下で
Wikipedia Commons から取得しています：

-   Argentina ©
    [Quilmeño89](https://commons.wikimedia.org/wiki/User:Quilme%C3%B1o89)
    **Creative Commons Attribution-Share Alike 4.0 International**
-   Australia ©
    [EurovisionNim](https://commons.wikimedia.org/wiki/User:EurovisionNim)
    **Creative Commons Attribution-Share Alike 4.0 International**
-   Finland © [Krokodyl](https://commons.wikimedia.org/wiki/User:Krokodyl)
    **Creative Commons Attribution 2.5 Generic**
-   India ©
    [Biswaruo Ganguly](https://commons.wikimedia.org/wiki/User:Gangulybiswarup)
    **Creative Commons Attribution 3.0 Unported**
-   Russia © [Krokodyl](https://commons.wikimedia.org/wiki/User:Krokodyl)
    **Creative Commons Attribution 2.5 Generic**
-   Sweden © [Lalpino](https://commons.wikimedia.org/wiki/User:Lalpino)
    **Creative Commons Attribution-Share Alike 4.0 International**
