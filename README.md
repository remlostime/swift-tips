# swift-tips

## Article
* [String][1]

## Code

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

[1]:	https://oleb.net/blog/2016/08/swift-3-strings/
