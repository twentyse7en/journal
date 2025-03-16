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
#### Key SwiftData Components in the Code
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

## What is a FetchDescriptor?

A `FetchDescriptor` is a structure in Swift Data that describes how to retrieve model objects from your `ModelContainer`. It allows you to:

- Filter results using predicates
- Sort results in a specific order
- Limit the number of results returned
- Include or exclude specific properties in the results

## Basic Usage

### Creating a Simple FetchDescriptor

```swift
// Define a basic fetch descriptor for all items
let allItemsFetchDescriptor = FetchDescriptor<Item>()

// Fetch all items from the model container
do {
    let items = try modelContext.fetch(allItemsFetchDescriptor)
    // Use the fetched items
} catch {
    print("Failed to fetch items: \(error)")
}
```

### Filtering with Predicates

```swift
// Fetch only completed items
var completedItemsFetchDescriptor = FetchDescriptor<Item>()
completedItemsFetchDescriptor.predicate = #Predicate<Item> { item in
    item.isCompleted == true
}

// Fetch items with a specific tag
var taggedItemsFetchDescriptor = FetchDescriptor<Item>()
taggedItemsFetchDescriptor.predicate = #Predicate<Item> { item in
    item.tags.contains("important")
}
```

### Sorting Results

```swift
// Sort by creation date (newest first)
var recentItemsFetchDescriptor = FetchDescriptor<Item>()
recentItemsFetchDescriptor.sortBy = [SortDescriptor(\Item.createdAt, order: .reverse)]

// Sort by priority then by name
var prioritizedItemsFetchDescriptor = FetchDescriptor<Item>()
prioritizedItemsFetchDescriptor.sortBy = [
    SortDescriptor(\Item.priority, order: .reverse),
    SortDescriptor(\Item.name)
]
```

### Limiting Results

```swift
// Fetch only the first 10 items
var limitedFetchDescriptor = FetchDescriptor<Item>()
limitedFetchDescriptor.fetchLimit = 10
```

## Advanced Features

### Property Selection

```swift
// Only fetch specific properties to improve performance
var efficientFetchDescriptor = FetchDescriptor<Item>()
efficientFetchDescriptor.propertiesToFetch = [\Item.id, \Item.name]
```

### Relationship Depth

```swift
// Control how many levels of relationships to include
var relationalFetchDescriptor = FetchDescriptor<Item>()
relationalFetchDescriptor.relationshipKeyPathsForPrefetching = [\Item.category, \Item.tags]
```

### Combined Example

```swift
// A comprehensive fetch descriptor
var comprehensiveFetchDescriptor = FetchDescriptor<Item>()
comprehensiveFetchDescriptor.predicate = #Predicate<Item> { item in
    item.isCompleted == false && item.priority > 3
}
comprehensiveFetchDescriptor.sortBy = [SortDescriptor(\Item.dueDate)]
comprehensiveFetchDescriptor.fetchLimit = 50
comprehensiveFetchDescriptor.includesSubentities = true
```

## Working with SwiftUI

```swift
struct ContentView: View {
    @Query(sort: \Item.createdAt, order: .reverse) var items: [Item]
    
    // @Query is a property wrapper that internally uses FetchDescriptor
    // It automatically updates when the data changes
    
    var body: some View {
        List(items) { item in
            Text(item.name)
        }
    }
}
```

## Custom Query Functions

```swift
extension ModelContext {
    func fetchRecentItems(limit: Int = 20) throws -> [Item] {
        var descriptor = FetchDescriptor<Item>()
        descriptor.predicate = #Predicate<Item> { item in
            item.createdAt > Date().addingTimeInterval(-7*24*60*60)
        }
        descriptor.sortBy = [SortDescriptor(\Item.createdAt, order: .reverse)]
        descriptor.fetchLimit = limit
        
        return try fetch(descriptor)
    }
}
```

## Error Handling

