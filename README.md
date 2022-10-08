# todoapi-dotnet

ASP.NET core と Docker を使って、コンテナ開発を学ぶためのコンテンツです。

## 前提・制約

- ASP.NET の詳しい説明はありません
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

さて、これからあなたには Todo アプリケーションのコンテナ化を目指して構築作業を行なって頂きます。

### (1) プロジェクトの作成

まずは テンプレートから ASP.NET プロジェクト`TodoApi`を作成しましょう

```shell
# このコマンドを実行
dotnet new webapi -o TodoApi
```

次に、インメモリ用の EFCore をインストールしておきます。  
EFCore により、Web アプリ側のみで一時的な DB をメモリ上に構成することができます。

```shell
cd /workspace/todoapi-dotnet/TodoApi
dotnet add package Microsoft.EntityFrameworkCore.InMemory
```

### (2) サンプルの WeatherForecast API を試してみる

プロジェクトに最初から用意されているサンプルの API で、挙動を確認してみましょう！  
※コマンドを実行するディレクトリの位置に注意してください。

1. TodoApi プロジェクトの実行

```shell
# このコマンドを実行
cd /workspace/todoapi-dotnet
dotnet run --project TodoApi --urls http://+:8080
```

```log
# 正常に実行できた場合のログ(例)
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

2. 別のターミナルを立ち上げる

![image](https://user-images.githubusercontent.com/65447508/193514013-215a52ea-14b5-40b7-8049-640277b24e0d.png)

3. WeatherForecast API を curl コマンドで確認

```shell
# このコマンドを実行
curl http://localhost:8080/weatherforecast | jq
```

<img width="955" alt="image" src="https://user-images.githubusercontent.com/65447508/193513739-ad7e889e-4d3d-4730-8464-22b41bef133a.png">

#### 今度は Swagger UI を使って、動作確認してみましょう！

Swagger とは…
- https://cloud.google.com/endpoints/docs/openapi/openapi-overview?hl=ja を用いて Restful APIを記述する際に用いられるオープンソースのツールセットのことです
- Swagger UI により、各APIを視覚化して操作・テスト等をすることができます
- https://swagger.io/

1. PORTS タグを開き、URL をコピーする

![image](https://user-images.githubusercontent.com/65447508/193514338-bd696fdf-423c-48e9-a81c-342afe8ecbde.png)

2. ブラウザで新しくタブを開き、以下のように URL を入力する

```
＜コピーしたURL＞/swagger

# 例： https://8080-degiple-todoapidotnet-lv9mmfnsijs.ws-us69.gitpod.io/swagger
```

3. /WeatherForecast をクリック

4. Try in out をクリック

5. Exexute をクリック

もし上手くいけば以下のような画面が表示されます！
<img width="1262" alt="image" src="https://user-images.githubusercontent.com/65447508/193514920-fa60983b-08b3-4431-96b5-885462ed1919.png">

最後に、1 つ目のターミナルで実行していたアプリケーションを Ctl + C キー で強制終了しておきましょう。

### (3) ToDo API の実装

それでは Todo アプリケーションを構築してきましょう！　　
尚、本コンテンツでは事前にソースコードを準備しています。

1. モデルクラスの追加

```shell
# このコマンドを実行
cp -r /workspace/todoapi-dotnet/work/Models/ /workspace/todoapi-dotnet/TodoApi/Models/
ls /workspace/todoapi-dotnet/TodoApi/Models/
```

```log
# 正常に実行できた場合のログ(例)
TodoContext.cs  TodoItems.cs
```

2. 依存関係を解決するように Program.cs を更新

```shell
# このコマンドを実行

## バックアップ
cp /workspace/todoapi-dotnet/TodoApi/Program.cs /workspace/todoapi-dotnet/TodoApi/Program.cs.backup

