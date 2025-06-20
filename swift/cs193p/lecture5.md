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