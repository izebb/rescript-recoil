# reason-recoil

![Node.js CI](https://github.com/bloodyowl/reason-recoil/workflows/Node.js%20CI/badge.svg)

> Zero-cost bindings to Facebook's [Recoil](https://recoiljs.org) library

⚠️ These bindings are still in experimental stages, use with caution

## Installation

Run the following command:

```console
$ yarn add reason-recoil
```

Then add `reason-recoil` to your `bsconfig.json`'s dependencies:

```diff
 {
   "bs-dependencies": [
+    "reason-recoil"
   ]
 }
```

## Usage

### Atom

```reason
let textState = Recoil.atom({
  key: "textState",
  default: "",
});
```

### Selector

A nice feature the OCaml type-system enables is the ability to differenciate Recoil values between the ones that can **only read state** with the ones that can **write state**. This way, you **can't** use hooks with write capabilities with a read-only value.

#### With read only capabilities

```reason
let textStateSize = Recoil.selector({
  key: "textStateSize",
  get: ({get}) => {
    let textState = get(textState);
    Js.String.length(textState);
  },
});
```

#### With write capabilities

```reason
let textStateSize = Recoil.selector({
  key: "textStateSize",
  get: ({get}) => {
    let textState = get(textState);
    Js.String.length(textState);
  },
  set: ({set}, newSize) => {
    let currentTextState = get(textState);
    set(textState, currentTextState->Js.String.slice(~from=0, ~to_=newSize));
  }
});
```

#### Async

```reason
let user = Recoil.asyncSelector({
  key: "user",
  get: ({get}) => {
    fetchUser(get(currentUserId))
  },
});
```

### Hooks

#### `useRecoilState`

```reason
let (state, setState) = Recoil.useRecoilState(textState);

state; // read
setState(textState => newTextState); // write
```

#### `useRecoilValue`

```reason
let state = Recoil.useRecoilState(textState);

state; // read
```

#### `useSetRecoilState`

```reason
let setState = Recoil.useSetRecoilState(textState);

setState(textState => newTextState); // write
```

#### `useResetRecoilState`

```reason
let reset = Recoil.useResetRecoilState(textState);

reset(); // write
```

## Examples

The [Recoil Basic Tutorial](https://recoiljs.org/docs/basic-tutorial/intro) has been made in ReasonReact: [check the source](./examples/TodoList.re)!

You can run it using:

```console
$ yarn examples
```

and going to [http://localhost:8000/TodoList.html](http://localhost:8000/TodoList.html)
