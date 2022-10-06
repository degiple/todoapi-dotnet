# todoapi-dotnet

ASP.NET core と Docker を使って、コンテナ開発を学ぶためのコンテンツです。

## 前提・制約

- ASP.NETの詳しい説明はありません
- GitPod の利用を前提としております

## ハンズオンの準備

### GitPod

以下の手順に従って、GitPod の準備を行なって下さい。

1. [GitHub アカウントを作成する](https://pengi-n.co.jp/blog/github-account/)
1. Chrome 拡張機能 [Gitpod - Always ready to code](https://chrome.google.com/webstore/detail/gitpod-always-ready-to-co/dodmmooeoklaejobgleioelladacbeki) をインストールする。
1. [todoapi-dotnet](https://github.com/degiple/todoapi-dotnet) にアクセスし、表示されている Gitpod ボタンをクリックする。
1. 作成した GitHub アカウントでログインし、GitHub と GitPod の連携を承認する。
1. ブラウザで IDE が起動される。

## ハンズオン!!

さて、これからあなたには Todoアプリケーションのコンテナ化を目指して構築作業を行なって頂きます。

### (1) プロジェクトの作成

まずは テンプレートからASP.NETプロジェクト`TodoApi`を作成しましょう

```shell
dotnet new webapi -o TodoApi
```

次に、インメモリ用のEFCoreをインストールしておきます。  
EFCoreにより、Webアプリ側のみで一時的なDBをメモリ上に構成することができます。

```shell
cd /workspace/todoapi-dotnet/TodoApi
dotnet add package Microsoft.EntityFrameworkCore.InMemory
```

### (2) サンプルの WeatherForecast API を試してみる

プロジェクトに最初から用意されているサンプルのAPIで、挙動を確認してみましょう！  
※コマンドを実行するディレクトリの位置に注意してください。

1. TodoApiプロジェクトの実行

```shell
cd /workspace/todoapi-dotnet
dotnet run --project TodoApi --urls http://+:8080
```

2. 別のターミナルを立ち上げる

![image](https://user-images.githubusercontent.com/65447508/193514013-215a52ea-14b5-40b7-8049-640277b24e0d.png)

3. WeatherForecast API を curl コマンドで確認
```shell
curl http://localhost:8080/weatherforecast | jq
```
<img width="955" alt="image" src="https://user-images.githubusercontent.com/65447508/193513739-ad7e889e-4d3d-4730-8464-22b41bef133a.png">


今度は Swagger を使って動作確認しましょう！

Swaggerって何？



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

最後に、1つ目のターミナルで実行していたアプリケーションを Ctl + Cキー で強制終了しておきましょう。


### (3) ToDo API の実装

それではTodoアプリケーションを構築してきましょう！　　 
尚、本コンテンツでは事前にソースコードを準備しています。

1. モデルクラスの追加
```shell
cp -r /workspace/todoapi-dotnet/work/Models/ /workspace/todoapi-dotnet/TodoApi/Models/
ls /workspace/todoapi-dotnet/TodoApi/Models/
```
```log
TodoContext.cs  TodoItems.cs
```

2. 依存関係を解決するように Program.cs を更新
```shell
cp /workspace/todoapi-dotnet/TodoApi/Program.cs /workspace/todoapi-dotnet/TodoApi/Program.cs.backup # バックアップ
cp /workspace/todoapi-dotnet/work/Program.cs /workspace/todoapi-dotnet/TodoApi/Program.cs
ls /workspace/todoapi-dotnet/TodoApi/Program.cs
```
```log
/workspace/todoapi-dotnet/TodoApi/Program.cs
```

3. コントローラーの追加
 
```shell
cp /workspace/todoapi-dotnet/work/Contralloers/TodoItemsController.cs /workspace/todoapi-dotnet/TodoApi/Controllers/TodoItemsController.cs
ls /workspace/todoapi-dotnet/TodoApi/Controllers/TodoItemsController.cs
```
```log
/workspace/todoapi-dotnet/TodoApi/Controllers/TodoItemsController.cs
```

4. Webアプリケーションの実行

```shell
cd /workspace/todoapi-dotnet
dotnet run --project TodoApi --urls http://+:8080
```
```log
Welcome to .NET 6.0!
---------------------
SDK Version: 6.0.200

Telemetry
---------
The .NET tools collect usage data in order to help us improve your experience. It is collected by Microsoft and shared with the community. You can opt-out of telemetry by setting the DOTNET_CLI_TELEMETRY_OPTOUT environment variable to '1' or 'true' using your favorite shell.

Read more about .NET CLI Tools telemetry: https://aka.ms/dotnet-cli-telemetry

----------------
Installed an ASP.NET Core HTTPS development certificate.
To trust the certificate run 'dotnet dev-certs https --trust' (Windows and macOS only).
Learn about HTTPS: https://aka.ms/dotnet-https
----------------
Write your first app: https://aka.ms/dotnet-hello-world
Find out what's new: https://aka.ms/dotnet-whats-new
Explore documentation: https://aka.ms/dotnet-docs
Report issues and find source on GitHub: https://github.com/dotnet/core
Use 'dotnet --help' to see available commands or visit: https://aka.ms/dotnet-cli
--------------------------------------------------------------------------------------
Building...
info: Microsoft.Hosting.Lifetime[14]
      Now listening on: http://[::]:8080
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
info: Microsoft.Hosting.Lifetime[0]
      Content root path: /workspace/todoapi-dotnet/TodoApi/
```

5. ターミナルでの動作確認をしてみましょう

Todoの登録（1つ目）

```shell
curl http://localhost:8080/api/todoitems -XPOST \
-H 'Content-Type: application/json' \
-d '{"name": "my first item"}' | jq .
```
```log
{
  "id": 1,
  "name": "my first item",
  "isComplete": false
}
```

Todoの登録（２つ目）

```shell
curl http://localhost:8080/api/todoitems -XPOST \
-H 'Content-Type: application/json' \
-d '{"name": "my second item"}' | jq .
```
```log
{
  "id": 2,
  "name": "my second item",
  "isComplete": false
}
```


登録したToDoの一覧を取得

```shell
curl http://localhost:8080/api/todoitems | jq .
```
```log
[
  {
    "id": 1,
    "name": "my first item",
    "isComplete": false
  },
  {
    "id": 2,
    "name": "my second item",
    "isComplete": false
  }
]
```

登録した1つ目のToDoを取得

```shell
curl http://localhost:8080/api/todoitems/1 | jq .
```
```log
{
  "id": 1,
  "name": "my first item",
  "isComplete": false
}
```

ToDoの更新

```shell
# 更新
curl http://localhost:8080/api/todoitems/1 -XPUT \
-H 'Content-Type: application/json' \
-d '{"id": 1, "name": "ハンズオンを完了させる！", "isComplete": false}' 

# 更新されたか確認
curl http://localhost:8080/api/todoitems/1 | jq .
```
```log
{
  "id": 1,
  "name": "ハンズオンを完了させる！",
  "isComplete": false
}
```

Todoの削除

```shell
# 削除
curl http://localhost:8080/api/todoitems/1 -XDELETE

# 削除されたか確認
curl http://localhost:8080/api/todoitems/1 | jq .
```
```log
{
  "type": "https://tools.ietf.org/html/rfc7231#section-6.5.4",
  "title": "Not Found",
  "status": 404,
  "traceId": "00-175af459cd4e48af097f8e6722204cbe-8b559381bf4caeac-00"
}
```

6. (Option) 前章までの内容を参考に、Swaggerでも動作確認をしてみましょう！


### (Option) Web画面の追加

ToDoをWeb画面で操作したくなりませんか？

シンプルな画面コードを用意しましたので、配置して試してみましょう！

1. 静的ファイル(with Vue.js)の配置
```shell
cp -r /workspace/todoapi-dotnet/work/wwwroot /workspace/todoapi-dotnet/TodoApi/wwwroot/
```

2. Webアプリケーションの実行
```shell
cd /workspace/todoapi-dotnet
dotnet run --project TodoApi --urls http://+:8080
```

3. PORTSタグを開き、Open Browserをクリック
<img width="973" alt="image" src="https://user-images.githubusercontent.com/65447508/193523273-c632dc6e-35ca-47a8-a421-b521968722aa.png">

4. Web UI が表示されます
<img width="1155" alt="image" src="https://user-images.githubusercontent.com/65447508/193523504-5a97596e-cd6e-4241-bba3-ff42e866754d.png">


### (4) TodoAPIのコンテナ化

いよいよコンテナ化です…！

1. Dockerfileの配置
```shell
# Dockerfileの配置
cp /workspace/todoapi-dotnet/work/Dockerfile /workspace/todoapi-dotnet/TodoApi/Dockerfile

# コンテナビルド時に無視するファイル群を指定（本来はどちらかに配置）
cp /workspace/todoapi-dotnet/work/.dockerignore /workspace/todoapi-dotnet/TodoApi/.dockerignore
```

2. コンテナイメージを todoapi というタグ名で作成
```shell
cd /workspace/todoapi-dotnet
docker build --file ./TodoApi/Dockerfile --tag todoapi:latest .
```

3. 作成したコンテナイメージで、コンテナの作成・起動（バックグラウンド）
```shell
docker run -d --name todoapi-sample todoapi:latest 
```
```shell
gitpod /workspace/todoapi-dotnet (hansdon) $ docker run -d --name todoapi-sample todoapi:latest 
53ac1ebe2143f7007e6872116f6443a0845600c879bfb5f9dbb0056233feb839
```

4. 正常に作成・起動しているか確認
```shell
# 作成されているコンテナの確認
docker container ls -a

# 起動しているコンテナの確認
docker ps

# 起動しているコンテナのログを確認
docker logs todoapi-sample
or
docker logs -f todoapi-sample  # Follow log output
```
```log
gitpod /workspace/todoapi-dotnet (handon) $ docker container ls -a
CONTAINER ID   IMAGE            COMMAND                CREATED         STATUS         PORTS      NAMES
53ac1ebe2143   todoapi:latest   "dotnet TodoApi.dll"   9 seconds ago   Up 8 seconds   8080/tcp   todoapi-sample
gitpod /workspace/todoapi-dotnet (handon) $ docker ps
CONTAINER ID   IMAGE            COMMAND                CREATED          STATUS          PORTS      NAMES
53ac1ebe2143   todoapi:latest   "dotnet TodoApi.dll"   15 seconds ago   Up 13 seconds   8080/tcp   todoapi-sample
gitpod /workspace/todoapi-dotnet (handon) $ docker logs todoapi-sample
info: Microsoft.Hosting.Lifetime[14]
      Now listening on: http://[::]:8080
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Production
info: Microsoft.Hosting.Lifetime[0]
      Content root path: /app/
```

5. 前章までの内容を参考に動作確認してみましょう！
  - ターミナル(CURL)　
  - Swagger
  - Web UI

6. 起動しているコンテナを停止する
```shell
docker stop todoapi-sample
docker ps 
```
```log
gitpod /workspace/todoapi-dotnet (handson) $ docker ps
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
```

7. 作成済のコンテナを削除する
```shell
docker rm todoapi-sample
docker container ls -a
```

### (5) docker-composeによる起動

docker-composeは、複数のDockerコンテナを定義し実行するDockerアプリケーションのためのツールです。
  
まだ単一のWebアプリコンテナだけですが、いずれDBコンテナ等と同時起動をしてみたいですよね!?
という訳で、docker-composeでのコンテナ起動もやってみましょう！

1. docker-compose.yamlの配置
```shell
cd /workspace/todoapi-dotnet/work/docker-compose.yml /workspace/todoapi-dotnet/
```

2. docker-composeによるコンテナ起動（バックグラウンド）
```shell
docker-compose up -d 
```

3. docker-composeで正常に作成・起動しているか確認
```shell
# 起動しているコンテナの確認
docker-compose ps

# 起動しているコンテナのログを確認
docker-compose logs todoapi
or
docker-compose logs -f todoapi  # Follow log output
```

4. 前章までの内容を参考に動作確認してみましょう！
  - ターミナル(CURL)　
  - Swagger
  - Web UI

6. docker-composeで起動しているコンテナを停止・削除する
```shell
docker-compose down
```

### (Option) docker-compose で SQL Server を起動して、ToDoアプリを完成させる！

Comming soon...
 

## ハンズオンの終了

ハンズオンは如何でしたでしょうか？

今後の改善に向けて、アンケートの記載をお願いします！
