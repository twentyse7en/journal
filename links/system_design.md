# System Design

## [Avoid premature abstraction with unstyled react components](https://buildui.com/posts/avoiding-premature-abstraction-with-unstyled-react-components)
Title itself is enough. While creating generic components don't pass styles.  

The extend to which we should do is depend upon the design system. For button it always works.
create a unstyled generic `<Button/>`. There could be primary and secondary button, create `<PrimaryButton>, <SecondaryButton />` wrapping `<Button />`.
Every time we need a new button, we don't need to mess with `<Button />`.   

This follow `O` of `S.O.L.I.D` priniciple
