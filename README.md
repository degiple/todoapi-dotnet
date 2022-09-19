# todoapi-dotnet

ASP.NET core と Docker を使って、コンテナ開発を学ぶためのコンテンツです。

## ハンズオンの準備

### GitPod

このコンテンツでは、GitPod の利用を推奨しています。

以下の手順に従って、GitPod の準備を行なって下さい。

1. [GitHub アカウントを作成する](https://pengi-n.co.jp/blog/github-account/)
1. Chrome 拡張機能 [Gitpod - Always ready to code](https://chrome.google.com/webstore/detail/gitpod-always-ready-to-co/dodmmooeoklaejobgleioelladacbeki) をインストールする。
1. [todoapi-dotnet](https://github.com/degiple/todoapi-dotnet) にアクセスし、表示されている Gitpod ボタンをクリックする。
1. 作成した GitHub アカウントでログインし、GitHub と GitPod の連携を承認する。
1. ブラウザで IDE が起動される。


## ハンズオン!!

さて、これからあなたには Todoアプリケーションのコンテナ化を目指して構築作業行なって頂きます。

まずはサンプルAPIの起動から始めましょう

### サンプルAPIの起動

```shell
# アプリケーション実行
dotnet run -p ToDoAPI/ --urls http://+:8080
# または cd ToDoAPI && dotnet run --urls http://+:8080
```


### ToDoAPIの追加

関連ファイルの配置

```shell
```


```shell
# アプリケーション実行
dotnet run -p ToDoAPI/ --urls http://+:8080
# または cd ToDoAPI && dotnet run --urls http://+:8080
```

Swaggerを使った動作確認


CLIでの動作確認

```shell
## 登録
curl http://localhost:8080/api/todoitems -XPOST \
-H 'Content-Type: application/json' \
-d '{"name": "my first item"}' | jq .

## リスト
curl http://localhost:8080/api/todoitems

## １件取得
curl http://localhost:8080/api/todoitems/1

## 削除
curl http://localhost:8080/api/todoitems/1 -XDELETE -v

```

### WebUIの追加


### Dockerfileの作成

```shell
# コンテナイメージの作成（ビルド）
docker build .
```

### docker-compose.ymlの作成

```shell
# コンテナ起動
docker-compose up
```

### 拡張機能によるDockerfileの自動作成

...