cp /workspace/todoapi-dotnet/work/Program.cs /workspace/todoapi-dotnet/TodoApi/Program.cs
ls /workspace/todoapi-dotnet/TodoApi/Program.cs
```

```log
# 正常に実行できた場合のログ(例)
/workspace/todoapi-dotnet/TodoApi/Program.cs
```

3. コントローラーの追加

```shell
# このコマンドを実行
cp /workspace/todoapi-dotnet/work/Contralloers/TodoItemsController.cs /workspace/todoapi-dotnet/TodoApi/Controllers/TodoItemsController.cs
ls /workspace/todoapi-dotnet/TodoApi/Controllers/
```

```log
# 正常に実行できた場合のログ(例)
TodoItemsController.cs  WeatherForecastController.cs
```

4. Web アプリケーションの実行

```shell
# このコマンドを実行
cd /workspace/todoapi-dotnet
dotnet run --project TodoApi --urls http://+:8080
```

5. ターミナルでの動作確認をしてみましょう

Todo の登録（1 つ目）

```shell
# このコマンドを実行
curl http://localhost:8080/api/todoitems -XPOST \
-H 'Content-Type: application/json' \
-d '{"name": "my first item"}' | jq .
```

```log
# 正常に実行できた場合のログ(例)
{
  "id": 1,
  "name": "my first item",
  "isComplete": false
}
```

Todo の登録（２つ目）

```shell
# このコマンドを実行
curl http://localhost:8080/api/todoitems -XPOST \
-H 'Content-Type: application/json' \
-d '{"name": "my second item"}' | jq .
```

```log
# 正常に実行できた場合のログ(例)
{
  "id": 2,
  "name": "my second item",
  "isComplete": false
}
```

登録した ToDo の一覧を取得

```shell
# このコマンドを実行
curl http://localhost:8080/api/todoitems | jq .
```

```log
# 正常に実行できた場合のログ(例)
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

登録した 1 つ目の ToDo を取得

```shell
# このコマンドを実行
curl http://localhost:8080/api/todoitems/1 | jq .
```

```log
# 正常に実行できた場合のログ(例)
{
  "id": 1,
  "name": "my first item",
  "isComplete": false
}
```

1 つ目の ToDo を更新

```shell
# このコマンドを実行

## 更新
curl http://localhost:8080/api/todoitems/1 -XPUT \
-H 'Content-Type: application/json' \
-d '{"id": 1, "name": "ハンズオンを完了させる！", "isComplete": false}'
## 更新されたか確認
curl http://localhost:8080/api/todoitems/1 | jq .
```

```log
# 正常に実行できた場合のログ(例)
{
  "id": 1,
  "name": "ハンズオンを完了させる！",
  "isComplete": false
}
```

1 つ目の ToDo を削除

```shell
# このコマンドを実行

## 削除
curl http://localhost:8080/api/todoitems/1 -XDELETE
## 削除されたか確認
curl http://localhost:8080/api/todoitems/1 | jq .
```

```log
# 正常に実行できた場合のログ(例)
{
  "type": "https://tools.ietf.org/html/rfc7231#section-6.5.4",
  "title": "Not Found",
  "status": 404,
  "traceId": "00-175af459cd4e48af097f8e6722204cbe-8b559381bf4caeac-00"
}
```

6. (Option) 前章までの内容を参考に、Swagger でも動作確認をしてみましょう！

### (Option) Web 画面の追加

ToDo を Web 画面で操作したくなりませんか？

シンプルな画面コードを用意しましたので、配置して試してみましょう！

1. 静的ファイル(with Vue.js)の配置

```shell
# このコマンドを実行
cp -r /workspace/todoapi-dotnet/work/wwwroot /workspace/todoapi-dotnet/TodoApi/wwwroot/
ls  /workspace/todoapi-dotnet/TodoApi/wwwroot/
```

```log
# 正常に実行できた場合のログ(例)
index.html  js
```

2. Web アプリケーションの実行

```shell
# このコマンドを実行
cd /workspace/todoapi-dotnet
dotnet run --project TodoApi --urls http://+:8080
```

3. PORTS タグを開き、Open Browser をクリック
   <img width="973" alt="image" src="https://user-images.githubusercontent.com/65447508/193523273-c632dc6e-35ca-47a8-a421-b521968722aa.png">

4. Web UI が表示されます
   <img width="1155" alt="image" src="https://user-images.githubusercontent.com/65447508/193523504-5a97596e-cd6e-4241-bba3-ff42e866754d.png">

### (4) TodoAPI のコンテナ化

いよいよコンテナ化です…！

1. Dockerfile の配置

```shell
# このコマンドを実行

## Dockerfileの配置
cp /workspace/todoapi-dotnet/work/Dockerfile /workspace/todoapi-dotnet/TodoApi/Dockerfile
## コンテナビルド時に無視するファイル群を指定
cp /workspace/todoapi-dotnet/work/.dockerignore /workspace/todoapi-dotnet/.dockerignore

```

2. コンテナイメージを todoapi というタグ名で作成

