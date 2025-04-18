# swift-tips

## Article
* [String][1]

## Code

### Dump data
```swift
// code - for class or data struct
struct A {
  let a: Int
}

let a = A()
dump(a)

// code
print(String(data: data!, encoding: .utf8))

// command
p String(data: data!, encoding: .utf8)
```

### Save & Load
```swift
// save
    let data = try! JSONEncoder().encode(self.store.value)
    let documentsPath = NSSearchPathForDirectoriesInDomains(
      .documentDirectory, .userDomainMask, true
      )[0]
    let documentsUrl = URL(fileURLWithPath: documentsPath)
    let favoritePrimesUrl = documentsUrl
      .appendingPathComponent("favorite-primes.json")
    try! data.write(to: favoritePrimesUrl)
    
// load
  let documentsPath = NSSearchPathForDirectoriesInDomains(
    .documentDirectory, .userDomainMask, true
    )[0]
  let documentsUrl = URL(fileURLWithPath: documentsPath)
  let favoritePrimesUrl = documentsUrl
    .appendingPathComponent("favorite-primes.json")
  guard
    let data = try? Data(contentsOf: favoritePrimesUrl),
    let favoritePrimes = try? JSONDecoder().decode([Int].self, from: data)
    else { return }
```

### Enum
```swift
public protocol CurrencyType {
    /// The three letter ISO 4217 currency code.
    static var code: String { get }

    /// The name of the currency.
    static var name: String { get }

    static var minorUnit: Int { get }
}

/// UAE Dirham (AED)
public enum AED: CurrencyType {
    public static var code: String {
        return "AED"
    }

    public static var name: String {
        return "UAE Dirham"
    }

    public static var minorUnit: Int {
        return 2
    }
}

let currencyType: CurrencyType.Type = AED.self
print(currencyType.code)
```

### Codeable
```swift

let json = """
[
    {
        "name": {
            "first_name": "Taylor",
            "last_name": "Swift"
        },
        "zip_code": "94538",
	"street": "xxx",
    }
]
"""

struct Address : Codable {
    var street: String
    var zip: String
    var firstName: String
    var lastName: String

    private enum CodingKeys : String, CodingKey {
        case street, zip = "zip_code", name
    }
    
    private enum NameCodingKeys: String, CodingKey {
    	case firstName, lastName
    }
    
    init(from decoder: Decoder) throws {
    	var container = decoder.container(keyedBy: CodingKeys.self)
	try container.decoder(street, forKey: .street)
	try container.decoder(zip, forKey: .zip)

        var name = container.nestedContainer(keyedBy: NameCodingKeys.self, forKey: .name)
        try name.encode(firstName, forKey: .firstName)
        try name.encode(lastName, forKey: .lastName)
    }
}


```

### Decode

```swift
// Age could be `bool`, `int`, `double`, `array` etc
let testInputs: [String] = [
    """
    {"age":10}
    """,
    """
    { "age":10.5 }
    """,
    """
    { "age":"Ten" }
    """,
    """
    { "age":false }
    """,
    """
    { "age":[10, 10.5, "Ten"] }
    """,
    """
    [{
      "info":{"first":"Alex", "nicknames":["Chase"]},
      "age":[10, 10.5, "Ten"]
    }]
    """
]

enum Age: Decodable {
  case int(Int)
  case double(Double)
  case string(String)
  case bool(Bool)
  case array([Age])

  init(from decoder: Decoder) throws {
    let container = try decoder.singleValueContainer()

    if let intValue = try? container.decode(Int.self) {
        self = .int(intValue)
    } else if let boolValue = try? container.decode(Bool.self) {
        self = .bool(boolValue)
    } else if let doubleValue = try? container.decode(Double.self) {
        self = .double(doubleValue)
    } else if let stringValue = try? container.decode(String.self) {
        self = .string(stringValue)
    } else if let arrayValue = try? container.decode([Age].self) {
       self = .array(arrayValue)
    } else {
      self = .int(0)
    }
  }
}

struct Info: Decodable {
  let first: String
  let nicknames: [String]
}

struct SwiftType: Decodable {
  let age: Age
  let info: Info?

  enum CodingKeys: String, CodingKey {
    case age
    case info
  }

  init(from decoder: Decoder) throws {
    let container = try decoder.container(keyedBy: CodingKeys.self)

    age = try container.decode(Age.self, forKey: .age)
    do {
      info = try container.decode(Info.self, forKey: .info)
    } catch {
      info = nil
    }
  }
}

for test in testInputs {
  let data = test
      .data(using: .utf8)!

  do {
      let decoded = try JSONDecoder().decode(SwiftType.self, from: data)
      print(decoded)
  } catch {
      let decodedArray = try JSONDecoder().decode([SwiftType].self, from: data)
      print(decodedArray)
  }
}
```

