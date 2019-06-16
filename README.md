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

## Implement store from scratch

```javascript
const createStore = (reducer) => {
    let state;
    let listeners = [];

    const getState = () => state;

    const dispatch = (action) => {
        state = redicer(state, action);
        listeners.forEach(listener => listener());
    };

    const suscribe = (listener) => {
        listeners.push(listener);
        return () => {
            listeners = listeners.filter(l => l !== listener);
        };
    };

    dispatch([]);

    return { getState, dispatch, suscribe };
};
```
###### IMPORTANT: this function is already provided by redux, this is a recreation to see how it works under the hood.

In here the only argument it needs is the reducer. Has the three main methods (getstate, dispatch, suscribe). We have an array with all the listeners. We see in the dispatch method that everytime it gets call it loops through the listeners array and calls each one. That way we can trigger all of them at every action.
The suscribe method has a way to unsuscribe too. The dummy action at the end is to populate is the initial state.

## Avoiding array mutations

As previously explained, the state should be immutable. In order to keep with this principle we need to make sure we use the right methods. For example: `concat()` `slice()` and the spread operator `...`. 

Dan uses deepfreeze and expect libraries to test for this.

**Example**

*wrong*
```javascript
const addCounter = (list) => {
    list.push(0);
    return list;
}
```

*right*
```javascript
const addCounter = (list) => {
    return list.concat([0]);
}
```
*also right*
```javascript
const addCounter = (list) => {
    return [...list, 0];
}
```

###### For more information on the spread operator [see this.](https://www.geeksforgeeks.org/javascript-spread-operator/)

## Reducer example (avoiding mutation)

```javascript
const todos = (state = [], action) => {
     switch(action.type) {
         case 'ADD_TODO':
            return [
                ...state,
                {
                    id: action.id,
                    text: action.text,
                    completed: false
                }
            ];
        case 'TOGGLE_TODO':
            return state.map(todo => {
                if (todo.id !== action.id) {
                    return todo;
                }

                return {
                    ...todo,
                    completed: !todo.completed
                };
            });
        default:
            return state;
     }
};
```

Here we can see all the important aspects of a reducer. Specifically the use of the spread operator in order to avoid mutation. On a side note, it is recommendable to have one reducer that calls other reducers that abstract how an action should be handled. This is not necessary but its good practice and can help with maintainability and readability.

## Reducers composition

```javascript
//this is completely new, its an abstraction
const todo = (state, action) => {
    switch(action.type) {
         case 'ADD_TODO':
            return [
                ...state,
                {
                    id: action.id,
                    text: action.text,
                    completed: false
                }
            ];
        case 'TOGGLE_TODO':
            return state.map(todo => {
                if (state.id !== action.id) {
                    return state;
                }

                return {
                    ...state,
                    completed: !state.completed
                };
            });
        default:
            return state;
     }
}

const todos = (state = [], action) => {
     switch(action.type) {
         case 'ADD_TODO':
            return [
                ...state,
                // this changed compared to the previos example
                todo(undefined, action)
            ];
        case 'TOGGLE_TODO':
        //this also changed
            return state.map(t => todo(t, action));
        default:
            return state;
     }
};
```

Ok this can be a lot. At least it took me a while to understand. *This peice of code does exactly the same as the previous one.* So what changed? we extracted the TODO creation/modification. For this we created a new function called todo, while todoS still exists but only to call todo with the right arguments.

Go through the code and check the comments. We could refer to todos as a parent function, it still has the switch and it will call todo with the right arguments to perform the correct action. Note that in the todo function the first argument is still called state, *altough it isn't the state*, it is, in the case of TOGGLE_TODO, one of the todos beign looped through.

In simpler words. Reducer composition is extracting functions to perform our state changing tasks more clearly. That makes it easier to mantain and read. *Ish*.

**Personal opinion**

This example that Dan uses is a clear view of what I don't like about Redux, it is unnecessarily complicated. Extracting functions to make code readable is pretty normal but Dan's use of terminology and naming is too complicated. The same thing happens with Redux all around. Why call it state in the case of TOGGLE_TODO, why not abstract that and call the argument todo? I don't know, maybe someone with more experience could enlight me. Also, breaking up the visual format of the arguments of the reducer took me off guard, again, nothing crazy, but was it really necessary?

*rant over*

## Combining reducers

```javascript
const visibilityFilter = (
    state = 'SHOW_ALL',
    action
) => {
    switch (action.type) {
        case 'SET_VISIBILITY_FILTER':
            return action.filter
        default:
            return state;
    }
};
```
Here we created a new function. We are going to combining all reducers into a single one for ease of use. This one right here takes care of the visibility filter, *only the visibility filter*.

```javascript
const todoApp = (state = {}, action) => {
    return {
        todos: todos(
            state.todos,
            action
        ),
        visibilityFilter: visibilityFilter(
            state.visibilityFilter,
            action
        )
    };
};
```

This is the combiner. Look at what it returns: *a single object*. So, in the return it is going to call all the reducers (in this case the todos reducer and the visibilityFilter reducer) and then combines them all in one object which it returns. Thats why the filter reducer only changes action.filter, and doesn't need any spread operator or state.

This pattern helps scale Redux apps.