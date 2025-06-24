# Lecture 5

## QuickNotes
### Rabbit hole of fixing Equatable
when you are making some generic thing and want to use == then all types in generic needs to be equatable (or refer the starting of lecture)
```swift
struct Card: Equatable {
    var isFaceUp = true
    var isMatched = false
    let content: CartContent // this also needs to be equatable
}
```

### Identifiable
swift will complain over lot of things, even for a simple `forEach` to iterate over above `[Card]`, it need to be `Identifiable`
```swift
struct Card: Equatable, Identifiable {
    var id: String

    var isFaceUp = true
    var isMatched = false
    let content: CartContent
}
```

### Animation 🤯
[timestamp](https://youtu.be/F1x-H8kEwo8?t=1240)
That was really amazing. Coming from a web background to react native mobile, this stuff is really mindblowing, All you need to do is a single line.

```swift
// cards - LazyGrid
ForEach(viewModel.cards) {card in 
    CardView(card)
}

// body
ScrollView {
    cards
        .animation(.default, value: viewModel.cards) // do the animation when value changes
}
```
⚠️ This was not working when this was iterated using a `forEach`, which is [explained](https://youtu.be/F1x-H8kEwo8?t=432), but I didn't get.  
🤔💭 animation was also working when a property of a card was changed (only animated for that card), May be `.animation(..)` added to parent might have got into all children as well.

## Custom debug string

```swift
struct Card: Equatable, Identifiable, CustomDebugStringConvertible {
    // ... usual stuff

    var debugDescription:String {
        "\(id): \(content)" // these are the properties of Card
    }
}
```

## struct is passed as value
when you passing struct as argument, it is passed as a value not a reference. You can't modify that.

## Enum
enum feels very powerful in swift, It can save data as well.

```swift
enum FastFoodMenuItem {
    case hamburger(numberOfPatties: Int)
    case fries(size: FryOrderSize)
    case drink(String, ounces: Int) // unnamed first parameter
    case cookie
}

enum FryOrderSize {
    case large
    case small
}

var fries: FastFoodMenuItem = .fries(size: .large)
```

An enum's state is usually checked with a **switch** statement. Although we could use an if statement, but it is unusal if there is a associated data.

```swift
var fries: FastFoodMenumItem = .fries(size: .large)

// NOTE: switch <enum>
switch fries {
    case .fries(let size): print("size \(size)")
    default:  print("Others")
}
```
- You don't need to add break after each case
- You can have methods, but not properties

```swift
enum FastFoodMenuItem {
    // ...
    func isSpecialOrder(number: Int) -> Bool {
        switch self { // <---------------- self to reference itself
            case .hamburger(let pattyCount): return pattyCount === number
        }
    }
}
```

- Getting all cases of enum, we need to add a protocol to it
```swift
enum TeslaModel: CaseIterable {
    case X
    case Y
    case Three
    case Y
}

for model in TeslaModel.allCases {
    // do what you want
}
```

## Optional

```swift
var hello: String?
var hello: String? = "hello"

// above code
var hello: Optional<String> = .none
var hello: Optional<String> = "hello"

print(hello!) // forced - if nil it will crash app


// TO safely look at value
if let safeHello = hello {
    print(safeHello)
} else {
    // do something else
}

// or

if let hello {
    print(hello) // but why not if hello?
} else {
    //
}

// ❌ this isn't the right way
if hello != nil {
    // hello is still of type String?
    // You can force unwrap (if you are sure) but that's risky
    print(hello!)
}

```

## Bonus
Swift compiled to c

```swift
struct Point { 
    var x: Int  // 8 bytes
    var y: Int  // 8 bytes
    
    mutating func moveBy(dx: Int, dy: Int) {
        x += dx
        y += dy
    }
}
```

```c
#include <stdio.h>

struct Point {
    int x; // 4 bytes
    int y; // 4 bytes
};

void moveBy(struct Point *p, int dx, int dy) {
    p->x += dx;
    p->y += dy;
}

```
# Get Set on computed property
```swift
class Example {
    var storedProperty: Int = 0     // 8 bytes allocated
    
    var computedProperty: Int {     // 0 bytes allocated
        get { return storedProperty * 2 }
        set { storedProperty = newValue / 2 }
    }
}

const example = Example();
print("value \(exampl.computedProperty)");
example.computedProperty = 2;

``` 
It's simply getting the value formatted and setting the value with some process, no storage for `computedProperty`

```js
let storedValue = 0;

const getFormattedValue = () => {
    return storedValue * 2;
}

const setFormattedValue = (newValue) => {
    storedValue = newValue / 2;
}
```

💭 It make sense, the place using the struct/class don't need to implement a format util, it's inside the class, and exposed via a property.

# Extension

```swift
// we can access the array using self
extension Array where Element == Int {
    func average() -> Double {
        guard !self.isEmpty else { return 0 }
        let total = self.reduce(0, +)
        return Double(total) / Double(self.count)
    }
}

// usage
let numbers = [10, 20, 30, 40]
print(numbers.average()) // Output: 25.0
```