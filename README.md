# store-doc-jp
[tucnak/store](https://github.com/tucnak/store)の日本語ドキュメント GPT-3が生成

## Load function

`Load`関数は、指定された設定名に基づいて構成ディレクトリから設定ファイルを読み込む関数です。もし設定ファイルが存在しない場合は、新しいオブジェクトを作成します。

以下はLoad関数の形式になります。

```go
Copy code
func Load(configName string, v interface{}) error {
    // ...
}
```
configNameは、設定ファイル名です。
vは、読み込んだ設定を格納するオブジェクトです。このオブジェクトは、構造体である必要があります。
Load関数は、設定ファイルを指定された構造体型に読み込みます。読み込みに失敗した場合は、エラーを返します。設定ファイルが存在しない場合は、新しいオブジェクトを作成して返します。
