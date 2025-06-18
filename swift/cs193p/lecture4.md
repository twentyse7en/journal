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

// continue: https://youtu.be/4CkEVfdqjLw?t=2155