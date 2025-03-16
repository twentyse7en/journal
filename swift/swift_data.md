# Swift Data
- SwiftData is Apple's modern Swift-native persistence framework introduced at WWDC 2023. It provides a declarative approach to data modeling and persistence, designed to work seamlessly with SwiftUI.

## Core Concepts of SwiftData
```swift
import SwiftUI
import SwiftData

@Model
final class Category {
    var id: UUID
    var name: String
    var color: String
    @Relationship(.cascade) var affirmations: [Affirmation]?
    
    init(id: UUID = UUID(), name: String, color: String) {
        self.id = id
        self.name = name
        self.color = color
    }
}
```
### Model
- `@Model` macro to define a data model
- This macro transforms your class into a persistent model that SwiftData can manage, automatically generating the necessary code for storage and retrieval.
- `@Relationship`: This creates a one-to-many relationship between Category and Affirmation, with cascade delete behavior (when a Category is deleted, all related Affirmations are also deleted).

### WindowGroup
```swift
// App setup
@main
struct MyApp: App {
    var body: some Scene {
        WindowGroup {
            ContentView()
        }
        .modelContainer(for: [Category.self, Affirmation.self])
    }
}
```