### UI Performace

```swift
// App Delegate DidFinishLaunching
let link = CADisplayLink(target: self, selector: #selector(AppDelegate.update(link:)))
link.add(to: RunLoop.main, forMode: .commonModes)

func update(link: CADisplayLink) {
    if lastTime == 0.0 {
        lastTime = link.timestamp
    }
    
    let currentTime = link.timestamp
    let elapsedTime = floor((currentTime - lastTime) * 10_000) / 10
    
    if elapsedTime > 16.7 {
        print("Frame was dropped with elapsed time of \(elapsedTime) at \(currentTime)")
    }
    
    lastTime = link.timestamp
}
```

### Loop

```swift
// print 0 2 4
for i in stride(from: 0, to: 5, by: 2) {
    print(i)
}
```

### Character

```swift
// Get char value
// Prints:
// 4 --> Optional(4)
// ④ --> Optional(4)
// 万 --> Optional(10000)
// a --> nil
c.wholeNumberValue

// Prints:
// 1 --> Optional(1)
// a --> Optional(10)
// Ｆ --> Optional(15)
// g --> nil
c.hexDigitValue

let startingValue = Int(("A" as UnicodeScalar).value) // 65
for i in 0 ..< 26 {
    print(Character(UnicodeScalar(i + startingValue)!))
}

let c = Character(UnicodeScalar(UnicodeScalar("a").value + 1) ?? UnicodeScalar(0)) // b
```

### Sort String

```swift
var str = "bcadf"
let array = str.characters.sorted()
let key = String(array)
```

### Loop String

```swift
for i in haystack.characters.indices {
    var indexHaystack = i
    var indexNeedle = needle.startIndex
    
    while indexHaystack != haystack.endIndex && indexNeedle != needle.endIndex {
        if haystack[indexHaystack] != needle[indexNeedle] {
            break
        }
        indexHaystack = haystack.index(after: indexHaystack) // next index
        indexNeedle = needle.index(after: indexNeedle)
    }
    
    if indexNeedle == needle.endIndex {
        return haystack.distance(from: haystack.startIndex, to: i) // return index by Int type
    }
}
```

### Substring
```swift
let toIndex = first.index(first.startIndex, offsetBy: i - 1)
first[first.startIndex...toIndex]
```

### Reverse String

```swift
let s = "abc"
String(s.characters.reversed())
```

### Array
```swift
// Allocate Space
var left: [Int] = Array(repeating: 0, count: nums.count)

// 2D array
var arr = Array(repeating: Array(repeating: 0, count: 2), count: 3)

// Reverse Order Visit
for i in (0...10).reversed()

// Index and Value
for (index, value) in nums.enumerated()

// Sort
struct aStruct {
    var value: Int
    var index: Int
}

for (index, value) in nums.enumerated() {
    let element = aStruct(value: value, index: index)
    a.append(element)
}

a = a.sorted { (lhs: aStruct, rhs: aStruct) -> Bool in
    return lhs.value <= rhs.value
}
```

