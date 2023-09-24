# Cosmos DB for NoSQLの操作

[目次へ戻る](./readme.md)

## Cosmos DBアカウントの作成
   
- Azure PortalよりCosmos DBを検索

<img src="./assets/01_01.png" width="200">

- コア(SQL)を選択

<img src="./assets/01_02.png" width="400">

- リソースグループを選択(もしくは新規作成)し、アカウント名、リージョンを決定。容量モードはプロビジョニングスループットを設定。その他はデフォルト値で「レビュー＋作成」を押下

<img src="./assets/01_03.png" width="400">
<img src="./assets/01_04.png" width="400">

- 内容をレビューし、「作成」を押下

<img src="./assets/01_05.png" width="400">

## データベース・コンテナーの作成

### データベースの作成

- ポータルからCosmos DBアカウントを選択し、「データエクスプローラー」を開く  
<img src="./assets/01_06.png" width="200">

- データエクスプローラーより"New Container"の横の"v"をクリックし、"New Database"を押下  
<img src="./assets/01_07.png" width="400">

- 右側に現れた入力欄に以下の情報を入力  
<img src="./assets/01_08.png" width="400">

|項目名|意味|サンプル入力|
|---|---|---|
|Database Id|データベース名(必須)| db1 |
|Provision Throughput|プロビジョンスループットにするか| ☑ |
|Database Throughput (autoscale)|標準スループットかオートスケールか| Autoscale |
|Database MaxRU/s|最大RU/s|1,000|

### コンテナーの作成

- データエクスプローラーより"New Container"をクリック  
<img src="./assets/01_09.png" width="400">

- 右側に現れた入力欄に以下の情報を入力
<img src="./assets/01_10.png" width="400">

|項目名|意味|サンプル入力|
|---|---|---|
|Database Id|データベース名(必須)| Existingを選択し、db1 |
|Container Id|コンテナー名(必須)|container1|
|Partition Key|パーティションキー| /id |

その他はデフォルトでOK


## アイテムの作成・更新・削除

-データエクスプローラーでコンテナーを選んで、"New Item"を押下

<img src="./assets/01_11.png" width="400">

-出てきたウインドウにJSONフォーマットでデータを入力し、SAVE。(以下をコピー&ペーストし、任意に改変)
```JSON
{
   "id"     : "000001",
   "name"   : "John Smith",
   "sku"    : "20223",
   "price"  : 23.95,
   "shipTo" : {
      "name" : "Jane Smith",
      "address" : "123 Maple Street",
      "city" : "Pretendville",
      "state" : "NY",
      "zip"   : "12345"
   },
   "billTo" : {
      "name" : "John Smith",
      "address" : "123 Maple Street",
      "city" : "Pretendville",
      "state" : "NY",
      "zip"   : "12345"
   }
}
```

Save後、システムプロパティが付きリストに登録されることを確認する。

<img src="./assets/01_12.png" width="400">

**"id"の値と他の項目の値を変更しつつ**同様の操作を何回か繰り返し、5~10程度のドキュメントを保存する。

## クエリの発行

### サンプルデータの作成

下記2つのJSONアイテムを、前項の操作に従って作成する。

- サンプルデータその1

``` JSON 
{
  "id": "863e778d-21c9-4e2a-a984-d31f947c665c",
  "categoryName": "Surfboards",
  "name": "Teapo Surfboard (6'10\") Grape",
  "sku": "teapo-surfboard-72109",
  "price": 690.00,
  "manufacturer": {
    "name": "Taepo",
    "location": {
      "type": "Point",
      "coordinates": [ 
        34.15562788533047, -118.4633004882891
      ]
    }
  },
  "tags": [
    { "name": "Tail Shape: Swallow" },
    { "name": "Color Group: Purple" }
  ]
}
```

- サンプルデータその2

``` JSON 
{
  "id": "6e9f51c1-6b45-440f-af5a-2abc96cd083d",
  "categoryName": "Sleeping Bags",
  "name": "Vareno Sleeping Bag (6') Turmeric",
  "price": 120.00,
  "closeout": true,
  "manufacturer": {
    "name": "Vareno"
  },
  "tags": [
    { "name": "Color Group: Yellow" },
    { "name": "Bag Shape: Mummy" }
  ]
}
```


### クエリ操作

データエクスプローラー上のツールバーにある、"New SQL Query"を押下。

<img src="./assets/01_13.png" width="400">

右側ペインにコードを張り付けて"Execute Query"を押下

