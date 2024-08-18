# Repeating a Svg

In html we can do a background repeat to repeat a image, but in react native we don't have that option.  

In css
```css
  background-image: url("paper.gif");
  background-repeat: repeat-x;
```

## Alternative solution using svg

Create a SVG pattern and repeat it.   
NOTE: if you are trying to reuse the svg with different fill color it won't be possible. Create a new svg with a different **pattern id**. 
```svg
const Boundary = () => (
  <Svg height='8' width='100%' fill='none'>
    <Defs>
      <Pattern id='pattern' patternUnits='userSpaceOnUse' width={20} height={8}>
        <Path
          fill={'#EAECF0'}
          d='M1.923 2.472 0 4V0h20v4l-1.778 1.452a5 5 0 0 1-6.274.042L10.069 4 8.145 2.472a5 5 0 0 0-6.222 0Z'
        />
      </Pattern>
    </Defs>
    <Rect x='0' y='0' width='100%' height='100%' fill='url(#pattern)' />
  </Svg>
);
```
