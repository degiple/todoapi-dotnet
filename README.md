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

さて、これからあなたには Todoアプリケーションのコンテナ化を目指して構築作業行なって頂きます。

まずはサンプルAPIの起動から始めましょう

### (1) プロジェクトの作成

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

### (2) サンプルの WeatherForecast API を試してみる

プロジェクトに最初から用意されているサンプルのAPIで、挙動を確認してみましょう！  
※コマンドを実行するディレクトリの位置に注意してください。

1. TodoApiプロジェクトの実行

```shell
cd /workspace/todoapi-dotnet && dotnet run --project TodoApi --urls http://+:8080
# または cd /workspace/todoapi-dotnet/TodoApi && dotnet run --urls http://+:8080
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

最後に、1つ目のターミナルで実行していたアプリケーションを Ctl + Cキー で強制終了しておきましょう。


### (3) ToDo API の実装

それではTodoアプリケーションを構築してきましょう！　　 
尚、本コンテンツでは事前にソースコードを準備しています。

1. モデルクラスの追加
```shell
cp -r /workspace/todoapi-dotnet/work/Models/ /workspace/todoapi-dotnet/TodoApi/Models/
ls /workspace/todoapi-dotnet/TodoApi/Models/
```

2. 依存関係を解決するように Program.cs を更新
```shell
cp /workspace/todoapi-dotnet/TodoApi/Program.cs /workspace/todoapi-dotnet/TodoApi/Program.cs.backup # バックアップ
cp /workspace/todoapi-dotnet/work/Program.cs /workspace/todoapi-dotnet/TodoApi/Program.cs
```

3. コントローラーの追加
 
```shell
cp /workspace/todoapi-dotnet/work/Contralloers/TodoItemsController.cs /workspace/todoapi-dotnet/work/Contralloers/TodoItemsController.cs
```

4. Webアプリケーションの実行

```shell
cd /workspace/todoapi-dotnet && dotnet run --project TodoApi --urls http://+:8080
# または cd /workspace/todoapi-dotnet/TodoApi && dotnet run --urls http://+:8080
```

5. ターミナルでの動作確認をしてみましょう

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

6. (Option) 前章までの内容を参考に、Swaggerでも動作確認をしてみましょう！


### (4) Web UI の追加

1. 静的ファイルの配置
```shell
cp -r /workspace/todoapi-dotnet/work/wwwroot /workspace/todoapi-dotnet/TodoApi/wwwroot/
```

2. Webアプリケーションの実行
```shell
cd /workspace/todoapi-dotnet && dotnet run --project TodoApi --urls http://+:8080
# または cd /workspace/todoapi-dotnet/TodoApi && dotnet run --urls http://+:8080
```

3. PORTSタグを開き、Open Browserをクリック
<img width="973" alt="image" src="https://user-images.githubusercontent.com/65447508/193523273-c632dc6e-35ca-47a8-a421-b521968722aa.png">

4. Web UI が表示されます
<img width="1155" alt="image" src="https://user-images.githubusercontent.com/65447508/193523504-5a97596e-cd6e-4241-bba3-ff42e866754d.png">


### (5) TodoAPIのコンテナ化

1. Dockerfileの配置
```shell
# Dockerfileの配置
cp /workspace/todoapi-dotnet/work/Dockerfile /workspace/todoapi-dotnet/Dockerfile

# コンテナビルド時に無視するファイル群を指定（本来はどちらかに配置）
cp /workspace/todoapi-dotnet/work/.dockerignore /workspace/todoapi-dotnet/.dockerignore
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

5. 前章までの内容を参考に動作確認してみましょう！
  - ターミナル(CURL)　
  - Swagger
  - Web UI

6. 起動しているコンテナを停止する
```shell
docker kill todoapi-sample
docker ps 
```

7. 作成済のコンテナを削除する
```shell
docker rm todoapi-sample
docker container ls -a
```

### (6) docker-composeによる起動

docker-composeは、複数のDockerコンテナを定義し実行するDockerアプリケーションのためのツールです。
  
今回は単一のWebアプリコンテナの起動となりますが、いずれDB等と同時起動

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

・・・
 

## ハンズオンの終了

ハンズオンは如何でしたでしょうか？

今後の改善に向けて、アンケートの記載をお願いします！
