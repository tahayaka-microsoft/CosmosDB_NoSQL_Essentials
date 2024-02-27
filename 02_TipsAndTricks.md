# CosmosDB Tips & Tricks集

## データのエクスポート、インポートに`dmt`を活用

### `dmt`とは？
- CosmosDBのデータマイグレーションツール
- 以下の形式のデータの入出力に対応
  - Azure Cosmos DB
  - Azure Table API
  - JSON
  - MongoDB
  - SQL Server
  - Parquet
  - CSV
  - File Storage
  - Azure Blob Storage
  - AWS S3
  - Azure Cognitive Search

- オープンソースにて提供(作成はMicrosoft)

### ダウンロード

- [こちらから](https://github.com/azurecosmosdb/data-migration-desktop-tool/releases)対象のOSのバイナリをダウンロードし、適当なフォルダに解凍する

### セットアップ

> **用語**  
> ソース(Source): データ転送元  
> シンク(Sink)  : データ転送先  

- `migrationsetting.json`を編集する。以下はソースをCosmosDB、シンクをJSONファイルとする設定

```JSON
{
  "Source": "cosmos-nosql",
  "Sink": "json",
  "SourceSettings": {    
	"ConnectionString":"<COSMOS DB接続文字列>",
	"Database":"<データベース名>",
	"Container":"<コンテナー名>",
	"PartitionKeyPath":"<パーティションキーのパス('/'付)>"

  },
  "SinkSettings": {    
	"FilePath":"<出力ファイルのパス>"
  }
}
```

### 実行

- dmt.exeのあるフォルダにて以下コマンドにて実行する。同じフォルダにある`migrationsetting.json`が参照される。
```
./dmt
```
正常時の出力は以下の通り
```
info: Cosmos.DataTransfer.Core.RunCommand.CommandHandler[0]
      Settings loading from file at configured path 'X:\xx\xx\migrationsettings.json'.
info: Cosmos.DataTransfer.Core.ExtensionLoader[0]
      Loading extensions from X:xx\xx\Extensions
info: Cosmos.DataTransfer.Core.ExtensionLoader[0]
      14 Extensions Loaded for type IDataSourceExtension
info: Cosmos.DataTransfer.Core.ExtensionLoader[0]
      14 Extensions Loaded for type IDataSinkExtension
Using Cosmos-nosql Source
Using JSON Sink
info: CosmosDataSourceExtension[0]
      Reading from db1.container1
info: Cosmos.DataTransfer.Core.RunCommand.CommandHandler[0]
      Data transfer complete
```

### (おまけ) コマンドラインオプション

```
PS D:\temp\dmt\dmt-2.1.4-win-x64\windows-package> ./dmt --help
Description:
  Azure data migration tool

Usage:
  dmt [command] [options] [[--] <additional arguments>...]]

Options:
  -from, --source <source>                     The extension to read data.
  -to, --destination, --sink, --target <sink>  The extension to write data.
  --settings <settings>                        The settings file. (default: migrationsettings.json)
  -?, -h, --help                               Show help and usage information
  --version                                    Show version information

Commands:
  <default>, run  Runs data transfer operation using selected source and sink
  list            Loads and lists all available extensions
  init            Creates template settings file for use as input
  settings        Loads and lists extension settings structure

Additional Arguments:
  Extension specific settings can be provided as additional arguments in the form:
  --<Source|Sink>Settings:<name> <value>  ex: --SourceSettings:FilePath MyDataFile.json
```
