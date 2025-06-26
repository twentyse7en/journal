# Intro
This Stanford CS193p lecture (Lecture 6, Spring 2023) delves into **SwiftUI Layout**, explaining how views are arranged on the screen. The core layout process in SwiftUI follows a predictable three-step system:

1.  **Container Views Offer Space:** Parent views (containers) offer a certain amount of space to their child views. The top-level `ContentView` is initially offered the entire device screen.
2.  **Child Views Choose Their Size:** Each child view, upon being offered space, decides its own size. This is a fundamental principle – views are in control of their own dimensions based on the space they're given.
3.  **Container Views Position Children:** After the children have chosen their sizes, the container view then positions these children within the space it originally offered, according to its layout rules (e.g., side-by-side for HStack, vertically for VStack).

Understanding these steps is key to mastering how SwiftUI lays out user interfaces.

## HStack/VStack
`HStack` (horizontal stack) and `VStack` (vertical stack) are fundamental layout containers in SwiftUI.

*   **Space Allocation:** They take the space offered to them by their parent and then divide it among their child views.
*   **Flexibility Principle:** Stacks offer space to their child views based on a "least flexible first" principle. This means:
    *   Views with fixed sizes (like an `Image` at its natural size) get their requested space first.
    *   Views that are somewhat flexible (like `Text`, which wants to fit its content but can shrink or wrap) are considered next.
    *   Highly flexible views (like `Spacer` or `RoundedRectangle`) will take up any remaining space.
*   **Sizing of the Stack Itself:** After all children have chosen their sizes, the HStack or VStack will size itself to fit those children. If a child is very flexible and takes all offered space, the stack itself will also become flexible. It might be smaller or larger than the space it was offered, depending on its content.
*   **Tools for Stacks:**
    *   **`Spacer()`:** A highly flexible view that expands to take up all available space in the stack's orientation, useful for pushing content to edges. Can have a `minLength`.
    *   **`Divider()`:** Draws a thin platform-dependent dividing line. It takes minimal space in its primary direction and expands in the cross-axis.
    *   **`.layoutPriority(Double)`:** A modifier to override the default "least flexible first" behavior. Views with higher layout priority get to choose their size before views with lower priority. The default priority is 0.
    *   **`alignment` Parameter:** Controls how views are aligned along the cross-axis of the stack.
        *   For `VStack`: `alignment: .leading`, `.center`, `.trailing`.
        *   For `HStack`: `alignment: .top`, `.center`, `.bottom`, or using text baselines like `.firstTextBaseline`, `.lastTextBaseline`.
    *   **Leading/Trailing vs. Left/Right:** SwiftUI uses "leading" and "trailing" for horizontal alignment to support right-to-left languages automatically. Leading refers to the edge where text starts.

## Lazy Stacks (LazyHStack, LazyVStack)
*   **Purpose:** Designed for performance when displaying a large number of items, especially within a `ScrollView`.
*   **"Lazy" Behavior:** They only build and lay out the views that are currently visible on screen (or very nearly visible). As the user scrolls, new views are built, and old ones may be deallocated.
*   **Sizing:** Unlike regular HStacks/VStacks, lazy stacks do *not* automatically expand to fill all offered space, even if they contain flexible views (like `Spacer`). They "scrunch down" to the minimum size needed for their *currently visible* content. This is why they are almost always used inside a `ScrollView`.

## Lazy Grids (LazyHGrid, LazyVGrid)
*   **Purpose:** Used for laying out items in a grid formation. We've seen `LazyVGrid` for our card game.
*   **Sizing:** They size their items based on information provided to them (e.g., the `columns` argument in `LazyVGrid`). The other direction (rows in `LazyVGrid`, columns in `LazyHGrid`) can grow and shrink as more views are added.
*   **Space Usage:** Similar to lazy stacks, they also do not necessarily take up all the space offered to them if they don't need it all.

## Grid
*   **Purpose:** A more general-purpose 2D layout container, often used for "spreadsheet" or "table of data" views. Unlike lazy grids, it doesn't have an 'H' or 'V' in its name because it inherently manages both dimensions.
*   **Alignment:** Offers extensive alignment options across columns and rows, often using `.grid...()` modifiers.
*   *(Note: The lecture indicates this will be covered in more detail later in the course).*

## ScrollView
*   **Purpose:** Allows content that is larger than the view's bounds to be scrolled.
*   **Sizing:** A `ScrollView` itself is very flexible and will take all the space offered to it by its parent.
*   **Content Sizing:** The views *inside* a `ScrollView` are sized to fit along the axis that you are scrolling on. For example, in a vertical `ScrollView`, the content's width will be constrained by the `ScrollView`'s width, but its height can be much larger.

