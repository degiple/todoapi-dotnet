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
# または cd TodoApi && dotnet run --urls http://+:8080
```

ターミナルでの動作確認は以下の通りです

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

WEbアプリケーション実行後には、Swaggerで動作確認も可能です！


### (4) Web UI の追加

1. 静的ファイルの配置
```shell
cp -r /workspace/todoapi-dotnet/work/wwwroot /workspace/todoapi-dotnet/TodoApi/wwwroot/
```

2. Webアプリケーションの実行

```shell
cd /workspace/todoapi-dotnet && dotnet run --project TodoApi --urls http://+:8080
# または cd TodoApi && dotnet run --urls http://+:8080
```

3. PORTSタグを開き、Open Browserをクリック

<img width="973" alt="image" src="https://user-images.githubusercontent.com/65447508/193523273-c632dc6e-35ca-47a8-a421-b521968722aa.png">

4. Web UI が表示されます
<img width="1155" alt="image" src="https://user-images.githubusercontent.com/65447508/193523504-5a97596e-cd6e-4241-bba3-ff42e866754d.png">



### (5) Dockerfileの作成

```shell
# コンテナイメージの作成（ビルド）
docker build .
```

### (6) docker-compose.yml の 作成

```shell
# コンテナ起動
docker-compose up
```

### (参考) 拡張機能によるDockerfileの自動作成

...

