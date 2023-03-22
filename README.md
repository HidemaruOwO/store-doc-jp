# store-doc-jp
[tucnak/store](https://github.com/tucnak/store)の日本語ドキュメント

## Init function
```go
func Init(application string) {
	// create file .config/$application
}
```
このコードは、構成データを保存するディレクトリ名を設定するための関数です。
この関数を呼び出すことで、アプリケーションの名前を引数として渡すことができます。
この名前は、構成データが保存されるディレクトリ名として使用されます。
デフォルトでは、StoreはLinuxシステムの場合は$ XDG_CONFIG_HOMEまたは$ HOME、Windowsの場合は% APPDATA%にすべての構成データを配置します。
ただし、この関数を呼び出す前に、Storeは機密情報を扱う呼び出しでパニックすることがあるため、注意が必要です。

## Load function

`Load`関数は、指定された設定名に基づいて構成ディレクトリから設定ファイルを読み込む関数です。もし設定ファイルが存在しない場合は、新しいオブジェクトを作成します。

以下はLoad関数の形式になります。

```go
func Load(configName string, v interface{}) error {
    // ...
}
```
configNameは、設定ファイル名です。
vは、読み込んだ設定を格納するオブジェクトです。このオブジェクトは、構造体である必要があります。
Load関数は、設定ファイルを指定された構造体型に読み込みます。読み込みに失敗した場合は、エラーを返します。設定ファイルが存在しない場合は、新しいオブジェクトを作成して返します。

## Save function
```go
func Save(path string, v interface{}) error {}
```
`Save`関数は、指定されたファイルパスに対して設定を保存するために使用されます。
保存する設定は、インターフェイス型の引数`v`として渡されます。ファイルパスの拡張子に応じて、適切なフォーマットで保存されます。

この関数は、以下のエラーを返す場合があります。

`store: application name not defined` : アプリケーション名が定義されていない場合に発生します。

`store: unknown configuration format!` : 対応するフォーマットが定義されていない場合に発生します。
例えば、以下のように使用できます。

```go
err := store.Save("config.json", configData)
if err != nil {
    // エラー処理
}
```

## Registar function
この例では、`configData`という名前の設定データを`config.json`というファイルに保存しています。

```go
func Register(extension string, m MarshalFunc, um UnmarshalFunc) {
//...
}
```
この関数は、`extension`というファイル名の拡張子に対応するマーシャルとアンマーシャルの関数を登録するために使用されます。一度登録されたフォーマットは、`Load`関数と`Save`関数と互換性があります。

例えば、`json`拡張子に対するマーシャルとアンマーシャル関数を登録する場合は、以下のようになります。

```go
store.Register("json", json.Marshal, json.Unmarshal)
```
これにより、`Load`関数や`Save`関数でjsonフォーマットが指定された場合に、`json.Marshal`関数と`json.Unmarshal関`数が使用されるようになります。

## LoadWith function
```go
func LoadWith(path string, v interface{}, um UnmarshalFunc) error {
//...
}
```
この関数は、引数で指定されたパスにある設定ファイルを読み込み、指定されたオブジェクトに設定をマーシャルします。`um`引数は、設定をアンマーシャルするために使用する関数を指定します。

関数の最初の部分では、設定ファイルが存在しない場合の処理が行われます。この場合、`Save`関数を使用して新しい設定ファイルを作成します。新しい設定ファイルには、指定されたオブジェクトの型と同じ型の空のオブジェクトが含まれます。その後、新しいオブジェクトに対して`v`変数を更新し、エラーを返す代わりにnilを返します。

ファイルが存在する場合、指定された設定ファイルを読み込み、`um`関数を使用してオブジェクトに設定をアンマーシャルします。アンマーシャルに失敗した場合、エラーを返します。正常に読み込んだ場合は、何も返さずに関数を終了します。
## SaveWith
`SaveWith`関数は、指定されたファイルパスに設定を保存するために使用されます。保存する設定は、`interface{}`型の引数`v`として渡され、任意の`Unmarshalerで`アンマーシャリング可能である必要があります。また、保存するファイルの形式を指定するために、任意の`MarshalFunc`関数も渡すことができます。

```go
func SaveWith(path string, v interface{}, mf MarshalFunc) error {
	// ...
}
```
例えば、次のように使用できます。

```go
err := store.SaveWith("config.json", configData, json.Marshal)
if err != nil {
    // エラー処理
}
```
この例では、`configData`という名前の設定データを`config.json`というファイルにJSON形式で保存しています。`json.Marshal`は、Goの標準ライブラリで提供されているJSONマーシャリング関数です。

## SetApplicationName

`SetApplicationName`関数は、アプリケーション名を設定するために使用される関数です。

以下は`SetApplicationName`関数の形式になります。

```go
func SetApplicationName(name string) {
	applicationName = name
}
```
`name`引数は、設定するアプリケーション名を指定する文字列です。

`SetApplicationName`関数は、引数に渡されたアプリケーション名をグローバル変数`applicationName`に設定します。この変数は、`Load`関数や`Save`関数など、他の関数で使用されます。

`SetApplicationName`関数は、通常、アプリケーションの起動時に呼び出されます。アプリケーション名は、設定ファイル名を作成するために使用されます。たとえば、アプリケーション名が`myapp`である場合、設定ファイルのデフォルト名は`myapp.config`になります。

以下は、`SetApplicationName`関数の使用例です。

```go
store.SetApplicationName("myapp")
```
この例では、アプリケーション名をmyappに設定しています。`Load`関数や`Save`関数は、このアプリケーション名を使用して設定ファイル名を作成します。
