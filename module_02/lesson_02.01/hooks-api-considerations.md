---
title: Hooks API Considerations
isPublicLesson: true
---


If you're a familiar user of `react-hanger` you may notice that some of its hooks have two different APIs, one returning an array and another returning an object.

"What's the big deal?" you may ask. 

 A good rule when you're developing certain custom hooks APIs (mostly for those that hold and expose some state) is to keep them as close to the original React Hooks API as possible.

Let's look at a simple example using simple `useState`:

```jsx
import { useState, useCallback } from "react";

const OpenClosedSign = () => {
  const [isOpen, setOpen] = useState(true);

  const toggleOpen = useCallback(() => {
    setOpen((prevOpen) => !prevOpen);
  });

  return (
    <div>
      <button onClick={toggleOpen}>Toggle</button>
      {isOpen ? "Open!" : "Closed!"}
    </div>
  );
};
```

Good, all clear! There is a little boilerplate needed in order to keep reference equality of `toggleOpen` between renders, so our child components are not re-rendering for no reason. This snippet can be improved by using the custom `useBoolean` hook from `react-hanger` (we're going to implement it in the next lesson).

```jsx
import { useBoolean } from "react-hanger/array";

const OpenClosedSign = () => {
  const [isOpen, isOpenActions] = useBoolean(true);
  return (
    <div>
      <button onClick={isOpenActions.toggle}>Toggle</button>
      {isOpen ? "Open!" : "Closed!"}
    </div>
  );
};
```

This is a pretty obvious API which feels like it's part of React itself, with the state on the left that is changing and our actions, setters, methods etc on the right.

The object-returning version of the same hook is shown here: 

```jsx
import { useBoolean } from "react-hanger";

const OpenClosedSign = () => {
  const { value: isOpen, toggle: toggleOpen } = useBoolean(true);
  return (
    <div>
      <button onClick={toggleOpen}>Toggle</button>
      {isOpen ? "Open!" : "Closed!"}
    </div>
  );
};
```

Why two versions? For some people, it's just a matter of preference, but there can be more to it. Consider these two snippets.

```js
const [showHeader, showHeaderActions] = useBoolean(true);
const [showFooter, showFooterActions] = useBoolean(true);
```

And

```js
const { value: showHeader, ...showHeaderActions } = useBoolean(true);
const { value: showFooter, ...showFooterActions } = useBoolean(true);
```

It looks like they are pretty much identical, but they're not.

In the first snippet, you can pass all the actions as a whole and separately, without unnecessary re-renders, because they are memoized under the hood, and the same reference is always returned.

In the second, we're taking the `value` out of the returned object and spreading the rest of the object properties that represent our actions into `showHeaderActions`. The problem here is that every time our component re-renders, `showHeaderActions` and `showFooterActions` will have a different reference. For the first snippet reference equality is never a concern.

In conclusion, the array version of the API is better because it:

- feels closer to React's `useState` hook and is hence easier to understand
- always returns the same reference for all actions avoiding unneeded re-renders of its children
- is slightly shorter than the object version of the API

With that in mind let's implement `useBoolean` ourselves.
