# React keys: Alternative use

## Intro
most commont use of `key` is when React Component is used inside a map

```jsx
{
    list.map((item) => <Card key={item.id}>)
}
```

## Alternative use
when ever the key change, the component can mount and un-mount which is very powerful.

```jsx
const Parent = () => {
    // set this with api call or something
    const [name, setName] = useState('');

    return <Child />
}


const Child = ({name}) => {
    const [localName, setLocalName] = useState(name);

    // syncing changes from parent
    useEffect(() => {
        setLocalName(name)
    }, [name]);
}
```

another way to do this is by `key`

```jsx
const Parent = () => {
//....

return <Child key={name} />
}
```

whenever the name changes in parent child will be unmounted and mounted, states are initialized again.

## Ref
- [Sync state in react](https://www.brenelz.com/posts/synchronizing-state-in-react/)
