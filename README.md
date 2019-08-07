# SwiftCorePointer
swiftCorePointer based on swiftCore memoryLayout

**swift version =>: swift 5.0**

## Swift

### [Struct](https://github.com/TannerJin/SwiftCorePointer/blob/master/SwiftPointerDemo/SwiftRuntime/Struct.swift)

```swift
struct StructValue {
    let a: Int8 = 4
    let b: Int16 = 6
    let c: Int32 = 8
}

var structValue = StructValue()
let structValuePointer = withUnsafePointer(to: &structValue) { (pointer) -> UnsafeMutableRawPointer in
    UnsafeMutableRawPointer(OpaquePointer(pointer))
}
structValuePointer.advanced(by: 2).assumingMemoryBound(to: Int16.self).initialize(to: 99)

// structValue.b = 99
```

### [Class](https://github.com/TannerJin/SwiftCorePointer/blob/master/SwiftPointerDemo/SwiftRuntime/Class.swift)   

```swift
class A {
  var a = 666
}

let a1 = A()
unowned let unowned_a = a1
let a2 = a1
let a3 = a2

let strongRefCount = StrongRefCount(a1)
let unownedRefCount = UnownedRefCount(a1)
let hasWeakRef = hasWeakRefCount(a1)
```

### [Protocol](https://github.com/TannerJin/SwiftCorePointer/blob/master/SwiftPointerDemo/SwiftRuntime/Protocol.swift)

```swift
protocol SwiftProtocol {
    func foo()
    func foo2()
}

struct StructValue: SwiftProtocol {
    func foo() {
        print("foo")
    }
    
    func foo2() {
        print("foo2")
    }
}

var Protocol: SwiftProtocol = StructValue()
let protocol_pointer = withUnsafePointer(to: &Protocol) { (pointer) -> UnsafeMutableRawPointer in
    UnsafeMutableRawPointer(OpaquePointer(pointer))
}

let witness_table_pointer_value = protocol_pointer.advanced(by: 32).assumingMemoryBound(to: UInt.self).pointee
let witness_table_pointer = UnsafeMutablePointer<UnsafeMutableRawPointer>.init(bitPattern: witness_table_pointer_value)

typealias FooMethod = @convention(thin) ()->Void

let foo2_pointer = unsafeBitCast(witness_table_pointer!.advanced(by: 2).pointee, to: FooMethod.self)
foo2_pointer()

// print foo2
```

### [Enum](https://github.com/TannerJin/SwiftCorePointer/blob/master/SwiftPointerDemo/SwiftRuntime/Enum.swift)   

```swift
enum EnumValue {
    case a
    case b(String)
    case c(Int32)
    case d
    case f(Int64)
    case e
}

var enumC = EnumValue.c(8)
let enumCPointer = withUnsafePointer(to: &enumC) { (pointer) -> UnsafeMutableRawPointer in
     UnsafeMutableRawPointer(OpaquePointer(pointer))
}
enumCPointer.advanced(by: 16).assumingMemoryBound(to: Int8.self).initialize(to: 0x02)

// enumC = EnumValue.f(8)
```


## SwiftCore

### [Bool](https://github.com/TannerJin/SwiftCorePointer/blob/master/SwiftPointerDemo/SwiftCorePointer/Bool.swift)

```swift
var bool = true
let boolPointer = bool.valuePointer
boolPointer.initialize(to: 0)

// bool = false 
```

### [Optional](https://github.com/TannerJin/SwiftCorePointer/blob/master/SwiftPointerDemo/SwiftCorePointer/Optional.swift)

```swift
var optional: Int16?
var optionalPointer = optional.valuePointer
optionalPointer.assumingMemoryBound(to: Int16.self).initialize(to: 99)

// optional = 99
```

### [String](https://github.com/TannerJin/SwiftCorePointer/blob/master/SwiftPointerDemo/SwiftCorePointer/String.swift)

```swift
var string = "TannerJin"
let stringPointer = string.valuePointer
stringPointer.assumingMemoryBound(to: Int8.self).initialize(to: 0x40)      // 0x40 => "@"

// string = "@anner Jin"    
```

### [Character](https://github.com/TannerJin/SwiftCorePointer/blob/master/SwiftPointerDemo/SwiftCorePointer/Character.swift)

```swift
var character = Character("😂")
let characterPointer = character.valuePointer.assumingMemoryBound(to: UInt8.self)
characterPointer.initialize(to: 0xf0)                             // f0 9f 9a 80 => "🚀"  unicode(utf-8)
characterPointer.advanced(by: 1).initialize(to: 0x9f)
characterPointer.advanced(by: 2).initialize(to: 0x9a)
characterPointer.advanced(by: 3).initialize(to: 0x80)

// character = "🚀"
```

### [Array](https://github.com/TannerJin/SwiftCorePointer/blob/master/SwiftPointerDemo/SwiftCorePointer/Array.swift)

```swift
let array = ["Apple", "Swift"]
let arrayPointer = array.valuePointer
arrayPointer.initialize(to: "Tanner")

// array = ["Tanner", "Swift"]

array.countPointer.initialize(to: 3)

// array.count = 3
```

### [Set](https://github.com/TannerJin/SwiftCorePointer/blob/master/SwiftPointerDemo/SwiftCorePointer/Set.swift)

 ```swift
 let set: Set<String> = ["Apple", "iOS", "Swift"]
 set.valuesPointer.initialize(to: "Tanner")
 
 // set =  ["Tanner", "Swift", "iOS"] ||  ["Tanner", "Swift", "Apple"] || ...
 
 set.countPointer.initialize(to: 2)
 
 // set.count = 2
 ```

### [Dictionary](https://github.com/TannerJin/SwiftCorePointer/blob/master/SwiftPointerDemo/SwiftCorePointer/Dictionary.swift)

```swift
let dictionary = ["Swift": 5.0, "iOS": 2019]

let dicCountPointer = dictionary.countPointer
let dicKeysPointer = dictionary.keysPointer
let dicValuesPointer = dictionary.valuesPointer
```
