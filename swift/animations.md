# Animation


# Gesture animation
Giving example of card swipe.

Key properties:  
1. `offset` is like position relative, how much should this moved from initial position.
2. `rotationEffect` this is cool transformation, as we drags rotate a bit
3. `DragGesture.onChanged` callback to change offset as we drag
4. `DragGesture.onEnded` callback to handle what to at end. If card is moved beyond a threshold we can remove or move it back to original position. 

```swift
import SwiftUI

struct CardSwipeNew: View {
    @State private var offset = CGSize.zero
    
    
    
    func handleSwipe(offsetWidth: CGFloat, offsetHeight: CGFloat) {
        switch (offsetWidth, offsetHeight) {
        case (-500...(-150), _):
            offset = CGSize(width: -500, height: 0)
        case (150...500, _):
            offset = CGSize(width: 500, height: 0)
        case (_, -500...(-150)):
            offset = CGSize(width: 0, height: -500)
        case (_, 150...500):
            offset = CGSize(width: 0, height: 500)
        default:
            offset = .zero
        }
    }
    
    var body: some View {
        Rectangle().fill(Color.red)
            .cornerRadius(20)
            .frame(width: 320, height: 420)
            .offset(x: offset.width * 1, y: offset.height * 0.4)
            .rotationEffect(.degrees(Double(offset.width / 40)))
            .gesture(
                DragGesture()
                    .onChanged { gesture in
                        offset = gesture.translation
                    }
                    .onEnded { gesture in
                        withAnimation {
                            handleSwipe(offsetWidth: offset.width, offsetHeight: offset.height)
                        }
                    }
            )
        
    }
}
```