---
title: Testing useBoolean hook
isPublicLesson: true
---

First of all, we need to install all the required dependencies in `package.json` to test our hooks, and delete the useless `index.test.ts` from the root.

```json
{
  "devDependencies": {
    "@testing-library/react-hooks": "3.3.0",
    "react-dom": "^16.8.0",
    "react-testing-library": "^16.8.0"
  }
}
```

If you're not familiar with the `@testing-library` set of libraries, they are basically a set of wrappers around DOM selectors and other custom selectors that make testing more user-behavior-centric.

By "user-behavior-centric" I mean its API is designed in such a way that components are selected by user-centric terms like `findByLabel` or `findByText` rather than `React.displayName` or plain old CSS selector. Tests will look like a high-level user interaction scenario. These scenarios are way more important to the user, and hence the developer, than any of the implementation details, which they wouldn't be losing any sleep over. 

It's about letting your tests give you the confidence that they are intended to. Since implementation details are not under test, we can easily refactor our components or even change an underlying framework without changing any of the test cases and assertions, changing only the preparation phase of the tests.

These libraries are absolute gold, and if you're still using `enzyme` for unit testing your React components, you would do well to change to `@testing-library/react` as soon as possible.

If we're testing functions (meaning unit tests rather than integration tests) we need to go down to the raw variables, reference comparisons etc. In the case of hooks testing, to avoid rendering some dummy components, we're going to use `@testing-library/react-hooks` package, which is essentially a set of helpers to test hooks in isolation. The authors of the library recommend, and I concur, that you should only use that for highly reusable hooks or hooks libraries such as we're writing now. In other scenarios you should test the whole component instead, using just `@testing-library/react`.

Okay. I think we've cleared everything up here, so let's start writing tests!

We're going to create `useBoolean.test.ts` file in `__tests__` folder, alongside our `useBoolean.ts` file.

We should add a `describe` block, and all cases need to be checked using `it` blocks.

```ts
describe("useBoolean array", () => {});
```

Now let's write the first case inside the `describe` block: that our hook should set true when we call `setTrue` action. This is how it will look:

```ts
import { renderHook, act } from "@testing-library/react-hooks";
import { useBoolean } from "./useBoolean";

describe("useBoolean array", () => {
  it("should set true", () => {
    const { result } = renderHook(() => useBoolean(false));
    const [, actions] = result.current;

    expect(result.current[0]).toBe(false);

    act(() => actions.setTrue());

    expect(result.current[0]).toBe(true);
  });
});
```

- First we render our hook, using the `renderHook` helper function from `@testing-library/react-hooks` library. 

- It returns an object with `result` field. This result field is a React ref, so in order to get its value we have to read its `current` field. Inside `current` there will be a result of `useBoolean` hook.

- We double check our initial state is set correctly to false. Then we call our `setTrue` action, wrapping it into the `act` function that makes sure all the changes to the state will be flushed into fake DOM before we run any assertions below it.

- Then we check our boolean value is indeed `true` and we're done with the first test!

You may be confused as to why we need the `act` function, but usage of `act` is a fairly complicated topic which would require another course to explain! In short, if any action or function call is going to change any React state in the component or hook under test, you have to wrap it in `act` function. The `act` function notifies React that you want all state changes to be flushed back into DOM before doing any assertions. If you forget, React will raise an error, so it's really hard to miss.

Note that most of the helper functions that are in `@testing-library/react` that we're not going to use in this course, are already wrapped in `act` by default, so you can ignore this rule for most cases using this library.

Okay, let's test the rest of the actions. Copy and paste and a little editing will do.

```ts
import { useBoolean } from "./useBoolean";

it("should set false", () => {
  const { result } = renderHook(() => useBoolean(true));
  const [, actions] = result.current;

  expect(result.current[0]).toBe(true);

  act(() => actions.setFalse());

  expect(result.current[0]).toBe(false);
});

it("should toggle", () => {
  const { result } = renderHook(() => useBoolean(true));
  const [, actions] = result.current;
  expect(result.current[0]).toBe(true);

  act(() => actions.toggle());

  expect(result.current[0]).toBe(false);

  act(() => actions.toggle());
  expect(result.current[0]).toBe(true);
});
```

Good. Everything is green and beautiful.

The last thing that we need to test is that our optimizations using `useCallback` and `useMemo` are working as expected, and that reference equality is kept after the state actually changes.

Let's make another `describe` block in `useBoolean.test.ts`,  with only one test. Name it `hooks optimizations` and name the test itself `should keep actions reference equality after value change`. Good. 

Let's write the test and walk through it.

```ts
describe("hooks optimizations", () => {
  it("should keep actions reference equality after value change", () => {
    // given
    const { result } = renderHook(() => useBoolean(true));
    const [, originalActionsReference] = result.current;
    const { setFalse, setTrue, toggle } = originalActionsReference;

    expect(result.current[1]).toBe(originalActionsReference);
    expect(result.current[1].setFalse).toBe(setFalse);
    expect(result.current[1].setTrue).toBe(setTrue);
    expect(result.current[1].toggle).toBe(toggle);
    // when
    act(() => originalActionsReference.setFalse());
    // then
    expect(originalActionsReference).toBe(result.current[1]);
    expect(setFalse).toBe(result.current[1].setFalse);
    expect(setTrue).toBe(result.current[1].setTrue);
    expect(toggle).toBe(result.current[1].toggle);
  });
});
```

- The beginning is the same. Then we save our `actions` into the `originalActionsReference` constant, and destructure all the actions to get their references too.

- Then when we change our state we should expect that our `result` ref kept the same `actions` object and the same functions. So we check whether their references are the same.

Okay, let's run it.....  good, all green! 

In order to ensure that our test code is really testing what we expect, it's good to break it intentionally just to be 100% sure. To break it, we can change any of the assertions to something random and re-run the test. As you can see, the test is now red. That's a good thing!

That's it for `useBoolean` testing. Next up we will implement a `useMap` hook with test-driven development.
