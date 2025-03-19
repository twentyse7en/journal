# Swift Data V2

## Core Components

### Usage in component
```swift
import SwiftUI
import SwiftData


struct FriendList: View {
    @Query(sort: \Friend.name) private var friends: [Friend] // <------------------- populate data and sort !wow 
    @Environment(\.modelContext) private var context // <--------------------------- connect to db to manipulate


    var body: some View {
        List {
            ForEach(friends) { friend in
                Text(friend.name)
            }
        }
        .task {
            context.insert(Friend(name: "Abijith")) // <---------------------------- insert data
            context.insert(Friend(name: "Anujith"))
        }
    }
}


#Preview {
    FriendList()
        .modelContainer(for: Friend.self, inMemory: true) // <---------------------- database 
}
```

### Custom Model
We might have multiple related data, we can combile all the data

```swift
import Foundation
import SwiftData


@MainActor  <-----------------------------------| to run on main thread
class SampleData {
    static let shared = SampleData() <----------| Singleton Instance


    let modelContainer: ModelContainer


    var context: ModelContext {
        modelContainer.mainContext  <-----------| For accessing/manipulating content
    }


    private init() {
        let schema = Schema([
            Friend.self,
            Movie.self,
        ])
        let modelConfiguration = ModelConfiguration(schema: schema, isStoredInMemoryOnly: true)


        do {
            modelContainer = try ModelContainer(for: schema, configurations: [modelConfiguration])
        } catch {
            fatalError("Could not create ModelContainer: \(error)")
        }
    }
}
```
1. `@MainActor`: Ensures code execution happens on the main thread. It's particularly important for UI-related code since UI updates must occur on the main thread.
2. Singleton Instance
   - Provides global access point via `SampleData.shared`
   - Guarantees single instance of data manager
3. context
    - this is [computed property](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/properties/#Read-Only-Computed-Properties).ie, Instead of storing a value directly, this property calculates and returns a value when accessed. It's a shorthand, check docs for more example.

```swift
    // Usage
    ContentView()
        .modelContainer(SampleData.shared.modelContainer)
````
- This tutorial introduced `NavigationStack` and `NavigationLink` which I ignored.

**NOTE**: At the end of tutorial, `SampleData` was used for preview only, instead below code is used
```swift
@main
struct NavigationSwiftDataApp: App {
    var body: some Scene {
        WindowGroup {
            ContentView()
        }
        .modelContainer(for: [Movie.self, Friend.self])
    }
}
```

### Saving Data
```swift
            HStack{
                TextField("Add Movie", text: $newMovie)
                Button("Save") {
                    print($newMovie)
                    let date =  Date(timeIntervalSinceReferenceDate: -402_000_000);
                    context.insert(Movie(title: newMovie, releaseDate: date)) // <------------------------ //
                    newMovie = ""
                }
            }.padding(40)
```
If you have `@Query` it will automatically refresh after save is complete

### ☕ Break - Sidequest
#### `.task`
- A SwiftUI modifier for performing asynchronous work when a view appears. It automatically cancels the task if the view disappears.
- Use for asynchronous tasks like fetching data, network requests, or any async/await operations tied to the view's lifecycle.

#### `.onAppear`
- A SwiftUI modifier for running synchronous code when a view appears on the screen.
- Use for synchronous tasks like updating state, starting animations, or logging when a view appears.

### Computeted Property in Model
```swift
import Foundation
import SwiftData


@Model
class Friend {
    var name: String
    var birthday: Date


    init(name: String, birthday: Date) {
        self.name = name
        self.birthday = birthday
    }


    var isBirthdayToday: Bool {
        Calendar.current.isDateInToday(birthday) // <-------------- comptuted when needed
    }
}
```
### Delete Content
```swift
    private func deleteMovies(indexes: IndexSet) {
        for index in indexes {
            context.delete(movies[index]) // <-----------------------
        }
    }
```
### Update content
You can consider this as normal `@State` data, just mutate that's it.

```swift
struct FriendDetails: View {
    @Bindable var friend: Friend // <--------------------------- Add binding as usual
    
    var body: some View {
        TextField("Name", text: $friend.name)
            .autocorrectionDisabled()
            .navigationTitle("Friend")
            .navigationBarTitleDisplayMode(.inline)
    }
}
```

-- continue: https://developer.apple.com/tutorials/develop-in-swift/create-update-and-delete-data

## Yet to Explore
- `NavigationStack` and `NavigationLink` which I ignored.
- Using `Date`, `DatePicker`, and `Calendar` to work with dates
- Positioning content on the screen using `.safeAreaInset`
- Formatting dates in `Text` views


## Reference
- https://developer.apple.com/tutorials/develop-in-swift/navigate-sample-data
