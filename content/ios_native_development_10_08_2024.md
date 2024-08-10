# what I felt with ios development
## Getting started
- very easy 
- take xcode, few next clicks
## What is different from react native
- each view can be previewed isolated
```swift
struct CardView_Previews: PreviewProvider {
    static var scrum = DailyScrum.sampleData[0]
    static var previews: some View {
        CardView(scrum: scrum)
            .background(scrum.theme.mainColor)
            .previewLayout(.fixed(width: 400, height: 60))
    }
}
```
- style (hstack, vstack similar to android)
- less import statements
```swift
import SwiftUI // <------   only one import here

struct CardView: View {
    let scrum: DailyScrum // <---- dailscrum is in ./Models/DailyScrum
    var body: some View {
        VStack(alignment: .leading) {
            Text(scrum.title)
                .font(.headline)
            Spacer()
            HStack {
                Label("\(scrum.attendees.count)", systemImage: "person.3")
            }
        }
    }
}

struct CardView_Previews: PreviewProvider {
    static var scrum = DailyScrum.sampleData[0]
    static var previews: some View {
        CardView(scrum: scrum)
            .background(scrum.theme.mainColor)
            .previewLayout(.fixed(width: 400, height: 60))
    }
}
```

## A lot of assets and components 
```swift
Label("300", systemImage: "hourglass.tophalf.fill")

Circle().strokeBorder(lineWidth: 24)

ProgressView(value: 5, total: 15);
```

## Resource
- https://developer.apple.com/tutorials/app-dev-training/getting-started-with-scrumdinger
## Conclusion
- I loved the experience
	- easy to get started
	- lot of assets
	- clean separation of code
	- quick preview of what we are doing