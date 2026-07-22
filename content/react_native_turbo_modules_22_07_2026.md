# How to create React Native TurboModules

## Motivation
- I thought of contributing to some react native projects
- I'm using this [keyboard lib](https://github.com/kirillzyusko/react-native-keyboard-controller/) so thought of contributing to it
- Oh manh! I don't know all these files and conventions
- So as a first step I'm learning how to create a turbo module

## Intro
- with react native's new arch we don't use some bridge to send data as json, we can directly call the underlying native code
- It's super fast and making react native relavant and good choice
- so let's see how it's working


## Before You Begin
- [**Codegen**](https://reactnative.dev/docs/the-new-architecture/what-is-codegen): reads your typed TypeScript or Flow definitions for native modules and components, then automatically generates the C++, Android (Java), and iOS (Objective-C++) glue code needed to connect JavaScript with native code. It mainly removes repetitive boilerplate when building Turbo Native Modules and Fabric Native Components.
- [**React-native-builder-bob**](https://github.com/callstack/react-native-builder-bob): A tool to create turbo module from callstack


## Getting started
We are going to build a simple local storage, where you can store data to device like local storage in browser.

1. Create a expo project
```sh
npx create-expo-app@latest turbo-modules
```

2. Scaffold a new React Native library with everything pre-configured.
```sh
npx create-react-native-library@latest --local local-storage
```
- Keep the default options and select turbo modules in the setup.
- in modules you will be able to see local storage folder
- local storage will have a placeholder multiply button

## Exploring module
There is three main folder: src, android and ios
### SRC
```ts
import { TurboModuleRegistry, type TurboModule } from 'react-native';

export interface Spec extends TurboModule {
  multiply(a: number, b: number): number;
}

export default TurboModuleRegistry.getEnforcing<Spec>('LocalStorage');
```
- `NativeLocalStorage.ts`: this is the most important file
- it is the contract that JavaScript and both native platforms must follow.
- It declares a module with one synchronous method
- Extending `TurboModule` tells React Native Codegen to generate the native base types. 
- `getEnforcing<Spec>('LocalStorage')` retrieves the registered native module and throws an understandable error if it is missing from the app build.

**index.tsx**
- This is the package's public JavaScript API.

**multiply.native.tsx**
- Right now, this file is a small JavaScript wrapper around the actual Turbo Module.
- In here you can invoke native module and do javascript part.





# Reference
- [Callstack Playlist on yt](https://www.youtube.com/playlist?list=PLZ3MwD-soTTE9yIH2ziaxBO6ytManw5Qc)