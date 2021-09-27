---
title: Implementing useBoolean hook
isPublicLesson: true
---

Okay. Let's implement the `useBoolean` hook.

Before we start we need to install `react` library. Let's add React as `devDependencies` and as `peerDependencies`:

```json
{
  "peerDependencies": {
    "react": ">=16.8.0"
  },
  "devDependencies": {
    "@types/react": "",
    "react": "16.13.1"
  }
}
```

Run `npm install`. This will install `react`. Note that when this library is published, React will not be installed automatically but will require the library user to have a version of React no lower than 16.8.0 (which added support for React Hooks API).

Once we have React installed we can start to implement our first `useBoolean` hook.

Let's create an array folder `./src/array` and a `useBoolean.ts` file inside it.

We start with types first.

`useBoolean` is the hook that takes the initial state as a parameter, and returns an array with two elements. The first element is our current value, and the second is a list of possible actions: `setTrue`, `setFalse` and `toggle` actions.

Let's create the `UseBooleanFunction` type with the signature of the hook API we want to get. To keep our users happy, it's also good practice to create and export the type of `useBoolean` function.

```ts
import React, { useState, SetStateAction } from 'react';

type UseBooleanFunction = (initial: boolean) => [boolean, UseBooleanActions];

export type UseBooleanActions = {
  setValue: React.Dispatch<SetStateAction<boolean>>;
  toggle: VoidFunction;
  setTrue: VoidFunction;
  setFalse: VoidFunction;
};

export type UseBoolean = [boolean, UseBooleanActions];
```

Let's implement the hook itself now, starting with naive implementation.

```ts
import React, { useState, SetStateAction } from 'react';

type UseBooleanFunction = (initial: boolean) => [boolean, UseBooleanActions];

export type UseBooleanActions = {
  setValue: React.Dispatch<SetStateAction<boolean>>;
  toggle: VoidFunction;
  setTrue: VoidFunction;
  setFalse: VoidFunction;
};

export type UseBoolean = [boolean, UseBooleanActions];

export const useBoolean: UseBooleanFunction = (initial) => {
  const [value, setValue] = useState<boolean>(initial);
  const toggle = () => setValue((v) => !v);
  const setTrue = () => setValue(true);
  const setFalse = () => setValue(false);
  const actions = { setValue, toggle, setTrue, setFalse };
  return [value, actions];
};

export default useBoolean;
```

All good. Let's test it! I'm using [codesandbox.io](https://codesandbox.io/) and good old copy-paste.

Open codesandbox.io and then click on the Create Sandbox button in the header. Search for React Typescript template and select it. After that copy-paste our code into the sandbox and check if it works.

```tsx
import * as React from "react";
import { useState } from "react";

type UseBooleanFunction = (initial: boolean) => [boolean, UseBooleanActions];

export type UseBooleanActions = {
  setValue: React.Dispatch<React.SetStateAction<boolean>>;
  toggle: VoidFunction;
  setTrue: VoidFunction;
  setFalse: VoidFunction;
};

export type UseBoolean = [boolean, UseBooleanActions];

export const useBoolean: UseBooleanFunction = (initial) => {
  const [value, setValue] = useState<boolean>(initial);
  const toggle = () => setValue((v) => !v);
  const setTrue = () => setValue(true);
  const setFalse = () => setValue(false);
  const actions = { setValue, toggle, setTrue, setFalse };
  return [value, actions];
};

const OpenClosedSign = () => {
  const [isOpen, isOpenActions] = useBoolean(true);
  return (
    <div>
      <button onClick={isOpenActions.toggle}>Toggle</button>
      {isOpen ? "Open!" : "Closed!"}
    </div>
  );
};

export default OpenClosedSign;
```

It's all working nicely - although there is one major flaw! We do not memoize our callback functions, nor our `isOpenActions` object, and that causes unnecessary re-renders. Let me show by example. I'm going to create a component that shows the timestamp of its last render.

```tsx
const Dummy = React.memo(() => {
  return <div>{new Date().getTime()}</div>;
});
```

As you can see it's a memoized component, meaning it should re-render only when props that are passed into it are changed.

Let's render it, without any props passed for now.

```tsx
return (
  <div>
    <Dummy />
    <button onClick={isOpenActions.toggle}>Toggle</button>
    {isOpen ? "Open!" : "Closed!"}
  </div>
);
```

If we click it, you can see that it never re-renders. Good, but what if we pass one of our callbacks down to the onClick prop?

```tsx
<Dummy onClick={isOpenActions.toggle} />
```

It re-renders every time our `OpenClosedSign` component gets re-rendered... but our callback never changes!  How can we fix that? 

If you're not familiar with React Hooks, here's the trick.   There is a `useCallback` hook backed into React itself that takes two parameters, the callback and the dependencies of that callback (in other words, what functions or values this callback is going to use from its scope outside.)

So let's wrap our toggle callback into `useCallback` like this:

```tsx
const toggle = useCallback(() => setValue((v) => !v), []);
```

As you can see, if we toggle the state of `isOpen`, the references of our callback functions stay the same, keeping our memoized component from useless re-renders.

So, let's wrap all the callback functions with `useCallbacks`:

```tsx
const toggle = useCallback(() => setValue((v) => !v), []);
const setTrue = useCallback(() => setValue(true), []);
const setFalse = useCallback(() => setValue(false), []);
```

And now let's test whether it's working for each of the callbacks by passing them all.

The last thing that we need to fix is the case when we pass actions down as props. Let see how it behaves now. 

As you can see it re-renders again on each re-render of the parent, which is something we want to avoid. To fix this we need to use the `useMemo` hook from React. It takes two parameters, the first being the callback that will be called, with its return memoized, and the second being, again, a list of dependencies.

Here is how we do it.

```ts
const actions = useMemo(() => ({ setValue, toggle, setTrue, setFalse }), [
  setFalse,
  setTrue,
  toggle,
]);
```

Let's test it. Awesome!

The last improvement would be to also memoize the returning array. Not particularly useful, but we ought to do it. 

```ts
return useMemo(() => [value, actions], [actions, value]);
```

This is what we should end up with: 

```ts
import { default as React, SetStateAction, useCallback, useMemo, useState } from "react";

type UseBooleanFunction = (initial: boolean) => [boolean, UseBooleanActions];

export type UseBooleanActions = {
  setValue: React.Dispatch<SetStateAction<boolean>>;
  toggle: VoidFunction;
  setTrue: VoidFunction;
  setFalse: VoidFunction;
};

export type UseBoolean = [boolean, UseBooleanActions];

export const useBoolean: UseBooleanFunction = (initial) => {
  const [value, setValue] = useState<boolean>(initial);
  const toggle = useCallback(() => setValue((v) => !v), []);
  const setTrue = useCallback(() => setValue(true), []);
  const setFalse = useCallback(() => setValue(false), []);
  const actions = useMemo(() => ({ setValue, toggle, setTrue, setFalse }), [
    setFalse,
    setTrue,
    toggle,
  ]);
  return [value, actions];
};

export default useBoolean;
```

In the next lesson we're going to write unit tests for all these use cases to speed up our iteration. Copy and pasting is ok, but it would be better to automate our tests.
