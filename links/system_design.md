# System Design

## [Avoid premature abstraction with unstyled react components](https://buildui.com/posts/avoiding-premature-abstraction-with-unstyled-react-components)
Title itself is enough. While creating generic components don't pass styles.  

The extend to which we should do is depend upon the design system. For button it always works.
create a unstyled generic `<Button/>`. There could be primary and secondary button, create `<PrimaryButton>, <SecondaryButton />` wrapping `<Button />`.
Every time we need a new button, we don't need to mess with `<Button />`.   

This follow `O` of `S.O.L.I.D` priniciple

## [Refactoring a messy react component](https://alexkondov.com/refactoring-a-messy-react-component/)
Not advanced stuff, more like for beginners
- move utils outside of component.
- use `useQuery` package
- use package for form validation
- don't use `useEffect` unless very necessary
- split into smaller components