```swift
do {
    let items = try modelContext.fetch(fetchDescriptor)
    // Process items
} catch {
    if let swiftDataError = error as? SwiftDataError {
        // Handle specific Swift Data errors
        switch swiftDataError {
        case .modelReadOnly:
            print("The model is read-only")
        case .validationFailed:
            print("Validation failed")
        default:
            print("Other Swift Data error: \(swiftDataError)")
        }
    } else {
        print("Unexpected error: \(error)")
    }
}
```

## Performance Tips

- Use `fetchLimit` to avoid loading too many objects into memory
- Use `propertiesToFetch` to only load the properties you need
- Consider batching large fetches using multiple fetch operations with different predicates
- Use `@Query` in SwiftUI for automatic updates rather than manually fetching when possible
```

## Common Issues and Solutions

### Invalid Predicate Format
If you're getting errors with predicates, make sure you're using the correct format:

```swift
// Swift Data uses the new macro-based predicates
// Correct:
fetchDescriptor.predicate = #Predicate<Item> { item in
    item.name.contains("Swift")
}

// Incorrect (old NSPredicate style):
// fetchDescriptor.predicate = NSPredicate(format: "name CONTAINS %@", "Swift")
```

### Relationship Traversal
To filter based on relationships:

```swift
fetchDescriptor.predicate = #Predicate<Project> { project in
    project.tasks.contains(where: { $0.isCompleted == false })
}
```

### Case-Insensitive Searches
For case-insensitive text searches:

```swift
fetchDescriptor.predicate = #Predicate<Item> { item in
    item.name.localizedStandardContains("search term")
}
```
-----------------------------------------------------------------
##  SwiftUI App with DataManager Explanation

```swift
import SwiftUI
import SwiftData

@main
struct AffirmationsApp: App {
    @StateObject private var dataManager = DataManager()
    
    var body: some Scene {
        WindowGroup {
            ContentView()
                .environmentObject(dataManager)
        }
    }
}
```
// usage
```swift
struct ContentView: View {
    @EnvironmentObject private var dataManager: DataManager

    
    var body: some View {}

    private func loadData() {
        categories = dataManager.fetchCategories()
        loadAffirmations()
    }
    
```

## Detailed Explanation

### DataManager Integration
- `@StateObject private var dataManager = DataManager()`: This line creates an instance of a custom `DataManager` class as a state object.
  - `@StateObject`: This property wrapper creates an observable object that persists for the lifetime of the app. It ensures the `DataManager` is initialized only once when the app starts up.
  - The `DataManager` is likely handling all the data persistence operations using SwiftData.

### Scene Configuration
- `.environmentObject(dataManager)`: This injects the `dataManager` into the SwiftUI environment, making it available to `ContentView` and all of its child views using the `@EnvironmentObject` property wrapper.


By using `@StateObject` and `.environmentObject()`, the app ensures that:
- The `DataManager` is created only once when the app launches.
- All views that need access to the data can easily obtain it through the environment.
- Changes to the data will automatically trigger UI updates in views that observe this data.

This architecture follows SwiftUI's data flow patterns, where data is passed down through the view hierarchy and changes propagate back up through bindings or observed objects.

# Common Issue
underlying database is sqlite, I think we can't think swift inside `#predicate` we need to think like db

- https://www.hackingwithswift.com/quick-start/swiftdata/common-swiftdata-errors-and-their-solutions
## EXC_BAD_ACCESS (code=1, address=0x0) / Crashing

```swift
@Model
class Author {
    var name: String
    var books: [String] // <------------------------------------------------------

    init(name: String, books: [String]) {
        self.name = name
        self.books = books
    }
}

@Query(filter: #Predicate<Author> {
    $0.books.contains("The Hobbit")
}) var authors: [Author]
```
Certainly it compiles cleanly. However, it also triggers the dreaded EXC_BAD_ACCESS, because we're trying to search a string array in a predicate – something that isn't allowed in SwiftData.

To fix this you need to create a relationship rather than a simple array. Internally SwiftData sees something like [String] and uses Codable to convert it to a data blob that can be saved alongside its parent model. This makes it impossible to search by SQLite, the underlying data store.

So, you should create a second model and make a relationship with it, even if that model only has a single string property.