<img src="./assets/01_14.png" width="400">

クエリ
```SQL
select count(1) AS CNT from c
```

結果
```JSON
[
    {
        "CNT": 2
    }
]
```
※結果の件数はコンテナーのデータ件数によっては異なる。

### クエリパターン

#### 全件取得

クエリ
```SQL
SELECT * FROM c
```
結果
```JSON
[
    {
        "id": "863e778d-21c9-4e2a-a984-d31f947c665c",
        "categoryName": "Surfboards",
        "name": "Teapo Surfboard (6'10\") Grape",
        "sku": "teapo-surfboard-72109",
        "price": 690,
        "manufacturer": {
            "name": "Taepo",
            "location": {
                "type": "Point",
                "coordinates": [
                    34.15562788533047,
                    -118.4633004882891
                ]
            }
        },
        "tags": [
            {
                "name": "Tail Shape: Swallow"
            },
            {
                "name": "Color Group: Purple"
            }
        ],
        "_rid": "OsMpAKRdg3MBAAAAAAAAAA==",
        "_self": "dbs/OsMpAA==/colls/OsMpAKRdg3M=/docs/OsMpAKRdg3MBAAAAAAAAAA==/",
        "_etag": "\"c600c175-0000-0700-0000-64a4f7960000\"",
        "_attachments": "attachments/",
        "_ts": 1688532886
    },
    {
        "id": "6e9f51c1-6b45-440f-af5a-2abc96cd083d",
        "categoryName": "Sleeping Bags",
        "name": "Vareno Sleeping Bag (6') Turmeric",
        "price": 120,
        "closeout": true,
        "manufacturer": {
            "name": "Vareno"
        },
        "tags": [
            {
                "name": "Color Group: Yellow"
            },
            {
                "name": "Bag Shape: Mummy"
            }
        ],
        "_rid": "OsMpAKRdg3MCAAAAAAAAAA==",
        "_self": "dbs/OsMpAA==/colls/OsMpAKRdg3M=/docs/OsMpAKRdg3MCAAAAAAAAAA==/",
        "_etag": "\"c600c275-0000-0700-0000-64a4f7a50000\"",
        "_attachments": "attachments/",
        "_ts": 1688532901
    }
]
```

#### WHERE句で条件を設定

クエリ
```SQL
SELECT * FROM c
WHERE c.categoryName = "Sleeping Bags"
```
結果
```JSON
[
    {
        "id": "6e9f51c1-6b45-440f-af5a-2abc96cd083d",
        "categoryName": "Sleeping Bags",
        "name": "Vareno Sleeping Bag (6') Turmeric",
        "price": 120,
        "closeout": true,
        "manufacturer": {
            "name": "Vareno"
        },
        "tags": [
            {
                "name": "Color Group: Yellow"
            },
            {
                "name": "Bag Shape: Mummy"
            }
        ],
        "_rid": "OsMpAKRdg3MCAAAAAAAAAA==",
        "_self": "dbs/OsMpAA==/colls/OsMpAKRdg3M=/docs/OsMpAKRdg3MCAAAAAAAAAA==/",
        "_etag": "\"c600c275-0000-0700-0000-64a4f7a50000\"",
        "_attachments": "attachments/",
        "_ts": 1688532901
    }
]
```


#### JSONを他の形式に変換(プロジェクション) : productというオブジェクトを新しく生成

クエリ
```SQL
SELECT {
  "name": c.name,
  "sku": c.sku,
  "vendor": c.manufacturer.name
} AS product
FROM c 
WHERE c.sku = "teapo-surfboard-72109"
```
結果
```JSON
[
    {
        "product": {
            "name": "Teapo Surfboard (6'10\") Grape",
            "sku": "teapo-surfboard-72109",
            "vendor": "Taepo"
        }
    }
]
```

#### SELECT VALUEを使ったフラット化

クエリ
```SQL
SELECT VALUE {
  "name": c.name,
  "sku": c.sku,
  "vendor": c.manufacturer.name
}
FROM c
WHERE c.sku = "teapo-surfboard-72109"
```  
結果
```JSON
[
    {
        "name": "Teapo Surfboard (6'10\") Grape",
        "sku": "teapo-surfboard-72109",
        "vendor": "Taepo"
    }
]
```

## SDKを利用したデータの登録

### Node.jsの場合

- Azure Cloud Shellを起動する
- 以下のコマンドを実行してCosmosDBのアクセスに必要なライブラリをインストールする
```
npm install @azure/cosmos
```

