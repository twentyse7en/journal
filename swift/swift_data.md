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

## DataManager

```swift
class DataManager: ObservableObject {
    let modelContainer: ModelContainer
    let modelContext: ModelContext
    
    private let initialSetupKey = "initialDataSetupCompleted"
    
    init() {
        do {
            let schema = Schema([Category.self, Affirmation.self])
            let modelConfiguration = ModelConfiguration(schema: schema)
            modelContainer = try ModelContainer(for: schema, configurations: [modelConfiguration])
            modelContext = ModelContext(modelContainer)
            
            // Check if we need to populate initial data
            // checkAndPopulateInitialData()
        } catch {
            fatalError("Failed to create ModelContainer: \(error.localizedDescription)")
        }
    }
}
```
### The DataManager Class
This class is designed to encapsulate all SwiftData operations, following a good separation of concerns pattern.

@ObservableObject: This protocol allows SwiftUI views to observe the class instance and react to any changes in its published properties.
### Key SwiftData Components in the Code
1. Schema
    - Defines the data model structure
    - In this code, it includes two model classes: Category and Affirmation
    - The schema essentially maps your Swift objects to database tables

2. ModelConfiguration
    - Configures how the data models behave
    - Can specify options like storage location, migration policies, etc.

3. ModelContainer
    - The actual container that holds your data
    - Responsible for storage, retrieval, and persistence
    - Initialized with a schema and configurations
    - Comparable to a database instance

4. ModelContext
    - Used for interacting with the data
    - Handles operations like fetching, inserting, updating, and deleting objects
    - Similar to a session or transaction in database terms
