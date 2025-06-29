# React useTransition

## Background: Batch State Updates in Modern React

Modern React automatically **batches multiple state updates** together to improve performance. This means that when you trigger several state changes (for example, inside an event handler), React **waits for all state updates to complete** before updating the UI in a single render pass. However, **not all updates are equally important**—some are urgent (like user input), while others are non-urgent (like rendering a large list).

By default, **React treats all updates in a batch with the same priority**, which can sometimes cause the UI to feel sluggish if a slow update blocks an urgent one. This is where `useTransition` comes in: it lets you mark certain updates as non-urgent, so React can prioritize keeping the UI responsive for the user.

## What is `useTransition`?

`useTransition` is a React Hook that lets you **mark certain state updates as non-urgent**. It allows you to keep your app responsive by letting urgent updates (like typing or clicking) take priority over slower, non-urgent updates (like rendering a large list).

With `useTransition`, you can specify any state updates as non-urgent. These non-urgent state updates will still occur simultaneously with other urgent updates, but the **UI component will be rerendered even if the non-urgent update is still in progres**s. This means that while the non-urgent update is happening, the user can still interact with the UI without feeling blocked or delayed.

## When to Use It

Use `useTransition` when you have **UI updates that are expensive or slow** (e.g., filtering a large list, rendering lots of components) and you **want to avoid blocking more urgent interactions**. It's best for situations where you want to **show immediate feedback** (like a spinner) while a heavy update is happening in the background.

## Why Use It (Benefits)

- Keeps the UI responsive during heavy updates
- Prevents blocking user input
- Improves perceived performance by showing loading states

## Example: Filtering a Large List

### Without `useTransition`
```jsx
function SearchList({ items }) {
  const [query, setQuery] = React.useState('');
  const filteredItems = items.filter(item => item.includes(query));

  return (
    <>
      <input value={query} onChange={e => setQuery(e.target.value)} />
      <ul>
        {filteredItems.map(item => <li key={item}>{item}</li>)}
      </ul>
    </>
  );
}
```
*Typing in the input may feel sluggish if `items` is large, because filtering happens on every keystroke.*

### With `useTransition`
```jsx
import { useTransition } from 'react';

function SearchList({ items }) {
  const [query, setQuery] = React.useState('');
  const [filteredItems, setFilteredItems] = React.useState(items);
  const [isPending, startTransition] = useTransition();

  function handleChange(e) {
    const value = e.target.value;
    setQuery(value);
    startTransition(() => {
      setFilteredItems(items.filter(item => item.includes(value)));
    });
  }

  return (
    <>
      <input value={query} onChange={handleChange} />
      {isPending && <span>Loading...</span>}
      <ul>
        {filteredItems.map(item => <li key={item}>{item}</li>)}
      </ul>
    </>
  );
}
```
*With `useTransition`, the input stays responsive and a loading indicator appears while filtering happens in the background.*
