# todoapi-dotnet

アプリケーションの実行

```shell
# アプリケーション実行
dotnet run -p ToDoAPI/ --urls http://+:8080
# または cd ToDoAPI && dotnet run --urls http://+:8080

# コンテナ起動
# docker-compose up
```

Todo API の動作確認

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