```shell
# このコマンドを実行
cd /workspace/todoapi-dotnet
docker build --file ./TodoApi/Dockerfile --tag todoapi:latest .
```

```log
# 正常に実行できた場合のログ(例)
Sending build context to Docker daemon  140.5MB
Step 1/19 : FROM mcr.microsoft.com/dotnet/aspnet:6.0-focal AS base
 ---> 434b27adeba8
Step 2/19 : WORKDIR /app
 ---> Using cache
 ---> 065f423eb69f
Step 3/19 : EXPOSE 8080
 ---> Using cache
 ---> 90a1e0c92a92
Step 4/19 : ENV ASPNETCORE_URLS=http://+:8080
 ---> Using cache
 ---> eab2e8750b5d
Step 5/19 : RUN adduser -u 5678 --disabled-password --gecos "" appuser && chown -R appuser /app
 ---> Using cache
 ---> 5ed5b5b4081c
Step 6/19 : USER appuser
 ---> Using cache
 ---> 363a8c13a8c8
Step 7/19 : FROM mcr.microsoft.com/dotnet/sdk:6.0-focal AS build
 ---> 7327e78eeed9
Step 8/19 : WORKDIR /src
 ---> Using cache
 ---> 793000c10b43
Step 9/19 : COPY ["TodoApi/TodoApi.csproj", "TodoApi/"]
 ---> Using cache
 ---> 38e62dc00094
Step 10/19 : RUN dotnet restore "TodoApi/TodoApi.csproj"
 ---> Using cache
 ---> ab56bd9fffbd
Step 11/19 : COPY . .
 ---> 497423dd400f
Step 12/19 : WORKDIR "/src/TodoApi"
 ---> Running in fb6b682faec5
Removing intermediate container fb6b682faec5
 ---> 7fe26371a597
Step 13/19 : RUN dotnet build "TodoApi.csproj" -c Release -o /app/build
 ---> Running in 70bd17f6f2e0
MSBuild version 17.3.1+2badb37d1 for .NET
  Determining projects to restore...
  Restored /src/TodoApi/TodoApi.csproj (in 656 ms).
  TodoApi -> /app/build/TodoApi.dll

Build succeeded.
    0 Warning(s)
    0 Error(s)

Time Elapsed 00:00:05.76
Removing intermediate container 70bd17f6f2e0
 ---> 9973f08415d7
Step 14/19 : FROM build AS publish
 ---> 9973f08415d7
Step 15/19 : RUN dotnet publish "TodoApi.csproj" -c Release -o /app/publish /p:UseAppHost=false
 ---> Running in 857d135b56e9
MSBuild version 17.3.1+2badb37d1 for .NET
  Determining projects to restore...
  All projects are up-to-date for restore.
  TodoApi -> /src/TodoApi/bin/Release/net6.0/TodoApi.dll
  TodoApi -> /app/publish/
Removing intermediate container 857d135b56e9
 ---> f78612cb76b1
Step 16/19 : FROM base AS final
 ---> 363a8c13a8c8
Step 17/19 : WORKDIR /app
 ---> Using cache
 ---> e116383038c7
Step 18/19 : COPY --from=publish /app/publish .
 ---> 0ddfecede353
Step 19/19 : ENTRYPOINT ["dotnet", "TodoApi.dll"]
 ---> Running in 8dd014aeb503
Removing intermediate container 8dd014aeb503
 ---> 8c9386e01348
Successfully built 8c9386e01348
Successfully tagged todoapi:latest
```

3. 作成したコンテナイメージで、コンテナの作成・起動（バックグラウンド）

```shell
# このコマンドを実行
docker run -d --name todoapi-sample todoapi:latest
```

```log
# 正常に実行できた場合のログ(例)
53ac1ebe2143f7007e6872116f6443a0845600c879bfb5f9dbb0056233feb839
```

4. 正常に作成・起動しているか確認

作成されているコンテナの確認

```shell
# このコマンドを実行
docker container ls -a
```

```log
# 正常に実行できた場合のログ(例)
CONTAINER ID   IMAGE            COMMAND                CREATED         STATUS         PORTS      NAMES
53ac1ebe2143   todoapi:latest   "dotnet TodoApi.dll"   9 seconds ago   Up 8 seconds   8080/tcp   todoapi-sample
```

起動しているコンテナの確認

```shell
# このコマンドを実行
docker ps
```

