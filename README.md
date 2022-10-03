# todoapi-dotnet

ASP.NET core と Docker を使って、コンテナ開発を学ぶためのコンテンツです。

## 前提・制約

- このコンテンツでは、ASP.NETの詳しい説明はありません

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

### プロジェクトの作成

まずは テンプレートからASP.NETプロジェクト`TodoApi`を作成しましょう

```shell
dotnet new webapi -o TodoApi
```

次に、インメモリ用のEFCoreをインストールしておきます。  
EFCoreにより、Webアプリ側のみで一時的なDBをメモリ上に構成することができます。

```shell
cd TodoApi
dotnet add package Microsoft.EntityFrameworkCore.InMemory
```

### サンプルの WeatherForecast API を試してみる

プロジェクトに最初から用意されているサンプルのAPIで、挙動を確認してみましょう！  
※コマンドを実行するディレクトリの位置に注意してください。

1. TodoApiプロジェクトの実行
```shell
cd /workspace/todoapi-dotnet
dotnet run --project TodoApi --urls http://+:8080
# または cd TodoApi && dotnet run --urls http://+:8080
```

2. 別のターミナルを立ち上げ

![image](https://user-images.githubusercontent.com/65447508/193514013-215a52ea-14b5-40b7-8049-640277b24e0d.png)

3. WeatherForecast API を CURLコマンドで確認
```shell
curl http://localhost:8080/weatherforecast | jq
```

<img width="955" alt="image" src="https://user-images.githubusercontent.com/65447508/193513739-ad7e889e-4d3d-4730-8464-22b41bef133a.png">


今度はSwaggerを使って動作確認しましょう！

1. PORTSタグを開き、URLをコピーする
![image](https://user-images.githubusercontent.com/65447508/193514338-bd696fdf-423c-48e9-a81c-342afe8ecbde.png)

2. ブラウザで新しくタブを開き、以下のようにURLを入力する
```
＜コピーしたURL＞/swagger

# 例： https://8080-degiple-todoapidotnet-lv9mmfnsijs.ws-us69.gitpod.io/swagger
```

3. /WeatherForecast をクリック

4. Try in out をクリック

5. Exexute をクリック

もし上手くいけば以下のような画面が表示されます！
<img width="1262" alt="image" src="https://user-images.githubusercontent.com/65447508/193514920-fa60983b-08b3-4431-96b5-885462ed1919.png">


### ToDo API の実装

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

