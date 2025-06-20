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
ForEach(viewModel.cards) {card in 
    CardView(card)
}
```

to be continued