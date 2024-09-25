# Conditional Rendering for mutually exclusive states

I was reading this [blog](https://tkdodo.eu/blog/component-composition-is-great-btw), one point struck me. Avoid conditional rendering for mutually exclusive states. I always felt this way. It's extremely difficult to read through a component which has a lot of conditionals. May be it has mutually exclusive stuff, put in a switch may be. But don't confuse the user.

## Example
Taken from the blog.
```js
function Layout(props: { children: ReactNode; title?: string }) {
  return (
    <Card>
      <CardHeading>Welcome 👋 {title}</CardHeading>
      <CardContent>{props.children}</CardContent>
    </Card>
  )
}

export function ShoppingList() {
  const { data, isPending } = useQuery(/* ... */)

  if (isPending) {
    return (
      <Layout>
        <Skeleton />
      </Layout>
    )
  }

  if (!data) {
    return (
      <Layout>
        <EmptyScreen />
      </Layout>
    )
  }

  return (
    <Layout title={data.title}>
      {data.assignee ? <UserInfo {...data.assignee} /> : null}
      {data.content.map((item) => (
        <ShoppingItem key={item.id} {...item} />
      ))}
    </Layout>
  )
}
```