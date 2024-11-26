# React Compiler

We don't need to explicitly write `useMemo`, `useCallback`, `React.memo`, React compiler will add this for you. But you have to follow [rules of React](https://react.dev/reference/rules), there is also a eslint plugin that will help you with this.

# Compiler Example
Just compiling few example and trying to get the core concepts.

# Example 1
Code before memoization
```jsx
function FriendList(props) {
  return (
    <div>
      <h1>hello</h1>
      <MessageButton onClick={props.action} />
    </div>
  );
}
```
Code after memoization
```js
function FriendList(props) {
  const $ = _c(3);
  let t0;
  if ($[0] === Symbol.for("react.memo_cache_sentinel")) {
    t0 = <h1>hello</h1>;
    $[0] = t0;
  } else {
    t0 = $[0];
  }
  let t1;
  if ($[1] !== props.action) {
    t1 = (
      <div>
        {t0}
        <MessageButton onClick={props.action} />
      </div>
    );
    $[1] = props.action;
    $[2] = t1;
  } else {
    t1 = $[2];
  }
  return t1;
}
```

Breaking down into pieces
- Whole jsx is broken down to pieces. `<h1>hello</h1>` only need to create once. Once cached we will reuse everytime.
- `props.action` is memoized. If this value changes then we re-render `<MessageButton />`.

# Interesting part
- if there is jsx part which isn't changing, it will also cache, this we won't generally do.
- todo: check the transpiled code to get example

# Misc
Don't need the function to be memoized, you can opt out.
```jsx
function SuspiciousComponent() {
 "use no memo";
}
```

For the existing project you can configure to cache a specific [directory](https://react.dev/learn/react-compiler#existing-projects). If it work you can expand to other directory.

# tldr;
- we don't need to use `useMemo`, `useCallback` or `React.memo`
- compiler will cache for us
- what is our duty, write react with rules of react, then use react compiler


# Reference
- https://react.dev/learn/react-compiler
- [playground](https://playground.react.dev/#N4Igzg9grgTgxgUxALhAMygOzgFwJYSYAEAYjHgpgCYAyeYOAFMEWuZVWEQL4CURwADrEicQgyKEANnkwIAwtEw4iAXiJQwCMhWoB5TDLmKsTXgG5hRInjRFGbXZwB0UygHMcACzWr1ABn4hEWsYBBxYYgAeADkIHQ4uAHoAPksRbisiMIiYYkYs6yiqPAA3FMLrIiiwAAcAQ0wU4GlZBSUcbklDNqikusaKkKrgR0TnAFt62sYHdmp+VRT7SqrqhOo6Bnl6mCoiAGsEAE9VUfmqZzwqLrHqM7ubolTVol5eTOGigFkEMDB6u4EAAhKA4HCEZ5DNZ9ErlLIWYTcEDcIA)
- https://www.youtube.com/watch?v=PYHBHK37xlE
