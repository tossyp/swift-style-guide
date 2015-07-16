# Swift コーディング規約

規約を決めるにあたっての判断基準の重要度順

* 誤読の可能性が低いこと
* 意図が明白であること
* 簡潔に記述できること

各規約の強制力
Must以外はコードレビュー時に修正するかどうかの判断を各プロジェクトに委ねる

* 従うべき (Must, Must Not)
* 従うのが良い (Good, Bad)
* 従うのが好ましい (Preferred, Not Preferred)

## 参考
__wantedly__ http://qiita.com/susieyy/items/f71435cc962e70d81b37

__GitHub__ https://github.com/github/swift-style-guide

__RayWenderlich__ https://github.com/raywenderlich/swift-style-guide


## Declaration

可能な限りvar宣言よりもlet宣言を使う

右辺が値で初期化する場合は左辺に型を記述しない。
ただし型が曖昧な場合は明記する。

**Preferred:**

```swift
let text = "Hoge Fuga"
let flg = false
let frame = CGRectZero
let i: Int = 0
let p: Float = 3.14
```

**Not Preferred:**

```swift
let text: String = "Hoge Fuga"
let flg: Bool = false
let frame: CGRect = CGRectZero
let i = 0
let p = 3.14
```

右辺のクラスをNewすることで初期化する場合は型が自明なので左辺に型を記述しない。
クラスメソッドの戻り値の型がそのクラスの型で初期化する場合は型が類推できるので左辺に型を記述しない。
キャストして代入する場合は変数定義の型明記を省力する。

**Preferred:**

```
let image = UIImage(named: "Hoge")
let size = CGSize(10.0, 10.0)
let app = UIApplication.sharedApplication()
let text = data["text"] as String
```

**Not Preferred:**

```
let image: UIImage = UIImage(named: "Hoge")
let size: CGSize = CGRectZero
let app: UIApplication = UIApplication.sharedApplication()
let text: String = data["text"] as String
```

### Collection Types

**Preferred:**

```
var array = [Int]()

var dictionary = [Int: String]()
```

**Not Preferred:**

```
var array: [Int] = []
var array: [Int] = [Int]()
var array = Array<Int>()

var dictionary: [Int: String] = [Int: String]()
var dictionary = Dictionary<Int, String>()
```

### Global Variable

グローバルな変数は宣言しない。
グローバル定数はObjective-Cとの互換性のため許可する。

**Preferred:**

```
let kSomeNotificationName = "notification_name"

class SomeClass {
```

**NotPreferred:**

```
var someNotificationName = "notification_name"

class SomeClass {
```

## Naming
クラス名はUpperCamelCase (頭文字が大文字)
変数・定数はCamelCase (頭文字が小文字)
グローバル定数はkから始まるCamelCase

**Preferred:**

```swift
let kMaximumWidgetCount = 100

class WidgetContainer {
  let contentView = UIView(frame: CGRectMake(0, 0, 300, 300))
  var widgetButton: UIButton
  let widgetHeightPercentage = 0.85
}
```

**Not Preferred:**

```swift
let MAX_WIDGET_COUNT = 100

class app_widgetContainer {
  let cView = UIView(frame: CGRectMake(0, 0, 300, 300))
  var wBut: UIButton
  let wHeightPct = 0.85
}
```

For functions and init methods, prefer named parameters for all arguments unless the context is very clear. Include external parameter names if it makes function calls more readable.

```swift
func dateFromString(dateString: String) -> NSDate
func convertPointAt(#column: Int, #row: Int) -> CGPoint
func timedAction(#delay: NSTimeInterval, perform action: SKAction) -> SKAction!

// would be called like this:
dateFromString("2014-03-14")
convertPointAt(column: 42, row: 13)
timedAction(delay: 1.0, perform: someOtherAction)
```

## Spacing

* 無駄なスペースを減らして改行を避けるためにインデントは半角スペース2つ

  ![Xcode indent settings](screens/indentation.png)

* Tip: You can re-indent by selecting some code (or ⌘A to select all) and then Control-I (or Editor\Structure\Re-Indent in the menu). Some of the Xcode template code will have 4-space tabs hard coded, so this is a good way to fix that.

* ファイル終端は改行する
* コードをロジック毎に分割するために、空白行を惜しみなく使う
* 行末に空白を残さない
  * 空白行でのインデント調整もしない

## Optional
オプショナル型の開示指定(Unwrap)は避け、オプショナル束縛(Optional Binding)を使う。

**Bad:**

```
var foo = nil
println("foo is \(foo!)")
```

**Good:**

```
var foo = nil
if let foo = foo {
  println("foo is \(var)")
} else {
  // do something here when foo is nil.
}
```

