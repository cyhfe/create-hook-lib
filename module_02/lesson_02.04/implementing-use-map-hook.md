---
title: Implementing useMap hook
isPublicLesson: true
---

This time around we will implement a hook named `useMap`, that will wrap around a native Map object and mimic its API. We're going to start with the tests this time.

The same logic applies. We want the actions that will change our state to stay the same no matter the changes of the state. In other words, only changes to the state should trigger
a re-render of a component that gets state as a prop.

We'll start with the imports and test cases. For this, it's good to open the [MDN documentation](https://developer.mozilla.org/ru/docs/Web/JavaScript/Reference/Global_Objects/Map) on `Map` prototype and its methods.

We don't care about the reading part, we care about writing, because we need to create a new instance of the map in order to make it immutable to change. So we need `delete`, `clear` and `set`. Also in `react-hanger` I have added `initialize` action in order to clear and override the whole map with fresh data in one go, which can be useful in some use cases.

So let's create the `useMap.test.ts` file alongside our other tests.

// Walk through while writing the code

```ts
import { act, renderHook } from "@testing-library/react-hooks";
import { useMap } from "../useMap";

describe("useMap array", () => {
  describe("set", () => {
    it("should update old value", () => {});
    it("should add new value", () => {});
  });

  describe("delete", () => {
    it("should delete existing value", () => {});
  });

  describe("initialize", () => {
    it.each`
      message    | input
      ${"map"}   | ${new Map([[1, "initialized"]])}
      ${"tuple"} | ${[[1, "initialized"]]}
    `("initializes with $message", ({ input }) => {});
  });

  describe("clear", () => {
    it("clears the map state and gets values", () => {});
  });

  describe("hooks optimizations", () => {
    it("should change value reference equality after change", () => {});
    it("should keep actions reference equality after value change", () => {});
  });
});
```

Essentially we can just copy and paste a little bit of code from the `useBoolean` test and adjust it a bit to save some typing. Thanks to the magic of editing I'll quickly type it all in, and explain it line-by-line after each case.

// Quick walk through each case

```ts
import { act, renderHook } from "@testing-library/react-hooks";
import { useMap } from "../useMap";

describe("useMap array", () => {
  describe("set", () => {
    it("should update old value", () => {
      // given
      const { result } = renderHook(() => useMap<number, string>([[1, "default"]]));
      const [, actions] = result.current;
      expect(result.current[0].get(1)).toBe("default");
      // when
      act(() => actions.set(1, "changed"));
      // then
      expect(result.current[0].get(1)).toBe("changed");
    });
    it("should add new value", () => {
      // given
      const { result } = renderHook(() => useMap<number, string>());
      const [, actions] = result.current;
      expect(result.current[0].get(1)).toBeUndefined();
      // when
      act(() => actions.set(1, "added"));
      // then
      expect(result.current[0].get(1)).toBe("added");
    });
  });

  describe("delete", () => {
    it("should delete existing value", () => {
      // given
      const { result } = renderHook(() => useMap<number, string>([[1, "existing"]]));
      const [, actions] = result.current;
      expect(result.current[0].get(1)).toBe("existing");
      // when
      act(() => actions.delete(1));
      // then
      expect(result.current[0].get(1)).toBeUndefined();
    });
  });

  describe("initialize", () => {
    it.each`
      message    | input
      ${"map"}   | ${new Map([[1, "initialized"]])}
      ${"tuple"} | ${[[1, "initialized"]]}
    `("initializes with $message", ({ input }) => {
      // given
      const { result } = renderHook(() => useMap<number, string>());
      const [, actions] = result.current;
      expect(result.current[0].get(1)).toBeUndefined();
      // when
      act(() => actions.initialize(input));
      // then
      expect(result.current[0].get(1)).toBe("initialized");
    });
  });

  describe("clear", () => {
    it("clears the map state and gets values", () => {
      // given
      const { result } = renderHook(() => useMap<number, string>([[1, "initialized"]]));
      const [, actions] = result.current;
      expect(result.current[0].get(1)).toBe("initialized");
      // when
      act(() => actions.clear());
      // then
      expect(result.current[0].get(1)).toBeUndefined();
    });
  });

  describe("hooks optimizations", () => {
    it("should change value reference equality after change", () => {
      // given
      const { result } = renderHook(() => useMap<number, number>());
      const [originalValueReference, actions] = result.current;
      expect(result.current[0]).toBe(originalValueReference);
      // when
      act(() => actions.set(1, 1));
      // then
      expect(originalValueReference).not.toBe(result.current[0]);
      expect(originalValueReference.get(1)).toBeUndefined();
      expect(result.current[0].get(1)).toBe(1);
    });
    it("should keep actions reference equality after value change", () => {
      // given
      const { result } = renderHook(() => useMap<number, number>());
      const [, originalActionsReference] = result.current;
      expect(result.current[1]).toBe(originalActionsReference);
      // when
      act(() => originalActionsReference.set(1, 1));
      // then
      expect(originalActionsReference).toBe(result.current[1]);
    });
  });
});
```

That's all the test cases - let's move to the implementation now.

Run `yarn test:watch` and then click `p` and spell `useMap` so we're watching only `useMap` tests.

Nice! Let's create a file named `useMap` and write our implementation real quick. Let's speed it up and walk through.

```ts
import { Dispatch, SetStateAction, useCallback, useMemo, useState } from "react";

export type MapOrEntries<K, V> = Map<K, V> | [K, V][];
export type UseMapActions<K, V> = {
  setValue: Dispatch<SetStateAction<Map<K, V>>>;
  delete: (keyToRemove: K) => void;
  set: (key: K, value: V) => void;
  clear: Map<K, V>["clear"];
  initialize: (pairsOrMap: MapOrEntries<K, V>) => void;
};

export type UseMap<K, V> = [Map<K, V>, UseMapActions<K, V>];

export function useMap<K, V>(initialState: MapOrEntries<K, V> = new Map()): UseMap<K, V> {
  const [map, setMap] = useState(
    Array.isArray(initialState) ? new Map(initialState) : initialState
  );

  const set = useCallback((key, value) => {
    setMap((aMap) => {
      const copy = new Map(aMap);
      return copy.set(key, value);
    });
  }, []);

  const deleteByKey = useCallback((key) => {
    setMap((_map) => {
      const copy = new Map(_map);
      copy.delete(key);
      return copy;
    });
  }, []);

  const clear = useCallback(() => {
    setMap(() => new Map());
  }, []);

  const initialize = useCallback((mapOrTuple: MapOrEntries<K, V> = []) => {
    setMap(() => new Map(mapOrTuple));
  }, []);

  const actions = useMemo(
    () => ({
      setValue: setMap,
      clear,
      set,
      delete: deleteByKey,
      initialize,
    }),
    [clear, deleteByKey, initialize, set]
  );

  return [map, actions];
}

export default useMap;
```

And that's a wrap. All the tests are green and we're good to go.
