# iOS Text Overflow Notes
written by ai, verified by hooman.

## Bug 1: Text overflow in row layouts on iOS

### Example problem

Consider a horizontal row with:

- a fixed-size icon
- a long text node placed directly in the row

On iOS, text inside a `flexDirection: 'row'` layout can keep its intrinsic width unless it is explicitly placed inside a shrinkable container. When that happens, the text may wrap visually but still paint outside the card or container bounds.

### Before

```text
+--------------------------------------+
| [icon] [very long text keeps         |
|        intrinsic width and can       |
|        paint outside the card ...... |
+--------------------------------------+
```

### Why padding did not fix it

Adding left or right padding changes spacing, but it does not change the core layout rule that decides whether the text is allowed to shrink.

So this kind of workaround:

- may look slightly better on one device
- does not fix the underlying row sizing behavior
- can still break on iOS

### Fix

Wrap the text in a container that is explicitly allowed to shrink:

- icon container: `flexShrink: 0`
- text container: `flex: 1`, `minWidth: 0`

### After

```text
+--------------------------------------+
| [icon] [text container flex:1        |
|        minWidth:0                    |
|        text now wraps inside card]   |
+--------------------------------------+
```

### Pattern

```tsx
<Row alignItems='center'>
  <IconContainer style={{flexShrink: 0}} />
  <TextContainer style={{flex: 1, minWidth: 0}}>
    <UcText>...</UcText>
  </TextContainer>
</Row>
```

### Practical rule

When text overflows inside a horizontal row in React Native, especially on iOS:

1. Do not start with padding hacks.
2. Keep the icon fixed.
3. Put the text inside a shrinkable wrapper with `flex: 1` and `minWidth: 0`.

## Bug 2: Keep `15 minutes` together

### Example problem

Sometimes a short phrase should stay together and not split across lines like this:

```text
15
minutes
```

### Fix

Use a non-breaking space:

```tsx
{'15\u00A0minutes'}
```

### Diagram

```text
Normal space:
"15 minutes"
 -> can wrap between words

Non-breaking space:
"15\u00A0minutes"
 -> wraps only before or after the whole phrase
```

### Practical rule

If a short phrase must stay together in UI text, use `\u00A0` instead of relying on layout behavior.

## Summary

### For row text overflow

- Parent row: `flexDirection: 'row'`
- Icon: fixed-size container, `flexShrink: 0`
- Text: wrapper with `flex: 1` and `minWidth: 0`

### For unbreakable text phrases

- Use non-breaking space, for example: `{'15\u00A0minutes'}`
