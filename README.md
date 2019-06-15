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