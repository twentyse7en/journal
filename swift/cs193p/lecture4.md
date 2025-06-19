# Lecture 4

## Quick notes
- in model, viewmodel: try to write the variable as private first, then if required remove private
- `func choose(_ card: MemoryGame<String>.Card)` now we can call as `choose(data)` else it would have been `choose(card: data)`
- simplifing closures
```swift
// actual function
func createCardContent(forPairAtIndex index: Int) -> String {
    return ["A", "2", "3", "J", "K", "Q"][index]
}

MemoryGame(numberOfPairsOfCards: 4, cardContentFactory: createCardContent)


// can do this inline with close, can remove a type as this can be infered from MemoryGame init() definition
MemoryGame(numberOfPairsOfCards: 4) { index in 
    return ["A", "2", "3", "J", "K", "Q"][index]
}

// or you can even reduce to this
MemoryGame(numberOfPairsOfCards: 4) {
    return ["A", "2", "3", "J", "K", "Q"][$0]
}
// NOTE: $0 means first argument
```

- order you property initialized in undetermined (for class, may be for struct too?)
```swift
class EmojiMemoryGame {
    let emojis = ["🚆", "⛈️"]

    private var model = MemoryGame(numberOfPairsOfCards: 2) { index in 
        return emojis[index] // <--------------------------------------- throw error was emojis might not be available
    }
}


// solution
// NOTE: static help you to solve this
class EmojiMemoryGame {
    private static let emojis = ["🚆", "⛈️"]

    private var model = MemoryGame(numberOfPairsOfCards: 2) { index in 
        return EmojiMemoryGame.emojis[index]
    }
}

// even better: swift can figure out which emoji
class EmojiMemoryGame {
    private static let emojis = ["🚆", "⛈️"]

    private var model = MemoryGame(numberOfPairsOfCards: 2) { index in 
        return emojis[index]
    }
}
```

- minimumScaleFactor(_:) - if a text is not fitting in the available space, we can scale it down to a minimum size
```
HStack {
    Text("This is a very long label:")
        .lineLimit(1)
        .minimumScaleFactor(0.5)
    TextField("My Long Text Field", text: $myTextField)
        .frame(width: 250, height: 50, alignment: .center)
}
```

Both text can't fit in available space, "This is a very long label:" will scale down by half.

- `mutating` in struct
Below struct make complete sense, but it will through a error
```
struct Counter {
    var count = 0
    
    func increment() {
        count += 1
    }
}
```
when you are mutating a struct, it need to be marked as `mutating`

```
struct Counter {
    var count = 0
    
    mutating func increment() {  // 'mutating' required
        count += 1
    }
}
```

more details explanation:

The mutating keyword in Swift is required for methods in structs and enums that modify the instance's properties or state.   

**Why it's needed:** Structs and enums are value types and are immutable by default. When you call a method on a struct, Swift treats the instance as read-only unless you explicitly mark the method as mutating.  
**What it does:** The mutating keyword tells Swift that the method is allowed to change the struct's properties. Under the hood, it actually creates a new instance and replaces the original (similar to how 4 + 1 creates a new value rather than changing the number 4).
Usage rules:

Required for structs and enums when modifying properties
Not needed for classes (since they're reference types)
Can only be called on variables (var), not constants (let)
Can reassign self to a completely new instance


The fundamental reason is that structs are value types (copied when assigned) while classes are reference types (shared via pointers).

- Reactive UI: ObservableObject
when something changes, changes will reflect
use `@Published` property to values which will change, so once it's changed the ui can react to it.
`@ObservedObject` for used inside view. What about `@StateObject` ?


```swift
// BAD CODE: Don't write
struct GameView: View {
    @ObservedObject var viewModel = SomeViewModel()
}
```

`@ObservedObject` need to passed not assigned
```swift
struct GameView: View {
    @ObservedObject var viewModel
}
```
or if we need to assign use `@StateObject`, you might also not do this because, this is only inside your view, you can't share your statemodel with any other view
```swift
struct GameView: View {
    @StateObject var viewModel = EmojiMemoryGame()
}
```

somewhere in the toplevel, we keep it as `@StateObject` then passed to others as `@ObservedObject`

// continue: https://youtu.be/4CkEVfdqjLw?t=2155