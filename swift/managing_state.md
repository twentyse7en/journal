# Managing State

## Binding
Communication is two way, when data is passed to child, child can also change the data using `@Binding`

```swift
// Parent View
@State var score = 0

ChildView(score: $score)  // Pass with $

// Child View
@Binding var score: Int   // Can read and modify parent's value
```