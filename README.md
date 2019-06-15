# Redux

As I finish my first React apps I've decided to learn Redux. The one thing I learned working on those small projects (you can find them here in my github) is that managing the state of the app is not easy. Redux should fix that.

For this I'll use [Dan Amabrov's course.](https://egghead.io/lessons/react-redux-the-reducer-function)

## Three principles of Redux

### Single Immutable State Tree

Redux creates a single source of truth. The whole state of the application is stored in a plan Javascript object, it is inmmutable, meaning it cannot be changed unless through a very specific method.

### Actions

The state is read-only. The only way to change it is to dispatch an action.

Actions are a plan Javascript object that represent the action dispached to change our state object. The most simple action is the one that only contains the 'type', which is a string that identifies the kind of action that is beign used.

More complex actions can be created for bigger applications. This actions have a type plus other information that is required to create the new state.

"Any data that gets into the state of the application gets there through actions". This is the only way to change the state tree.

### Reducers

Reducers are pure functions accept state and actions as arguments and return a new state. Any state mutations should be done through reducers.

###### IMPORTANT: pure functions only use their arguments and don't modify the original arguments given, they return new ones. 

##### EXAMPLE

```javascript
const counter = (state = 0, action) => {
    switch (Action.type) {
        case 'INCREMENT':
            return state + 1;
        case 'DECREMENT':
            return state - 1;
        default:
            return state;
    }
}
```

What is going on here? In this basic reducer function we are decreasing or increasing the state by one. Three things are important to notice: 

1. The state is initialized in the first line with an ES6 default argument syntax.
2. We handle the INCREMENT action type correctly as well as DECREMENT
3. We have an option for when an action is not defined correctly: the default. This only returns the current state.

## Methods

Creating a store.

```Javascript
const { createStore } = Redux;
//or
import { createStore } from 'redux'
```

The store binds together the 3 core principles of redux. It stores the state tree. After that we have to create the reducer function.

```Javascript
const { createStore } = Redux;
const store = createStore(counter);
```

Counter is the function we created previously. It is a reducer. The store has *3 important methods.*

### getState()

Retrieves the current state of the store (0 in this case).

```Javascript
const { createStore } = Redux;
const store = createStore(counter);
console.log(store.getState());
```

### dispatch()

The most used one. It dispatches actions to change the state of our application. It takes in the action object.
```Javascript
const { createStore } = Redux;
const store = createStore(counter);
console.log(store.getState());

store.dispatch({ type: 'INCREMENT'});
```

### suscribe()

It lets you register a callback that the Redux store is going to call everytime an action has been dispatched so that you can update the UI with the new state.

```Javascript
const { createStore } = Redux;
const store = createStore(counter);
console.log(store.getState());

const render = () => {
    document.body.innerText = store.getState();
}

store.suscribe(render);
render();

document.addEventListener('click', () => {
    store.dispatch({ type: 'INCREMENT' });
});
```

So what is going on here? We created an event listener. Everytime we click it is going to dispatch an action of type increment, then the state changes. Before that we suscribed the render method, meaning that everytime that we execute the action the render method is going to happen as a callback. In this method Dan renders the state to the browser. 

`suscribe => bind action to click => action => new state => callback(render)`