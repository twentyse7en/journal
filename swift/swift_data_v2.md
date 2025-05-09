# Swift Data V2

## Core Components

### Usage in component
```swift
import SwiftUI
import SwiftData // <--------------------------------------------------------------- Import SwiftData


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

## Relationship
In tutorial which we are creating so far, there is Friend and Movies. A user can have fav movie, a movie can be favourited by multiple
people. One to Many Relationship.

```swift
@Model
class Friend {
    var name: String
    var favoriteMovie: Movie?  // <-------------------- One and it's optional


    init(name: String) {
        self.name = name
    }
}
```
**⚠️ Note**: If it's was a db, we might have stored id of movie, but we need to reference entire Movie, **Important!**

```swift
@Model
class Movie {
    var title: String
    var releaseDate: Date
    var favoritedBy = [Friend]() // <----------------------- allows array of Friends
```
The Friend.favoriteMovie and Movie.favoritedBy properties form a two-way relationship. In the direction from Movie to Friend, it’s a one-to-many relationship: One movie can be favorited by multiple friends. You can create the appropriate type of relationship by choosing between an array or a single value for each property, enabling you to create one-to-one, one-to-many, and many-to-many relationships.

```swift
        Section ("Favorited By"){
            List {
                ForEach(movie.favoritedBy) { friend in // <----------------- We can directly access values
                    Text(friend.name)
                }
            }
        }
```

## Condition - Predicate
- You use [predicates](https://developer.apple.com/documentation/foundation/predicate) to describe conditions for SwiftData to filter data.

### Search Movies
```swift
struct MovieList: View {
    @Query private var movies: [Movie]
    @Environment(\.modelContext) private var context
    @State private var newMovie: Movie?


    init(titleFilter: String = "") { // <-----------------------------------| Props titleFilter
        let predicate = #Predicate<Movie> { movie in
            titleFilter.isEmpty ||
            movie.title.localizedStandardContains(titleFilter) // <---------| logic for filtering
        }


        _movies = Query(filter: predicate, sort: \Movie.title) // <----------| _movie (special)
    }
```
- here we are passing `titleFiler` as a string to for filtering movies
- we need to be careful about predicate, we can add any code here, this need to be as per [predicate](https://developer.apple.com/documentation/foundation/predicate) rules. I wasted too much time not understand this 😓
- `_movies`: this one tricky, The underscore property (_movies) is the actual storage that SwiftData uses behind the scenes. So even though we haven't declared this when `moives` was created this one is also created.

## Many to Many Relationship
After compeleting One-to-Many relationship, I thought I could naturally pick up Many-to-Many Relationship. But I that was noobie mistake.
There is more to learn about swiftdata, we try to learn about Many to many first. 
- [Resource 1](https://www.hackingwithswift.com/quick-start/swiftdata/how-to-create-many-to-many-relationships)
- [Resource 2](https://www.youtube.com/watch?v=CAr_1kcf2_c&list=PLBn01m5Vbs4Ck-JEF2nkcFTF_2rhGBMKX&index=1)     
If you do something, swift data will crash without giving clue about the error, Manh! (It's like you girlfriend, will go upset without telling reason)

1. Step 1: Mark the relationship properly
```swift
@Model
class Actor {
    var name: String
    var movies: [Movie]

    init(name: String, movies: [Movie]) {
        self.name = name
        self.movies = movies
    }
}

@Model
class Movie {
    var name: String
    var releaseYear: Int
    @Relationship(inverse: \Actor.movies) var cast: [Actor] // <--------------------------------- (mandatory)

    init(name: String, releaseYear: Int, cast: [Actor]) {
        self.name = name
        self.releaseYear = releaseYear
        self.cast = cast
    }
}
```

2. Inset to context
No need to explicitly insert mi2
```swift
let mi2 = Movie(name: "Mission: Impossible 2", releaseYear: 2000, cast: [])
let cruise = Actor(name: "Tom Cruise", movies: [mi2])
let newton = Actor(name: "Thandiwe Newton", movies: [mi2])

modelContext.insert(cruise)
modelContext.insert(newton)
```
3. List of don'ts

```swift
let mi2 = Movie(name: "Mission: Impossible 2", releaseYear: 2000, cast: [])

let cruise = Actor(name: "Tom Cruise", movies: [])
let newton = Actor(name: "Thandiwe Newton", movies: [])

// DONT MANIPULATE BEFORE INSERT
mi2.cast.append(cruise)
mi2.cast.append(newton)

modelContext.insert(mi2)
modelContext.insert(cruise)
modelContext.insert(newton)


//------ Do after insertion
// mi2.cast.append(cruise)
// mi2.cast.append(newton)
```
> You’ll also get a crash with the message “illegal attempt to establish a relationship” if you attempt to insert things out of sequence, like this:
```swift
let mi2 = Movie(name: "Mission: Impossible 2", releaseYear: 2000, cast: [])
modelContext.insert(mi2)

let cruise = Actor(name: "Tom Cruise", movies: [mi2])
let newton = Actor(name: "Thandiwe Newton", movies: [mi2])

modelContext.insert(cruise)
modelContext.insert(newton)
```

## Yet to Explore
- `NavigationStack` and `NavigationLink` which I ignored.
- Using `Date`, `DatePicker`, and `Calendar` to work with dates
- Positioning content on the screen using `.safeAreaInset`
- Formatting dates in `Text` views


## Reference
- https://developer.apple.com/tutorials/develop-in-swift/navigate-sample-data
- (Good intro) https://developer.apple.com/documentation/swiftdata/preserving-your-apps-model-data-across-launches 
- (Another tuto) https://developer.apple.com/documentation/swiftdata/adding-and-editing-persistent-data-in-your-app
- (Populating data) https://developer.apple.com/documentation/swiftdata/maintaining-a-local-copy-of-server-data
