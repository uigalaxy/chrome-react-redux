## Installation


1. `yarn / npm i`
2. `yarn build / npm run build `
3. install the extension from `/dist` folder via _Load unpacked extension_ button in Chrome

## Tutorial

So, let's assume that our WebExtension has some todos. We can add new todo or delete with button in popup.

#### Background page

Let's look at the background store:

```js
const store = createStore(reducer); // real Redux store

export default createBackgroundStore({
    store,
    actions: {
        'someKeyForAdd': addTask,
        'someKeyForRemove': removeTask
    }
});
```

As you can see we provide real Redux store to `createBackgroundStore` and also pass `actions` object. This object associates actions in _background_ page with _popup_ actions (don't worry, it'll become clearer when you look at actions in _popup_). 

Regarding _background_ actions, they're just usual actions as in any Redux application:

```js
export function addTask({ value }) {
  return {
    type: ADD_TASK,
    value
  };
}

export function removeTask({ taskId }) {
  return {
    type: REMOVE_TASK,
    taskId
  };
}

```

And that's it about _background_ page, pretty straightforward.

#### Popup (UI page)

To create store in _popup_ we can use the following code: 

```js
const store = await createUIStore();
```

Just one line if you use `async/await` feature! `createUIStore` creates an object with the same interface as Redux store, so you or [react-redux](https://github.com/reactjs/react-redux) won't see any difference. 

The only thing that is left is _popup_ actions:

```js
export function incrementCounterUI() {
    return {type: 'someKeyForIncrement'};
}

export function decrementCounterUI() {
    return {type: 'someKeyForDecrement'};
}
```

As you can see the values of `type` property are the same as the keys of `actions` object that we provided to `createBackgroundStore`. And when we call one of these _popup_ actions, `redux-webext` will call the associated action in _background_ page. 

For instance, when you call `incrementCounterUI` in _popup_, `redux-webext` will call `incrementCounterBackground` in _background_ page.
