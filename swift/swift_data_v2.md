# Swift Data V2

## Core Components

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


## Reference
- https://developer.apple.com/tutorials/develop-in-swift/navigate-sample-data
