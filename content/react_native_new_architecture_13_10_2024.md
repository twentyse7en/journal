# React Native: New Architecture

Here we are trying to explore the issues with current react native architecture and what's exciting about new architecture.

## What is React Native?
React native allows developers to create native apps. Native apps can be android, ios, tv, web. There tagline itself is "Learn once, write anywhere". How are they making this possible?

<img width="600" src="/content/images/what_is_rn.png" />

- Developer can write business logic in javascript just like react.
- For building UI, React Native provide building blocks like View
- For using hardware and other resources there will be third party libraries (for using camera and gps)

With all this we can build each platform. You may need to adjust few things according to each platform.

*Other Stuffs going under the hood* : The Javascript part will be compiled by Metro bundler (something like webpack, but optimized for mobile usecase). Java (android) and Obj-C (ios) will be compiled and both of them together form the App bundle, that user can install. Javascript run in Javascript Virtual Machine, previous it was JavascriptCore (the engine used by safari browser), now it is Hermes (Meta created it's own Javascript engine). Native stuff run in Native side.

As Javascript and Native stuff are independent, with codepush (which is going to deprecated soon) or alternative solution, javascript only changes we can release OTA, no release is required through playstore or appstore. That's a cool part.

<img width="600" src="/content/images/rn_files.png" />

## Issues with current Architecture

Javascript run in one thread and native stuff run in another thread. The communication happen by react native bridge. 

Let's go by an example:  Consider the user is clicking on a button.

1. Message is passed to javascript script thread, in JSON format
2. Javascript Thread receives the message, trigger the handler function
3. Based on the new changes, a new message is sent to Native side for updating the UI.
4. Native thread receives the message and update the UI.

<img width="600" src="/content/images/rn_bridge.png" />

The interaction between two sides are via bridge through message transfer, which is asynchrounous. There will be lot of overhead for JSON serialiazation, deserialization. As you can see already, if there is lot of interaction app can lag. For example, when you are scrolling a long list of data you can often see white screen.

Bridge is *async*, changes passed might not immediately reflected. This means that users may see intermediate states or visual jumps between rendering the initial layout and responding to layout measurements.

For Example: While entering Amount add comma seperation.

<img width="600" src="/content/images/ui_slow_update.png" />

Another example is positioning tooltip based on some layout calculation. `useLayoutEffect` is broken, there is no way currently to do this without user seeing intermediate view.

### limitation of current architecture
- Communication view Bridge is slow
- can't synchronously update UI
- All modules load at startup which increases time to interaction.

## New architecture
<img width="600" src="/content/images/new_architecture.png" />

### Javascript Interface

### Turbogen

### Fabric

### Codegen