# React Native Threading Model: Old Architecture vs New Architecture

Understanding **how threads communicate in React Native** is key to understanding performance issues like jank, blank screens, delayed taps, and why the **New Architecture** exists at all.

This post covers:
- How the **old Batched Bridge architecture** worked
- Why it caused real-world performance problems
- What **JSI, Fabric, and TurboModules** change
- How threading works in modern React Native

---

## Core Threads in React Native

Before comparing architectures, we need to understand the main threads involved.

### 1. JS Thread
- Executes **JavaScript**
- Runs React reconciliation, hooks, state updates, and business logic
- Handles JS event callbacks (`onPress`, `onScroll`, etc.)
- Single-threaded

> If the JS thread is blocked, **JS-driven updates and event handling stall**.

---

### 2. UI (Main) Thread
- Runs **UIKit** on iOS and **Android View system**
- Responsible for:
  - Creating native views
  - Applying layout
  - Drawing pixels
  - Handling touch input

> If the UI thread is blocked, **frames drop and scrolling becomes janky**.

---

### 3. Native / Background Threads
- Used for:
  - Native modules
  - IO, networking, storage
  - Async native work

---

## Old Architecture: Batched Bridge

### Key Idea

**JS and Native are isolated worlds** connected by an **asynchronous, batched, JSON-serialized Bridge**.

JS **cannot directly call native functions**.

---

## How the Old Bridge Works

### Characteristics
- **Asynchronous** (no synchronous JS → native calls)
- **Batched** (commands queued and flushed together)
- **JSON-serialized** (all data converted to JSON)
- **One-way per batch**

---

### Typical Old-Architecture Render Flow

```
JS Thread
  └─ React computes new Fiber tree
     └─ Generates UI commands
         └─ Batch + JSON serialize
             └─ Send over Bridge
                 └─ Native receives
                     └─ UI Thread applies updates
```

---

### Example: Updating a View

JS produces commands like:

```json
[
  ["createView", 42, "RCTView", { "flex": 1 }],
  ["updateProps", 42, { "backgroundColor": "red" }]
]
```

These commands are:
- Queued on the JS side
- Serialized to JSON
- Sent across the Bridge
- Deserialized on the native side
- Applied on the UI thread

---

## Why the Old Architecture Hurt Performance

### 1. Serialization Cost
- Large props (lists, styles, data blobs) require **expensive JSON encode/decode**
- Frequent updates amplify this cost

---

### 2. Asynchronous Boundary
- JS had **no guarantee when native would apply updates**
- Native could not synchronously query JS
- Made low-latency interactions difficult

---

### 3. Queue Backups
- If the JS thread was busy:
  - Commands piled up
  - UI updates arrived late
- If native was busy:
  - Bridge queue grew
  - Visual stutter appeared

---

### 4. No Shared Memory
- JS and native lived in **separate runtimes**
- Even small data had to be serialized

---

## Symptoms Developers Observed

- Fast scrolling → **blank areas**
- Heavy JS work → delayed taps
- Large lists → stutter
- JS-driven animations → dropped frames
- Native modules hard to optimize

---

## New Architecture: JSI + Fabric + TurboModules

### Core Goal

> **Remove the async JSON Bridge and replace it with a synchronous, C++-based interface.**

---

## 1. JSI (JavaScript Interface)

### What JSI Changes
- JS can **directly call C++ methods**
- No JSON serialization
- No message queues
- No Bridge

JS can hold references to **native/C++ objects**.

```
JS ⇄ C++ ⇄ Native
```

---

### Why JSI Matters
- Enables **synchronous calls** (when safe)
- Enables **shared memory**
- Enables modern animation systems
- Enables advanced scheduling

JSI is the foundation of the New Architecture.

---

## 2. Fabric (New Renderer)

### What Fabric Replaces
- Replaces the legacy `UIManager` renderer
- Designed for **Concurrent React**
- Shares more rendering logic in **C++**

---

### Fabric Rendering Flow

```
React Fiber (JS)
   ↓
Fabric Shadow Tree (C++)
   ↓
Layout via Yoga (C++)
   ↓
Mount Instructions
   ↓
Native UI Tree
```

---

### Fabric Improvements
- Deterministic mounting
- Better scheduling
- Reduced JS ↔ native communication
- Future-ready for Concurrent React features

---

## 3. TurboModules

### Old Native Modules
- Accessed via the Bridge
- Async-only
- Heavy serialization
- Slow startup

---

### TurboModules
- Built on JSI
- Can be **sync or async**
- Use **codegen** for type safety
- Faster startup and invocation

---

## Threading Model Comparison

### Old Architecture

```
JS Thread
   ↓ (batch + JSON)
Bridge
   ↓
Native Modules Thread
   ↓
UI Thread
```

---

### New Architecture

```
JS Thread
   ⇄ (JSI / C++)
Fabric & TurboModules
   ⇄
UI Thread
```

Still multiple threads — but **far less friction between them**.

---

## What Developers Feel in Practice

### Old Architecture
- JS busy → UI scrolls but content lags
- Frequent updates → stutter
- Complex native integrations → painful

---

### New Architecture
- Lower JS ↔ native overhead
- More predictable rendering
- Better support for:
  - Concurrent React
  - Modern animations
  - Large lists
  - High-frequency updates

---

## Key Takeaways

- React Native **always had multiple threads**
- The **Bridge** was the real bottleneck
- JSI removes serialization and async barriers
- Fabric modernizes rendering
- TurboModules modernize native APIs
- The New Architecture is foundational, not cosmetic

---

## Final Mental Model

```
Old React Native:
JS → JSON → Bridge → Native

New React Native:
JS ⇄ C++ ⇄ Native
```

