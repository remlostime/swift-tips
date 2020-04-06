# swift-tips

## Article
* [String][1]

## Code

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

[1]:	https://oleb.net/blog/2016/08/swift-3-strings/
