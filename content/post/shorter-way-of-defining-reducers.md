+++
date = "2017-03-24T19:11:08+01:00"
title = "Shorter way of defining reducers and testing them using updeep"
tags = ["redux","javascript", "reducers", "tests", "ui"]
categories = [""]
draft = false
description = ""

+++
While exploring [discussion issues](https://github.com/reactjs/redux/issues?q=is%3Aissue+label%3Adiscussion+is%3Aclosed) in redux  I found a nice library called [updeep](https://github.com/substantial/updeep) and it's use to define reducers.

It looks like this:
```[javscript]
import u from 'updeep'

export const reducer = createReducer({
  // [action name]: action => currentState => nextState
  startCompiling: () => u({ compiling: true }),
  errorCompiling: ({ error }) => u({ compiling: false, compileError: error }),
  startRunning: ({ app }) => u({
    running: () => app,
    compiling: false
  }),
  stopRunning: () => u({ running: false }),
  discardCompileError: () => u({ compileError: null }),
  // ...
})
```
Source: https://github.com/reactjs/redux/issues/1171#issuecomment-205888533

Ok there's missing `createReducer` function but we can define it on our own like this:
```
// definition := { [action.type]: action => currentState => nextState }
export default function createReducer(initialState, definition){
  return (state = initialState, action) => {
    const actionResponse = definition[action.type];
    return actionResponse ? actionResponse(action)(state) : state;
  };
}

```

This is more `actions` oriented approach to the classical switch mentioned in redux docs,
 which are more data oriented(when you use `combineReducers` and switch).

What I like more about this updeep library is that it's perfect for testing reducer mutation when you have complex data tree.

With it's help you can write tests without any boilerplate like this(CoffeScript):
```[javascript]
u = ...
redux = ...

initialState = redux.getState()

# define data paths which you expect modified:
expectedState =
  first:
    path: "foo"
  other:
    path:
      one: "two"
  and:
    this: "bar"

redux.dispatch({ type: "MY_ACTION" });
expect(u(expectedState, initialState)).toEqual(redux.getState())
```
