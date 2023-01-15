# About Project App

**Login App -**
A basic Login App which uses effects, reducers & context api features in react.

## Live App link

<a href="https://PSoni2000.github.io/side-effects-reducers-context-api"
target="_blank" style='font-size:1.2rem; font-weight:bold;'>Demo Login App</a>

## Language / Library Used

1. React JS

# NOTES:

<ol>
<li>

**UseEffect -**

<p>The useEffect Hook allows you to perform side effects in your components.
Some examples of side effects are: fetching data, directly updating the DOM, and timers.useEffect accepts two arguments. The second argument is optional. but we should always include the second argument.

`useEffect(<function>, <dependency>)`

<ol>
<li>No dependency passed:

```
useEffect(() => {
    //Runs on every render
    //Runs after component rendering cycle
});
```

</li>
<li>An empty array:

```
useEffect(() => {
    //Runs only on the first render
    //Runs after component rendering cycle once
}, []);
```

</li>
<li>Props or state values:

```
useEffect(() => {
    //Runs on the first render
    //And any time any dependency value changes
}, [prop, state])
```

</li>

</ol>

**Effect Clean Up -**
Some effects require cleanup to reduce memory leaks.
Timeouts, subscriptions, event listeners, and other effects that are no longer needed should be disposed.

```
useEffect(()=>{
    subscribe()

    return ()=>{
        unsubscribe()
        // Runs every time before next subscribe() when dependency changes. So, basically it Runs before subscribe() but not before the first time runs.
        // Runs when component closes/Unmount.
    }
}, [])
```

**To focus any UI component -**

```
useEffect(()=>{
  inputRef.current.focus();
},[])
```

</p>
</li>
<li>

**UseReducer -**

<p>
The useReducer Hook is similar to the useState Hook.
It allows for custom state logic.
If you find yourself keeping track of multiple pieces of state that rely on complex logic, useReducer may be useful.

_Syntax_

```
const [state, dispatchFn] = useReducer(reducerFn, initialState, initFn);
```

- **state** - The state snapshot used in the component rerender/ revaluation cycle

- **dispatchFn** - A function that can be used to dispatch a new action (i.e. trigger an update of the state)

- **reducerFn** - (prevState, action) => newState
  A function that is triggered automatically once an action is dispatched (via dispatchFn()) – it receives the latest state snapshot and should return the new, updated state.

- **initialState** - The initial state.

- **initFn** - A function to set the initial state programmatically (rarely used)

_Example -_

```
import { useReducer } from 'react';

const emailReducer = (state, action) => {
  if (action.type === 'USER_INPUT') {
    return { value: action.val, isValid: action.val.includes('@') };
  }
  if (action.type === 'INPUT_BLUR') {
    return { value: state.value, isValid: state.value.includes('@') };
  }
  return { value: '', isValid: false };
};

const ReactComponent = (props) => {

    const [emailState, dispatchEmail] = useReducer(emailReducer, {
        value: '',
        isValid: null,
    });

    console.log(emailState.value); // shows user Entered email.

    const emailChangeHandler = (event) => {
        dispatchEmail({ type: 'USER_INPUT', val: event.target.value });
        // Send dispatch to update 'emailState' with type and payload (here payload is val).
    };

    const validateEmailHandler = () => {
        dispatchEmail({ type: 'INPUT_BLUR' });
        // payload is optional. if there is no new input you can avoid payload.
    };
}
```

</p>
</li>

<li>

**UseContext -**

<p>
React Context is a way to manage state globally.

It can be used together with the useState Hook to share state between deeply nested components more easily than with useState alone.

**Problem -**
State should be held by the highest parent component in the stack that requires access to the state.

To illustrate, we have many nested components. The component at the top and bottom of the stack need access to the state.

To do this without Context, we will need to pass the state as "props" through each nested component. This is called _"prop drilling"_.

```
import { useState } from "react";
import ReactDOM from "react-dom/client";

function Component1() {
  const [user, setUser] = useState("Jesse Hall");

  return (
    <>
      <h1>{`Hello ${user}!`}</h1>
      <Component2 user={user} />
    </>
  );
}

function Component2({ user }) {
  return (
    <>
      <h1>Component 2</h1>
      <Component3 user={user} />
    </>
  );
}

function Component3({ user }) {
  return (
    <>
      <h1>Component 3</h1>
      <Component4 user={user} />
    </>
  );
}

function Component4({ user }) {
  return (
    <>
      <h1>Component 4</h1>
      <Component5 user={user} />
    </>
  );
}

function Component5({ user }) {
  return (
    <>
      <h1>Component 5</h1>
      <h2>{`Hello ${user} again!`}</h2>
    </>
  );
}

const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(<Component1 />);
```

Even though components 2-4 did not need the state, they had to pass the state along so that it could reach component 5.

**The solution is to create context.**

```
import { useState, createContext, useContext } from 'react';
import ReactDOM from 'react-dom/client';

const UserContext = createContext();
// const contextName = createContext(<InitialValue>)

function Component1() {
  const [user, setUser] = useState('Jesse Hall');

  return (
    // Context Provider wrap the tree of components that need the state Context.

    <UserContext.Provider value={user}>
      <h1>{`Hello ${user}!`}</h1>
      <Component2 />
    </UserContext.Provider>

    // Now, all components in this tree will have access to the user Context.
  );
}

function Component2() {
  return (
    <>
      <h1>Component 2</h1>
      <Component3 />
    </>
  );
}

function Component3() {
  return (
    <>
      <h1>Component 3</h1>
      <Component4 />
    </>
  );
}

function Component4() {
  return (
    <>
      <h1>Component 4</h1>
      <Component5 />
    </>
  );
}

function Component5() {
  // In order to use the Context in a child component, we need to access it using the useContext Hook.

  const user = useContext(UserContext);

  return (
    <>
      <h1>Component 5</h1>
      <h2>{`Hello ${user} again!`}</h2>
    </>
  );
}

const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(<Component1 />);
```

**alternative way of consuming userContext-**

```
const Navigation = (props) => {
  return (
    <AuthContext.Consumer>
    // this way in place of useContext we use <Context.Consumer> tag to get context values
      {(ctx) => {
        // ctx is our context value
        return (
          <nav className={classes.nav}>
              {ctx.isLoggedIn && (
                  <a href="/">Users</a>
              )}
          </nav>
        );
      }}
    </AuthContext.Consumer>
  );
};
```

**Limitations -**

1. React Context is not optimized for high frequency changes!
</p>
</li>

<li>

**Rules of Hooks-**

1.  Only call React Hooks in React Functions.

    1. React Component Functions
    2. Custom Hooks

2.  Only call React Hooks at the top level.

    1. Don't call them in nested functions
    2. Don't call them in any block statements

3.  +extra, unofficial Rule for useEffect(): Always add everything you refer to inside of useEffect() as a dependency.
</li>

<li>

**useImperativeHandle -**

useImperativeHandle is a React Hook that lets you customize the handle exposed as a ref.

`useImperativeHandle(ref, createHandle, dependencies?)`

_More on_ - https://beta.reactjs.org/reference/react/useImperativeHandle

</li>
</ol>