```log
# 正常に実行できた場合のログ(例)
gitpod /workspace/todoapi-dotnet (handson) $ docker ps
CONTAINER ID   IMAGE            COMMAND                CREATED          STATUS          PORTS      NAMES
53ac1ebe2143   todoapi:latest   "dotnet TodoApi.dll"   15 seconds ago   Up 13 seconds   8080/tcp   todoapi-sample
```

起動しているコンテナのログを確認

```shell
# このコマンドを実行
docker logs todoapi-sample
## または docker logs -f todoapi-sample
```

```log
# 正常に実行できた場合のログ(例)
gitpod /workspace/todoapi-dotnet (handson) $ docker logs todoapi-sample
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

コンテナの停止

```shell
# このコマンドを実行
docker stop todoapi-sample
docker ps
```

```log
# 正常に実行できた場合のログ(例)
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
```

停止したコンテナの確認

```shell
# このコマンドを実行
docker container ls -a
```

```log
# 正常に実行できた場合のログ(例)
CONTAINER ID   IMAGE                   COMMAND                CREATED              STATUS                      PORTS     NAMES
5128c98d200e   todoapi:latest          "dotnet TodoApi.dll"   About a minute ago   Exited (0) 28 seconds ago             todoapi-sample
```

7. 作成済のコンテナを削除する

```shell
# このコマンドを実行
docker rm todoapi-sample
docker container ls -a
```

```log
# 正常に実行できた場合のログ(例)
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
```

### (5) docker-compose による起動

docker-compose は、複数の Docker コンテナを定義し実行する Docker アプリケーションのためのツールです。

まだ単一の Web アプリコンテナだけですが、いずれ DB コンテナ等と同時起動をしてみたいですよね!?
という訳で、docker-compose でのコンテナ起動もやってみましょう！

1. docker-compose.yaml の配置

```shell
# このコマンドを実行
cp /workspace/todoapi-dotnet/work/docker-compose.yml /workspace/todoapi-dotnet/
ls /workspace/todoapi-dotnet/docker-compose.yml
```

```log
# 正常に実行できた場合のログ(例)
/workspace/todoapi-dotnet/docker-compose.yml
```

2. docker-compose によるコンテナ起動（バックグラウンド）

```shell
# このコマンドを実行
docker-compose up -d
```

```log
# 正常に実行できた場合のログ(例)
[+] Running 1/1
 ⠿ Container todoapi-dotnet-todoapi-1  Started
```

3. docker-compose で正常に起動しているか確認

```shell
# このコマンドを実行
docker-compose ps
```

```log
# 正常に実行できた場合のログ(例)
NAME                       COMMAND                SERVICE             STATUS              PORTS
todoapi-dotnet-todoapi-1   "dotnet TodoApi.dll"   todoapi             running             0.0.0.0:8080->8080/tcp, :::8080->8080/tcp
```

4. 起動しているコンテナのログを確認

```shell
docker-compose logs todoapi
# または docker-compose logs -f todoapi
```

```log
# 正常に実行できた場合のログ(例)
todoapi-dotnet-todoapi-1  | info: Microsoft.Hosting.Lifetime[14]
todoapi-dotnet-todoapi-1  |       Now listening on: http://[::]:8080
todoapi-dotnet-todoapi-1  | info: Microsoft.Hosting.Lifetime[0]
todoapi-dotnet-todoapi-1  |       Application started. Press Ctrl+C to shut down.
todoapi-dotnet-todoapi-1  | info: Microsoft.Hosting.Lifetime[0]
todoapi-dotnet-todoapi-1  |       Hosting environment: Production
todoapi-dotnet-todoapi-1  | info: Microsoft.Hosting.Lifetime[0]
todoapi-dotnet-todoapi-1  |       Content root path: /app/
```

4. 前章までの内容を参考に動作確認してみましょう！

- ターミナル(CURL)
- Swagger
- Web UI

6. docker-compose で起動しているコンテナを停止・削除する

```shell
# このコマンドを実行
docker-compose down
```

```log
# 正常に実行できた場合のログ(例)
[+] Running 2/2
 ⠿ Container todoapi-dotnet-todoapi-1  Removed
 ⠿ Network todoapi-dotnet_default      Removed
```

### (Option) docker-compose で SQL Server を起動して、ToDo アプリを完成させる！

Comming soon...

## ハンズオンの終了

ハンズオンは如何でしたでしょうか？

今後の改善に向けて、アンケートの記載をお願いします！
https://forms.gle/JhTtZ6zahVxZgtNW7