### UIImage Corner Radius

```swift
func roundImage(_ orig: UIImage, radius: CGFloat, size: CGSize) -> UIImage? {
    UIGraphicsBeginImageContextWithOptions(size, false, 0);
    let rect = CGRect(x: 0, y: 0, width: size.width, height: size.height)
    UIBezierPath(roundedRect: rect, cornerRadius: radius).addClip()
    orig.draw(in: rect)
    let result = UIGraphicsGetImageFromCurrentImageContext()
    UIGraphicsEndImageContext();
    
    return result;
}
```

### Tuples

```swift
let tup: (Int, Int)

if let (a, _) = tup {
	print(a)
}
```

### Protocol programming

```swift
enum Category {
	case a
	case b
}

class CategoryDataSource {
	init(_ category: Category) {

	}

	// base on category do something
}

// This is better. Protocol programming

protocol CategoryDataSource {
	func a()
	func b()
	...
}

class ACategoryDataSource: CategoryDataSource {

}

class BCategoryDataSource: CategoryDataSource {
	
}
```

### Enum

```swift
// Original
enum AssetType {
  case photo
  case camera
}

protocol ImagePickerAsset {
  var assetType: AssetType { get }
}

class CameraAsset: ImagePickerAsset {
  var assetType: AssetType {
    return .camera
  }

  let image: UIImage

  init(_ image: UIImage) {
    self.image = image
  }
}

class PhotoAsset: ImagePickerAsset {
  var assetType: AssetType {
    return .photo
  }

  let phAsset: PHAsset

  init(_ phAsset: PHAsset) {
    self.phAsset = phAsset
  }
}

// Change to
  enum ImagePickerCellType {
    case image(PHAsset)
    case cameraButton(UIImage)
  }
```

### Hash

```swift
extension ListNode: Hashable {
    public var hashValue: Int {
        return ObjectIdentifier(self).hashValue
    }    
}

extension ListNode: Equatable {
    public static func == (lhs: ListNode, rhs: ListNode) -> Bool {
        return ObjectIdentifier(lhs) == ObjectIdentifier(rhs)
    }
}
```

### Function Programming
```swift
var greeting = "Hello,****playground***dsafsdf"

let a = greeting.map { c -> String in
  if c.isLetter {
    return "1"
  }

  return String(c)
}.joined()

print(a) // 11111,****1111111111***1111111

let b = a.split(separator: "*")

print(b) // ["11111,", "1111111111", "1111111"]
print(type(of: b)) // Array<Substring>
```

### List
```swift
class Node {
  var next: Node?
  var pre: Node?
  var val: Int
  
  init(next: Node? = nil, pre: Node? = nil, val: Int) {
    self.next = next
    self.pre = pre
    self.val = val
  }
}

class List {
  private var head: Node? = nil
  private var tail: Node? = nil
  
  private var val2Node: [Int: Node] = [:]
  
  func printList() {
    var node = head
    while let _node = node {
      print(_node.val, terminator: ",")
      node = node?.next
    }
    
    print("End")
  }
  
  func append(val: Int) {
    let node = Node(val: val)
    if head == nil {
      head = node
      tail = node
    } else {
      tail?.next = node
      node.pre = tail
      tail = node
    }
    
    val2Node[val] = node
  }
  
  func insert2Head(val: Int) {
    let node = Node(val: val)
    if head == nil {
      head = node
      tail = node
    } else {
      head?.pre = node
      node.next = head
      head = node
    }
    
    val2Node[val] = node
  }
  
  func removeVal(_ val: Int) {
    guard let node = val2Node[val] else {
      return
    }
    
    let preNode = node.pre
    let nextNode = node.next
    preNode?.next = nextNode
    nextNode?.pre = preNode
    
    if node === head {
      head = nextNode
    }
    
    if node === tail {
      tail = preNode
    }
  }
}
```

[1]:	https://oleb.net/blog/2016/08/swift-3-strings/
