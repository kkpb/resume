# 職務経歴書
バージョン: 2022/10/28

最新版: https://github.com/kkpb/resume

- Shohei KOKUBO
- 生年月日: 1991/11/07
- email: kkb1107@gmail.com


## 職務経歴

### 株式会社ドワンゴ
- 2016/04 新卒入社
- 2019/01 退職

#### Dwango Media Cluster

##### 技術
- Elang/OTP
- Python3
- Git
- Docker
- Docker Compose
- Jenkins2

##### Dwango Media Cluster について
- ニコニコ動画/生放送の配信基盤
- `Erlang/OTP` 製
- 800台以上のサーバで分散・並行処理を行っている
- 参考資料
  - [Developers Summit 2015](https://www.slideshare.net/takahiro_yachi/ss-44828680)
  - [Developers Summit 2017](https://niconare.nicovideo.jp/watch/kn2331)
  - [Erlang & Elixir Fest 2017](https://niconare.nicovideo.jp/watch/kn2397)
  - [Erlang & Elixir Fest 2018](https://speakerdeck.com/amutake/erlang-shi-li-shao-jie-medeiasutorimuzhong-ji-sisutemu)

##### サービスを止めることなくサーバの更新が行えるデプロイ方法の検証
- 検証を行うに際に以下のことを考慮する必要がある
  - 本番環境ではアクセスが少ない時間帯に全体の25%づつ停止して4日間かけて更新を行う
  - ノード毎にロールが決まっているので偏りが起こらないようにする必要がある
  - 更新前・更新後のノード間でデータのやり取りが発生する場合があるので互換性を維持しなければならない
    - 同名で要素数や要素名・データ構造が異なる record を渡すとクラッシュする等
- 解決方法
  - 本番環境と同規模のクラスタを組むのは不可能なので各ロールを最低2台以上用意し Ansible を用いて検証環境を構築
  - ロール等を考慮して更新するノードを決定するスクリプトを作成
  - rpc 等でリモートノードと通信している箇所の互換性の確認、修正の必要があれば互換性を保つために JSON 等でシリアライズするように変更
  - 検証環境に50%づつデプロイして動作確認
    - 動作確認に必要なツール・スクリプトの作成
    - [recon](https://github.com/ferd/recon) 等のデバッグツールを利用して確認
- 初回の無停止更新をトラブルなく終えることができた

##### タイムシフト機能の設計・実装
- 問題
  - タイムシフト用のデータは [Frugalos](https://github.com/frugalos/frugalos) に保存されるが Frugalos は視聴ノードとは別のノードに立っているので視聴時に毎回取得するとパフォーマンスが出ないので視聴ノードにキャッシュする
     - [Frugalos について](https://dwango.github.io/articles/frugalos/)
  - 視聴の手順として API ノードに視聴リクエストを投げて視聴ノードがアサインされ視聴 URI 返すがパフォーマンスを上げるためにはキャッシュされている視聴ノードを優先的にアサインする必要がある
- 解決方法
  - 視聴ノードのアサイン時にキャッシュ情報を保存するために分散 KVS を設計・実装
    - API ノード全台で起動しキャッシュ情報を同期するようなもの
    - KVS に対する操作はメッセージ順に依存するので各ノードで内容が異なる可能性があるが軽量で分断に強い(CAP 定理の AP を保証している)
  - キャッシュ情報に基づいてアサインが行われるので特定のノードにアクセスが集中しノードのキャパシティを超えた場合に別のノードを選択するようにアサインロジックを変更
- 1000 rps 以上の視聴セッション作成リクエストに対し失敗なくデータがキャッシュされたノードにアサインすることを可能にした

### 株式会社ネオキャリア
- 2019/06 中途入社
- 2021/12 退職

#### ドローンからの映像の低遅延化
- 技術
  - Python3
  - Git
  - WebRTC
  - RaspberryPi
- 概要
  - ドローンを操作する際に手元のモニタにドローンからの映像を映して操作を行う場面が多い
  - 手元の映像の遅延が大きいと障害物に衝突する等のトラブルを回避するのが難しくなる
  - 様々なメーカーのドローンに対応するためマイコンを搭載する形で低遅延映像を実現するために Raspberry Pi を利用
- 役割
  - NTT の [SkyWay](https://webrtc.ecl.ntt.com/) や [Twilio](https://www.twilio.com/ja/), [Agora.io](https://www.agora.io/en/) 等の WebRTC の商用サービスの調査・選定およびこれらのサービスを用いたプロトタイプの実装
  - OSS の [Janus](https://github.com/meetecho/janus-gateway) を利用したプロトタイプの実装
  - 実際にドローンに搭載しての動作確認

#### 競輪の動画配信システム
- 技術
  - Python3
  - JavaScript
  - Git
  - AWS
    - Lambda
    - CloudFormation
    - Amplify
    - S3
    - DynamoDB
    - Cognito
    - API Gateway
    - CloudFront
    - Elemental
  - Vue.js
- 概要
  - 現状の配信状況としてはスピードチャンネル(CS 放送)、公式サイト、YouTube, ニコニコ生放送等の各配信サイトで放送・配信が行われている
    - これらは以下のような問題を抱えている
      - スピードチャンネルと他で映像の入力が異なっている
        - スピードチャンネルはブロンズシステムと言う専用回線を持つ配信システムを利用
        - 公式サイト、各配信サイトは配信用の機材を各競輪場に個別に用意することで配信
      - 各競輪場の配信担当は異なる2つの配信環境を整える必要があり、オペレーション的にも手間がかかっている
  - 競輪の映像は海外で違法配信され賭けの対象となっているという問題もある
  - 映像元の一元化および海外・実況等を行いたい個人への公式映像の販売を目指して AWS 上に配信システムを構築する
  - ブロンズシステムを経由した映像はデータセンタに一度集約されるのでそこから映像を取得
  - システムの利用者は受信用の RTMP アドレスを登録しておくことでレースが始まる時間になると自動で映像を受信することができる
  - 基本的にデータセンタ→本配信システム→各配信サイトというような映像を中継することが役割となるが、公式サイト等での配信を目的とし HLS による映像配信も可能とする
- 役割
  - 本システムの要件定義および設計
  - 競輪場ごとに AWS Elemental MediaLive にチャンネルおよび受信用の用意する必要があり、新たに競輪場が追加された場合も考えて環境構築に AWS CloudFromation を利用
    - 今後、ほかの公営競技(オートレース等)でも利用される可能性があることも考慮
  - フロント側ではユーザ登録、受信用アドレスの登録、各競輪場の配信状況の確認等が行える必要があり、バックエンドとして AWS Lambda(Python), AWS API Gateway, AWS Cognito, AWS DynamoDB を利用し実装

#### Zatsudan
- [Zatsudan](https://zatsudan.com/)
- 技術
  - WebRTC
- 概要
  - 双方向性のある配信を行えることを目的とする
    - 視聴者はいま話していることについてすばやくコメントや質問がすることができる
    - 配信者はコメントや質問にすばやく反応することができる
  - 配信中に手軽にほかの人を呼んでコラボ配信のようなことを可能にする
  - 双方向性のための低遅延配信等が要求されるので WebRTC SFU が可能なサービスを利用して実現
  - 録画等の要件も満たしている Agora.io を利用
- 役割
  - 要件定義および初期設計を担当

### workhouse株式会社
2022/01 中途入社

#### FULL KAITEN
- 技術
  - Rust
  - TypeScript
  - Python3
  - Git
  - GraphQL
  - React.js
  - PostgreSQL
  - Elasticsearch
- [FULL KAITEN](https://full-kaiten.com/)
- 概要
  - 受託開発で既存機能の改修および新機能の設計・実装
- 役割
  - 既存機能の改修
  - フロント・バックエンドのコードレビュー
  - 新機能の DB 設計およびクエリ最適化
  - ユニットテスト
  - デバッグ

## その他
- O'Reilly [レガシーコードからの脱却](https://www.oreilly.co.jp/books/9784873118864/) 翻訳レビュー 2019年

## 学歴
- 2014/03 琉球大学工学部情報工学科 卒業
- 2016/03 琉球大学大学院理工学研究科情報工学専攻 卒業
