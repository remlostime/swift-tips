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

[1]:	https://oleb.net/blog/2016/08/swift-3-strings/