- Cloud Shellのエディタを **{}アイコンから** 起動して以下のプログラムを入力する。ファイルは`cosmosdb_test.js`という名前で保存する。
   - `const endpoint =`からの4行にある接続に必要な情報は自身の環境の情報に置き換える。
   - 保存は`CTRL+S`、終了は`CTRL+Q`で行う。

```
// Cosmos DB Clientの作成
const { CosmosClient } = require("@azure/cosmos");

const endpoint = "<YOUR DB URL>";
const key = "<YOUR DB KEY>";
const databaseId = "<YOUR DATABSE>";
const containerId = "<YOUR CONTAINER>";

const client = new CosmosClient({ endpoint, key });

// データの登録
async function createItem(itemBody) {
    const { item } = await client
        .database(databaseId)
        .container(containerId)
        .items.create(itemBody);
    return item;
}

// データの更新
async function updateItem(itemId, itemBody) {
    const { item } = await client
        .database(databaseId)
        .container(containerId)
        .item(itemId)
        .replace(itemBody);
    return item;
}

// データのクエリ
async function queryItems(querySpec) {
    const { resources: items } = await client
        .database(databaseId)
        .container(containerId)
        .items.query(querySpec)
        .fetchAll();
    return items;
}

// データの削除
async function deleteItem(itemId,pk) {
    await client
        .database(databaseId)
        .container(containerId)
        .item(itemId,pk)
        .delete();
}

async function main() {
    const newItem = { id: "1", name: "Sample Item", description: "This is a sample item.",partitionKey:'pk' };

    newItem.id = (new Date()).getTime().toString();

    console.log("Creating item...");
    const createdItem = await createItem(newItem);
    console.log(`Created item with id ${createdItem.id}`);

    console.log("Updating item...");
    newItem.name = "Updated Sample Item";
    const updatedItem = await updateItem(newItem.id, newItem);
    console.log(`Updated item with id ${updatedItem.id}`);

    const querySpec = {
        query: "SELECT * FROM c WHERE c.name = @name",
        parameters: [
            {
                name: "@name",
                value: "Updated Sample Item"
            }
        ]
    };

    const queryItem = await queryItems(querySpec).then(items => {
        console.log(`Found ${items.length} item(s) where name = 'Updated Sample Item'`);
        items.forEach(item => {
            console.log(`  Item with id ${item.id}`);
        });
    });

    console.log("Deleting item..." + newItem.id);
    await deleteItem(newItem.id,newItem.partitionKey);
    console.log(`Deleted item with id ${newItem.id}`);
}

// main()でエラーが起こった場合
main().catch(error => {
    console.error("Error running sample:", error.message);
});

```

- ファイルを保存したら、`node cosmosdb_test.js`をCloud Shellに入力して実行する。
  
  ログが以下のように表示されれば成功。idは時間なので変化する。

```
Creating item...
Created item with id 1695566789021
Updating item...
Updated item with id 1695566789021
Found 1 item(s) where name = 'Updated Sample Item'
  Item with id 1695566789021
Deleting item...1695566789021
Deleted item with id 1695566789021
```

- (Option) プログラム内のパラメータやデータなどを書き換えて再度実行し、動作を確認する。

## 管理操作
   - スループットの変更
      - データエクスプローラーの**データベースの下**にある"Scale"を選択  
        <img src="./assets/01_15.png" width="400">  
      - Manual or Automaticを選択し、スループット(RU/s)を入力し、"Save"  
        <img src="./assets/01_16.png" width="400">
 
   - インデックスポリシーの変更
      - データエクスプローラーの**コンテナーの下**にある"Settings"を選択  
        <img src="./assets/01_17.png" width="400">  
      - 右ペインのタグ"Index Policy"を開く  
        <img src="./assets/01_18.png" width="400">

   - インデックスポリシーの内容を追記・変更して"Save"することで反映される。  
     インデックスの変更は使われていないRU/sを消費してバックグラウンドに実行される。

   ```JSON
   {
       "indexingMode": "consistent", ← "consistent" or "none"
       "automatic": true,  ← 自動で作成するか
       "includedPaths": [   ← インデックスを生成する項目・パス
           {
               "path": "/*"   ←「ルートからすべての項目」の意味
           }
       ],
       "excludedPaths": [   ← インデックスを生成しない項目・パス
           {
               "path": "/\"_etag\"/?"  ←"/etag"にはインデックスを作らない。\はエスケープ
           }
       ]
   }
   ```