## ViewThatFits
*   **Purpose:** A container that can choose which of its child views to display based on which one fits best in the space it's offered.
*   **Mechanism:** It takes a list of container views (often an `HStack` and a `VStack` containing the same logical content but arranged differently) in its `@ViewBuilder`. It then attempts to lay out the first one; if it fits, it's used. If not, it tries the second, and so on.
*   **Use Cases:**
    *   Adapting layout for **landscape vs. portrait** orientation.
    *   Handling **dynamic type sizes** (e.g., if text becomes too large to fit horizontally, `ViewThatFits` could switch to a vertical arrangement).

## Form, List, OutlineGroup, DisclosureGroup
*   **Nature:** These are described as "really smart VStacks." They are specialized containers that provide additional functionality beyond simple vertical stacking.
*   **Functionality:** They handle things like scrolling, selection, hierarchy (for `OutlineGroup` and `DisclosureGroup`), and platform-specific styling (especially `Form` and `List`).
*   *(Note: The lecture indicates these will be covered in more detail later in the course).*

## Custom Implementations of the Layout Protocol
*   **Purpose:** For highly specialized layout needs that go beyond what standard containers offer, you can create your own custom layout logic by implementing the `Layout` protocol.
*   **Mechanism:** This involves defining how your custom container performs the "offer space, let views choose their size, then position them" dance.
*   *(Note: The lecture mentions this is an advanced topic, but a brief example might be shown in a demo).*

## ZStack
*   **Purpose:** Lays out views on top of each other, along the Z-axis (depth).
*   **Sizing:** A `ZStack` sizes itself to fit its largest child view.
*   **Flexibility:** If even one of its children is fully flexible (e.g., `RoundedRectangle`, `Color`, `Spacer`), then the `ZStack` itself will also become fully flexible and will expand to take all the space offered to it.
*   **Alignment:** Has an `alignment` parameter to control how children are positioned within its bounds (e.g., `.topLeading`, `.center`, `.bottomTrailing`).

## .background and .overlay Modifiers
These view modifiers provide a convenient way to achieve a ZStack-like effect for two views without explicitly using a `ZStack`.

*   **`.background(Content)`:**
    *   The `Content` view is placed *behind* the view being modified.
    *   The overall size of the resulting combined view is determined by the **original view** (the one being modified, which is in the foreground). The background content adapts to this size.
*   **`.overlay(Content)`:**
    *   The `Content` view is placed *on top of* the view being modified.
    *   The overall size of the resulting combined view is determined by the **original view** (the one being modified, which is underneath). The overlay content adapts to this size.
    *   Has an `alignment` parameter to position the overlay content relative to the original view.

## aspectRatio

## Geometry Reader
- takes the available space
- give you data about available space so we can utilise


# Lecture Code

## LazyVGrid

- check `columns: [GridItem(), GridItem()]`, this is actually `columns: [GridItem(.flexible()), GridItem(.flexible())]`, as we expected, there will be 2 columns and this will take availble space.

```swift
LazyVGrid(columns: [GridItem(), GridItem()], spacing: 0) {
    ForEach(viewModel.cards) { card in
        CardView(card)
    }
}
```

- fixed: `GridItem(.fixed(100))` fixed size for item
- adaptive: `GridItem(.adaptive(minimum: 65))`
- **spacing**: spacing is explicitly set to zero as it will depend on platform, it can be on in iphone, different in apple watch. so Author is trying to put explicit padding.
```swift
LazyVGrid(columns: [GridItem(.adaptive(minimum: 65), spacing: 0)], spacing : 0) {
    forEach(viewModel.cards) { card in
        CardView(card)
            .padding(4)
    }
}
```

## Modifier order
```swift
HStack {
    // ...
}
.background(.yellow) // <---------------- bg will have padding
.padding()


HStack {
    //...
}
.padding()
.background(.yellow) // <----------------- bg will be filled

```

## Geometric Reader
We have a bunch of cards, we need to find the right width, so all the cards fit in the screen, there is no need for scrollview.

```swift
GeometryReader { geometry in
    // NOTE: this isnot a build in function, we need to define it
    let gridItemSize = gridItemWidthThatFits(
        count: viewModel.cards.count,
        size: geometry.size,
        atAspectRatio: 2/3
    )

    LazyVGrid(columns: [GridItem(.adaptive(minimum: gridItemSize), spacing : 0), spacing: 0]) {
        ForEach(viewModel.cards) { card in
            CardView(card)
                .aspectRatio(2/3)
                .padding(4)
        }
    }
}

// TODO: gridItemWidthThatFits Implemetation
// https://youtu.be/fYlMD9llu7w?t=2375
```

continue: https://youtu.be/fYlMD9llu7w?t=2583