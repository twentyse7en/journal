# React Native: From JSX to Pixels  
*(Old Bridge vs New Architecture with JSI)*

This document explains **step by step**:
1. How React Native JSX becomes pixels on the screen
2. What happens internally when a user clicks a button (`onPress` / `onClick`)
3. Differences between **Old Bridge architecture** and **New Architecture (JSI, Fabric, TurboModules)**

---

## 1. From JSX to Screen (Old Bridge Architecture)

### A. App Startup
1. Native app (iOS/Android) launches.
2. Native initializes a **JavaScript runtime** (JavaScriptCore or Hermes).
3. React Native loads the JS bundle.
4. `AppRegistry.runApplication()` is called.

---

### B. React Rendering (JS Thread)
5. React executes component functions.
6. JSX is converted into **React elements** (plain JavaScript objects).
7. React **reconciler** compares the new element tree with the previous one (diffing).

---

### C. Shadow Tree & Layout (JS-managed)
8. React Native creates/updates a **Shadow Tree** (JS representation of native views).
9. Layout is calculated using **Yoga** (Flexbox layout engine).

---

### D. Bridge Communication (Async, Batched)
10. React Native generates UI commands such as:
    - Create native view
    - Update props/styles
    - Attach child views
11. These commands are **serialized** (JSON-like) and sent over the **Bridge**.
12. Communication is **asynchronous and batched**.

---

### E. Native UI Mounting
13. Native receives the command batch.
14. Native creates real platform views (`UIView` / `android.view.View`).
15. Views are added to the native view hierarchy.
16. OS renders the views → **User sees pixels on screen**.

---

### Old Bridge Characteristics
- Async & batched communication
- Serialization/deserialization overhead
- JS thread and Native UI thread are decoupled
- UI updates may lag if JS thread is busy

---

## 2. Button Click Flow (Old Bridge)

### A. Touch Detection (Native)
1. User taps the screen.
2. OS delivers touch events to the native view.
3. React Native’s native touch system identifies the target view.

---

### B. Event Sent Over Bridge
4. Native packages event data (view tag, event name, payload).
5. Event is queued and sent over the **Bridge** to the JS thread.

---

### C. JS Event Handling
6. JS event emitter receives the event.
7. React calls the `onPress` / `onClick` handler.
8. Your JavaScript handler executes.

---

### D. State Update & Re-render
9. `setState` / state update triggers React re-render.
10. New UI commands are generated.
11. Commands are sent back to native via the Bridge.
12. Native applies changes → updated UI appears.

---

### Old Bridge Limitation
- If JS thread is blocked, button clicks feel delayed.
- Every event and UI update must cross the Bridge.

---

## 3. From JSX to Screen (New Architecture: JSI + Fabric)

### A. App Startup
1. Native app launches.
2. JS runtime (usually **Hermes**) is created.
3. **JSI bindings** and **Fabric renderer** are initialized.

---

### B. React Rendering
4. React executes component functions.
5. JSX becomes React elements.
6. React reconciler prepares updates.

---

### C. Fabric Shadow Tree (C++ Core)
7. Updates are applied to a **Fabric Shadow Tree** (managed in C++).
8. Layout is computed (Yoga still used internally).

---

### D. Commit & Mount
9. React performs a **commit phase**, producing a list of mutations.
10. Fabric mounts changes directly to native views.
11. Native UI hierarchy is updated.
12. OS renders → **User sees pixels**.

---

### New Architecture Improvements
- No JSON-based bridge for UI updates
- Shared C++ core across platforms
- Faster UI updates and better scheduling
- Less overhead and memory copying

---

## 4. Button Click Flow (New Architecture)

### A. Touch Detection
1. User taps screen.
2. OS sends touch event to native view.
3. Fabric event system identifies the target.

---

### B. Event Delivery via JSI
4. Event flows through C++ core.
5. Event is delivered to JS via **JSI** (not old bridge).
6. React invokes the `onPress` / `onClick` handler.

---

### C. State Update & Fabric Commit
7. Handler executes on JS thread.
8. State update triggers React reconciliation.
9. Fabric commit produces UI mutations.
10. Native views are updated.
11. Updated UI is rendered on next frame.

---

### New Architecture Benefits
- Faster event delivery
- Reduced serialization cost
- More predictable UI updates
- Still JS-thread dependent for handler execution

---

## 5. NativeModules vs TurboModules

### Old NativeModules (Bridge-based)
- Calls go through the Bridge
- Arguments/results are serialized
- Mostly asynchronous
- Higher overhead

---

### TurboModules (JSI-based)
- Exposed via JSI
- Direct JS ↔ Native interaction
- Less serialization
- Better performance and type safety
- Uses codegen for consistency

---

## 6. Key Takeaways for Developers

- `onPress` / `onClick` always runs on the **JS thread**
- Blocking JS causes UI lag in **both architectures**
- Avoid heavy computation in event handlers
- Use:
  - Batched state updates
  - Background/native threads for heavy work
  - Native-driven animations (e.g., Reanimated)

---

## Summary

| Area | Old Bridge | New Architecture |
|-----|-----------|------------------|
| UI Updates | Serialized over Bridge | Fabric + C++ |
| Event Delivery | Bridge-based | JSI-based |
| Performance | Slower, more overhead | Faster, leaner |
| Architecture | JS ↔ Native | JS ↔ C++ ↔ Native |

---

**Mental Model:**  
JSX → React Elements → Reconciler → Shadow Tree → Native Views → Pixels  
Tap → Native Event → JS Handler → State Update → UI Update

---
