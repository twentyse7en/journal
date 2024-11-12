# React Compiler
todo - need to write more

# tldr;
- we don't need to use `useMemo`, `useCallback` or `React.memo`
- compiler will cache for us
- what is our duty, write react with rules of react, then use react compiler

# interesting part
- if there is jsx part which isn't changing, it will also cache, this we won't generally do.
- todo: check the transpiled code to get example

# Reference
- https://react.dev/learn/react-compiler
- [playground](https://playground.react.dev/#N4Igzg9grgTgxgUxALhAMygOzgFwJYSYAEAYjHgpgCYAyeYOAFMEWuZVWEQL4CURwADrEicQgyKEANnkwIAwtEw4iAXiJQwCMhWoB5TDLmKsTXgG5hRInjRFGbXZwB0UygHMcACzWr1ABn4hEWsYBBxYYgAeADkIHQ4uAHoAPksRbisiMIiYYkYs6yiqPAA3FMLrIiiwAAcAQ0wU4GlZBSUcbklDNqikusaKkKrgR0TnAFt62sYHdmp+VRT7SqrqhOo6Bnl6mCoiAGsEAE9VUfmqZzwqLrHqM7ubolTVol5eTOGigFkEMDB6u4EAAhKA4HCEZ5DNZ9ErlLIWYTcEDcIA)
- https://www.youtube.com/watch?v=PYHBHK37xlE