可能な限り、暗黙的開示オプショナル型(Implictly Unwrapped Optional Typeの使用を避ける。文脈上、確実にnilでないことが保証されていない限り(クラッシュすることが即ちassert代わりになる場合にのみ使う)、強制開示を避ける。

**Good:**

```
var foo: FooType?
foo?.callSomethingIfFooIsNotNil()

@IBOutlet var barLabel: UILabel!
barLabel.text = "foobar"
```

**Bad:**

```
var foo: FooType!
foo.callSomethingIfFooIsNotNil()

var foo: FooType?
foo!.callSomethingIfFooIsNotNil()
```

## String

NSStringよりSwiftのStringを使う

**Preferred:**

```
var text: String?
("Swift text" as NSString).lowercaseString

```

**Not Preferred:**

```
var text: NSString?
"Swift text".bridgeToObjectiveC().lowercaseString
```

文字列のカウント、空文字チェック

**Preferred:**

```
"Swift text".count
if "Hoge".isEmpty { }
```

**Not Preferred:**

```
("Swift text" as NSString).length
if countElements("Swift text") == 0 { }
if ("Swift text" as NSString).length == 0 { }
```

文字列の比較は == 演算子

```
let flg: Bool = "hoge" == "fuga"
```

## 数値
整数型はInt, 浮動小数点型はFloat。但し、UIKit/CoreGraphicsではコンバージョンを減らすため、CGFloatを使っても良い

```
let i: Int = 5
let f: Float = 1.0
let c: CGFloat = 1.0
```

## 関数
戻り値がVoid型の場合、戻り値の型の記述を省略する。
第一引数は可読性を考慮して、必要であれば外部引数名を利用する。

**Preferred:**

```
func changeWorld(text: String) {  }

func dateFromString(dateString: String) -> NSDate
dateFromString("2014-03-14")

func convertPointAt(#column: Int, #row: Int) -> CGPoint
convertPointAt(column: 42, row: 13) 
```

**Not Preferred:**

```
func changeWorld(text: String) -> () { }
func changeWorld(text: String) -> Void { }

func dateFromString(#dateString: String) -> NSDate
dateFromString(dateString: "2014-03-14")

func convertPointAt(column: Int, #row: Int) -> CGPoint
convertPointAt(42, row: 13) // 第一引数がわかりにくい
```

第一引数の引数名を省略可能でかつデフォルト値を設定する場合は_を記述する。

```
func log(_ message: String = "default message") 

log("this is message") // -> this is message
log()                  // -> default message
```

## 制御構文

### if文
if に続く評価式は括弧（）で括らない。

**Good:**

```
if a == b { }
```

**Bad:**

```
if (a == b) { }
```

### for文, for-in文
ループ処理はfor-in文で記述する。
for文のキャストは配列の要素に対して個別に行わず、配列をキャストした上でループする。

**Good:**

```
for index in 0..<3 {
    println("index is \(index)")
}

for value in array {
    println("index is \(index)")
    println("value is \(value)")    
}

for view in self.view.subviews as [UIView] {

}
```

**Bad:**

```
for var index = 0; index < 3; ++index {
    println("index is \(index)")
}

for var index = 0; index < array.count; index++ {
    let value = array[index]
    println("value is \(value)")
}

for temp in self.view.subviews {
    let view = temp as UIView
}
```

### ブラケット

メソッドブラケットと制御構文のブラケット（if/else/switch/while etc.）は式と同じ行でオープンブラケットを記述する。

**Preferred:**

```swift
if user.isHappy {
  // Do something
} else {
  // Do something else
}

if !valid { return }
```

**Not Preferred:**

```swift
if user.isHappy
{
    // Do something
}
else {
    // Do something else
}
```

## Enumeration

caseがInt型で0からのシーケンスの場合は、enumの型とcaseの値は記述しない。各caseが値を取る場合はcaseごとに改行して記述する。
型が自明な変数に代入するenumの値はenum名を省略する。

**Preferred:**

```swift
enum CustomResult {
    case Success, Error
}

enum CustomResult: String {
    case Success = "success"
    case Error = "error" 
}

button.setTitle("title", forState: .Normal)
var state: UIControlState = .Normal

```

**Not Preferred:**

```swift
enum CustomResult: Int {
    case Success = 0, Error
}

button.setTitle("title", forState: UIControlState.Normal)
var state: UIControlState = UIControlState.Normal
```

グローバルな列挙型を定義するときは、クラススコープ、ストラクチャスコープで記述できないか検討する。

**Preferred:**

```swift
class NetworkManager {
    enum CustomResult: String {
        case Success = "success",
        case Error = "error" 
    }
    var customResult: CustomResult? 
}
```

**Not Preferred:**

```swift
enum CustomResult: String {
    case Success = "success",
    case Error = "error" 
}

class NetworkManager {
    var customResult: CustomResult? 
}
```

## クラス

読み取り専用のプロパティと添字付けでは暗黙的なgetterを優先する

**Preferred:**

```swift
var myGreatProperty: Int {
    return 4
}

subscript(index: Int) -> T {
    return objects[index]
}
```

**Not Preferred:**

```swift
var myGreatProperty: Int {
    get {
        return 4
    }
}

subscript(index: Int) -> T {
    get {
        return objects[index]
    }
}
```

selfの持つプロパティやメソッドへアクセスする時、デフォルトではselfへの参照は省きましょう。
明示的にselfを入れるのは言語によって必要と判断された場合だけにしましょう-たとえばクロージャ内、もしくはパラメータ名の衝突などです。

**Preferred:**

```swift
private class History {
    var events: [Event]

    func rewrite() {
        events = []
    }
}

extension History {
    init(events: [Event]) {
        self.events = events
    }

    var whenVictorious: () -> () {
        return {
            self.rewrite()
        }
    }
}
```

