---
layout: post
title: Building React Applications with Idiomatic Redux
tags: [React, Redux, ES6]
---

Notes for \\
[Building React Applications with Idiomatic Redux](https://egghead.io/courses/building-react-applications-with-idiomatic-redux)

<!--more-->

### Redux #1: Simplifying the Arrow Functions

If the arrow function only contains a single return statement, you can replace the body of the arrow function with just that value. If this value is an object, don't forget to wrap it in parentheses so that the parser understands that this is an expression and not a block.

Also, it can be nicer to use the concise method notation, instead of arrow functions because it's shorter.





### Redux #2: Supplying the Initial State

When you create the Redux store, it's initial state is determined by the root reducer.

``` js
const todos = (state=[], action) => {...}
const filters = (state='SHOW_ALL', action) => {...}
const todoApp = combineReducers({
  todos,
  filters
})
const store = createStore(todoApp)
console.log(store.getState())
```
However, sometimes we want to load some existing data into the app synchronously before it starts. For example, we might have persistent todos from the previous session, and we might want to load this slice of the state into the app right before it starts.

``` js
const persistedState = {
  todos: [{
  id: '0',
  text: 'Welcome back!',
  completed: false
  }]
}
const store = createStore(
  todoApp,
  persistedState
)
```




### Redux #3: Persisting the State to the Local Storage

I'm going to write a function called `loadState` and a new model that I'm going to call **localStorage.js** that is going to use the **localStorage browser API**.

in <i>localStorage.js</i>

``` js
export const loadState = () => {
  try {
    const serializedState = localStorage.getItem('state')
    if (serializedState === null) return
    return JSON.parse(serializedState)
  } catch (err) {
    return
  }
}
export const saveState = (state) => {
  try {
    const serializedState = JSON.stringify(state)
    localStorage.setItem('state', serializedState)
  } catch(err) {
    // ignore err
  }
}
```

The `loadState` function is going to look into localStorage by key, retrieve a string, and try to parse it as JSON. It's important that we wrap this code into `try/catch` because calls to your localStorage `.getItem` can fail if the user privacy mode does not allow the use of localStorage.

``` js
import { loadState, saveState } from './localStorage'
import throttle from 'lodash/throttle'
const persistedState = loadState()
store.subscribe(throttle(() => {
  saveState({
    todos: store.getState().todos
  })
}, 1000))
// Wrapping callback in a throttle call insures that the inner function I pass is not going to be called more often than the number of milliseconds I specify.
```

However, the current code has a bug. If I add a new todo to the existing todos, it's not going to appear, and react is going to log a warning saying that I encountered two children with the same key, zero. That's because the todo ID we assigned in the `addTodo` action creator uses a local variable called `nextTodoId` as a counter. It's supposed to be unique. However if the application runs the second time the `nextTodoId` is going to be initialized to zero again so the new todo which is added also has an ID of zero just like the very first todo.

To avoid problems like this I'm going to install an npm module called `node-uuid`. It is a very tiny module, and it exports a couple of functions. The function we're going to use is called `v4`, which is just a name of the standard. It generates a unique string ID every time, and we're going to use this ID instead of a counter.

in <i>actions/index.js</i>

``` js
import {v4} from 'node-uuid'
export const addTodo = (text) => ({
  type: 'ADD_TODO',
  id: v4(),
  text
})
```




### Redux #4: Refactoring the Entry Point

I'm extracting the logic necessary to `createStore`, and to `subscribe` to it to persist the state into a separate file. I'm calling this file `configureStore.js`, and so I'm creating a function called `configureStore` that is going to contain this logic so that the app doesn't have to know exactly how the store is created and if we have any subscribe handlers on it. It can just use the returned store in the `index.js` file.

It is useful to export `configureStore` rather than store itself, so that if you later write some tests, you can easily create as many store instances as you want.

in <i>configureStore.js</i>

``` js
import { createStore } from 'redux'
import { Provider } from 'react-redux'
import throttle from 'lodash/throttle'
import theApp from './reducers'
import { loadState, saveState } from './localState'

const configureStore = () => {
  const persistedState = loadState()
  const store = createStore(
    theApp,
    persistedState
  )
  store.subscribe(throttle(() => {
    saveState({
      todos: store.getState().todos
    })
  }, 1000))
  return store
}
export default configureStore
```

I also want to extract the root rendered element into a separate component that I'm going to call `Root`. It's going to accept the store as a prop, and it's going to be defined in a separate file in the components folder.

in <i>root.js</i>

``` js
import React, { PropTypes } from 'react'
import { Provider } from 'react-redux'
import App from './App'

const Root = ({store}) => (
  <Provider store={store}>
    <App />
  </Provider>
)
Root.propTypes = {
  store: PropTypes.object.isRequired
}
export default Root
```




### Redux #5: Adding React Router to the Project

in <i>root.js</i>

``` js
import React, { PropTypes } from 'react'
import { Provider } from 'react-redux'
import { Router, Route, browserHistory } from 'react-router'
import App from './App'

const Root = ({store}) => {
  <Provider store={store}>
    <Router history={browserHistory}>
      <Route path='/' component={App} />
    </Router>
  </Provider>
}
```




### Redux 6: Navigating with React Router <Link>

The links that control the visibility filter do not currently behave like real links. I'd like to change it so that the back button works and the current URL updates when I click on these links and change the current `visibilityFilter`.

I will add a parameter to my route called `filter`, and I need to wrap it in parentheses to tell `react-router` that it's optional, because if it's not specified, I want to show all todos.

in <i>root.js</i>

``` js
const Root = ({store}) => {
  <Provider store={store}>
    <Router history={browserHistory}>
      <Router path='/(:filter)' component={App} />
    </Router>
  </Provider>
}
```

in <i>components/Footer.js</i>

``` js
import React from 'react'
import FilterLink from '../containers/FilterLink'
const Footer = () => (
  <p>
    Show: {' '}
    <FilterLink filter="all">All</FilterLink>{", "}
    <FilterLink filter="active">Active</FilterLink>{", "}
    <FilterLink filter="completed">Completed</FilterLink>
  </p>
)
```

in <i>containers/FilterLink.js</i>

``` js
import { connect } from 'react-redux'
import { Link } from 'react-router'

const FilterLink = ({filter, children}) => (
  <Link
    to={ filter === 'all' ? '' : filter}
    activeClassName="active">
    {children}
  </Link>
)
// or using `activeStyle` instead
```

Now I can remove the `setVisibilityFilter` Action creator, as I don't use it anymore. I can also remove my custom <Link> Component, because now I'm using the `{Link}` from `react-router` instead of it.

I'm using "active" and "completed" for the filter prop to align more closely with the paths I want to be displayed, and to avoid passing an empty string, I'll just use a null string to signify the default path.




### Redux 7: Filtering Redux State with React Router Params

in <i>containers/ListTodos.js</i>

``` js
const mapStateToProps = (state, ownProps) => ({
  todos: getVisibilityTodos(state.todos, ownProps.filter)
})
```

I am adding an argument called `ownProps` to the `mapStateToProps` function, and I'm going to read the current `visibilityFilter` from `ownProps`. I will also change the `getVisibilityTodos` function to use the current convention we use for the `filter` prop that is "all", "completed" or "active".

The `<ListTodos/>` component gets rendered from the `<App/>`, so this is where we need to add the `filter` prop to make it available in the `mapStateToProps` function of the `<ListTodos/>`.

We want the `filter` prop to correspond to the current `filter` parameter in our route configuration. `react-router` makes such parameters available to the route handler components in a special prop called `params`, so I'm adding a `params` prop to the app, and now I can read the filter from `params.filter`. Since it's empty on the root path, I'm passing all as a fallback to the `<ListTodos/>`.

in <i>App.js</i>

``` js
const App = ({params}) => (
  <div>
    <AddTodo />
    <ListFilter/>
    <ListTodos filter={params.filter || 'all'} />
  </div>
)
```

Now that the `visibilityFilter` is managed by `react-router`, I no longer need the `visibilityFilter` reducer.

Let's recap how `react-router` became the source of truth for the `visibilityFilter`. In the `root` component, I render the router, and I only have a single route with an optional `filter` parameter. The `filter` parameter gets passed into the `<App/>` component by `react-router`, and `react-router` will make it available inside a special `params` prop.

I am passing the `filter` param from the URL, or `all` if we are on the root path, to the `<ListTodos/>` component as a `filter` prop. The `<ListTodos/>` component now reads the filter from its `props` and not from the state, so it uses the filter specified by the app.

Finally, I fix the dev server to correctly return index HTML no matter which path is requested, so that React Router can pick it up on the client.

in <i>server.js</i>

``` js
app.get("/*", function(req, res) {
  res.sendFile(__dirname + '/index.html')
})
```

We're using `redux` as the source of truth for the todos, and we're using `react-router` as the source of truth for anything that can be computed from the URL. In our case, this is the current `visibilityFilter`.




### Redux 8: Using withRouter() to Inject the Params into Connected Components

The `<App/>` component itself does not really use `filter`. It just passes the `filter` down to the `<ListTodos/>`, which uses it to calculate the currently visible todos. Passing the params from the top level of route handlers gets tedious, so I'm removing the `filter` prop. Instead, I'm going to find a way to read the current router params in the `<ListTodos/>` itself.


in <i>containers/ListTodos.js</i>

``` js
const mapStateToProps = (state, {params}) => ({
  todos: getVisibilityTodos(state.todos, params.filter || 'all')
})
const ListTodos = withRouter(
  connect(mapStateToProps, mapDispatchToProps)(TodoList)
)
```





### Redux 9: Using mapDispatchToProps() Shorthand Notation

Normally, `mapDispatchToProps` accepts the dispatch function as an argument so that you can return the props through inject into the React component that each can dispatch certain actions using the dispatch function.

However, it is very common that the arguments passed through the callback props are passed through to the action creators in the same order. In this case, rather than write `mapDispatchToProps` function yourself, it can pass a configuration object that maps the names of the callback props that we want to inject to the corresponding action creator functions.

in <i>containers/ListTodos.js</i>

``` js
// const mapDispatchToProps = (dispatch) => ({
//   toggleTodoStatus: (id) => {
//     dispatch(toggleTodoStatus(id))
//   }
// })
const ListTodos = connect(
  mapStateToProps,
  { toggleTodoStatus }
)(TodoList)

```





### Redux 10: Colocating Selectors with Reducers

in <i>reducers/todos.js</i>

``` js
export const getVisibilityTodos = (state, filter) => {
  switch (filter) {
    case 'all':
      return state
    case 'todo':
      return state.filter(t => t.progress === 0)
    case 'doing':
      return state.filter(t => t.progress === 1)
    case 'done':
      return state.filter(t => t.progress === 2)
    default:
      throw new Error(`Unknown filter: ${filter}.`)
  }
}
```

in <i>reducers/index.js</i>

``` js
import todos, * as todoReducer from './todos'
export const getVisibileTodos = (state, filter) =>
  todoReducer.getVisibileTodos(state.todos, filter)
```

in <i>components/ListTodos.js</i>

``` js
import { getVisibileTodos } from '../reducers'
const mapStateToProps = (state, ownProps) => ({
  todos: getVisibileTodos(state, ownProps.filter)
})
```





### Redux 11: Normalizing the State Shape

in <i>reducers/todos.js</i>

``` js
import { combineReducers } from 'redux'

const byId = (state={}, action) => {
  switch (action.type) {
    case 'ADD_TODO':
    case 'TOGGLE_TODO':
      return {
        ...state,
        [action.id]: todo(state[action.id], action)
      }
    default:
      return state
  }
}

const allIds = (state=[], action) => {
  switch (action.type) {
    case 'ADD_TODO':
      return [...state, action.id]
    default:
      return state
  }
}

const todos = combineReducers({
  byId,
  allIds
})

export default todos

const getAllTodos = (state) =>
  state.allIds.map(id => state.byId[id])

export const getVisibileTodos = (state, filter) => {
  const allTodos = getAllTodos(state)
  switch (filter) {
    case 'all':
      return allTodos
    case 'todo':
      return allTodos.filter(t => t.progress === 0)
    case 'doing':
      return allTodos.filter(t => t.progress === 1)
    case 'done':
      return allTodos.filter(t => t.progress === 2)
    default:
      throw new Error(`Unknown filter: ${filter}.`)
  }
}
```

The new reducer `byId` is an object. It reads the id of the todo to update from the action and it calls the `todo` reducer with the previous state of this id and the action.

For the `'ADD_TODO'` action, the corresponding todo will not exist in the lookup table yet. We're calling the `todo` reducer with `undefined` as the first argument. The `todo` reducer would then return a new todo object when handling `'ADD_TODO'` so this object will get assigned under the action id key inside the next version of the lookup table.

Then I wrote a second reducer called `allIds` that manages just the array of ids of the todos. Every time a todo is added, it returns a new array with this id of the new todo at the very end.

I'm combining the two reducers I wrote into a single reducer by calling `combineReducers` provided by Redux. You may use `combineReducers` at multiple levels in your reducer hierarchy.

I also wrote the private selector called `getAllTodos` that just assembles all the todos objects from the state by mapping the ids to the lookup table. For every id, we get the todo from `state.byId`. I'm returning the array of todos with exactly the same shape as the state inside `getsVisibleTodos` used to be before this change.




### Redux 12: Wrapping dispatch() to Log Actions

in <i>configureStore.js</i>

``` js
const addLoggingToDispatch = (store) => {
  const rawDispatch = store.dispatch
  if (!console.group) return rawDispatch
  return (action) => {
    console.group(action.type)
    console.log('prev state', store.getState())
    console.log('action', action)
    const returnValue = rawDispatch(action)
    console.log('next state', store.getState())
    console.groupEnd(action.type)
    return returnValue
  }
}

const storeInitialize = () => {
  // ...
  if(process.env.NODE_ENV !== 'production')
    store.dispatch = addLoggingToDispatch(store)
  // ...
}
```





### Redux 13: Adding a Fake Backend to the Project

``` js
const delay = (ms) =>
  new Promise(resolve => setTimeout(resolve, ms))

export const fetchTodos = (state, filter) =>
  delay(500).then(() => {
    switch (filter) {
      case 'all':
        ...
    }
  })
```

### Redux 14: Fetching Data on Route Change

in <i>components/ListTodos.js</i>

``` js
import React, { Component } from 'react'
import { fetchTodos } from '../api'

class ListTodos extends Component {
  componentDidMount() {
    this.fetchData()
  }
  componentDidUpdate(prevProps) {
    if (this.props.filter !== prevProps.filter) {
      this.fetchData()
    }
  }
  fetchData() {
    fetchTodos(this.props.filter).then(todos =>
      console.log(this.props.filter, todos)
    )
  }
  render() {
    return <TodoList {...this.props} />
  }
}
ListTodos = connect(
  mapStateToProps,
  actions
)(ListTodos)
```





### Redux 15: Dispatching Actions with the Fetched Data






### Redux 16: Wrapping dispatch() to Recognize Promises

in <i>actions/index.js</i>

``` js
import * as api from '../api'
const receiveTodos = (filter, response) => ({
  type: 'RECEIVE_TODOS',
  filter,
  response
})
export const fetchTodos = (filter) =>
  api.fetchTodos(filter).then(response =>
    receiveTodos(filter, response)
  )
```

The `fetchTodos` action creator calls the `fetchTodos` function from the <i>../api</i> but then it transforms its result into a Redux action generated by `receiveTodos`.

in <i>components/ListTodos.js</i>

``` js
import * as actions from '../actions'

class ListTodos extends Component {
  componentDidMount() {
    this.fetchData()
  }
  componentDidUpdate(prevProps) {
    if (this.props.filter !== prevProps.filter) {
      this.fetchData()
    }
  }
  fetchData() {
    const { filter, fetchTodos } = this.props
    fetchTodos(filter)
  }
  render() {
    return <TodoList {...this.props} />
  }
}
```

in <i>configureStore.js</i>

``` js
const addPromiseSupportToDispatch = (store) => {
  const rawDispatch = store.dispatch
  return (action) => {
    if (typeof action.then === 'function')
      return action.then(rawDispatch)
    return rawDispatch(action)
  }
}
const storeInitialize = () => {
  // ...
  if(process.env.NODE_ENV !== 'production')
    store.dispatch = addLoggingToDispatch(store)
  store.dispatch = addPromiseSupportToDispatch(store)
  // ...
}
```

We override `store.dispatch` two times. Once to add the logging and second time to add the promise support. The function that adds the promise support accepts the store as an argument and it returns a function that looks like a dispatch function because it accepts the action but if the action is not really an action but a promise, we're going to wait for that promise to resolve to the real action, which will pass through raw dispatch.







### Redux 17: The Middleware Chain

While previous method of extending the store works, it's not really great at re-override the public api and replace it with custom functions. To get away from this pattern, I am declaring an array of what I'm calling middleware functions, that will be applied later as a single step.

in <i>configureStore.js</i>

``` js
const wrapDispatchWithMiddlewares = (store, middlewares) =>
  middlewares.forEach(middleware =>
    store.dispatch = middleware(store)(store.dispatch)
  )

const storeInitialize = () => {
  const persistedState = loadState()
  const middlewares = []
  // ...
  if(process.env.NODE_ENV !== 'production')
    middlewares.push(addLoggingToDispatch)
  middlewares.push(addPromiseSupportToDispatch)

  wrapDispatchWithMiddlewares(store, middlewares)
  // ...
}

```

Inside the middleware functions themselves, there is a certain pattern that I have to repeat. I'm grabbing the value of store dispatch, and I'm storing it in a variable called `rawDispatch` so that I can call it later. To make it a part of the middleware contract, I can make `rawDispatch` an outside argument, just like the `store` before it and the `action` after it.





### Redux 18: Applying Redux Middleware

``` shell
$ npm install --save-dev redux-promise
$ npm install --save-dev redux-logger
```

in <i>configureStore.js</i>

``` js
import { createStore, applyMiddleware, compose } from 'redux'
import promise from 'redux-promise'
import createLogger from 'redux-logger'
// ...
const storeInitialize = () => {
  const persistedState = loadState()
  const middlewares = [promise]
  if(process.env.NODE_ENV !== 'production')
    middlewares.push(createLogger())

  const store = createStore(reducer, persistedState, compose(
    applyMiddleware(...middlewares),
    window.devToolsExtension ? window.devToolsExtension() : f => f
  ))
  // ...
}
```





### Redux 19: Updating the State with the Fetched Data

in <i>components/ListTodos.js</i>

``` js

```




### Redux 20: Refactoring the Reducers

in <i>reducers/index.js</i>

``` js

import { combineReducers } from 'redux'
import byId, * as fromById from './byId'
import createList, * as fromList from './createList'

const listByFilter = combineReducers({
  all: createList('all'),
  todo: createList('todo'),
  doing: createList('doing'),
  done: createList('done')
})

const todos = combineReducers({
  byId,
  listByFilter
})
export default todos

export const getVisibileTodos = (state, filter) => {
  const ids = fromList.getIds(state.listByFilter[filter])
  return ids.map(id => fromById.getTodo(state.byId, id))
}
```

in <i>reducers/byIds.js</i>

``` js
import { combineReducers } from 'redux'

const byId = (state={}, action) => {
  switch (action.type) {
    case 'RECEIVE_TODOS':
      const nextState = {...state}
      action.response.forEach(todo => {
        nextState[todo.id] = todo
      })
      return nextState
    default:
      return state
  }
}
export default byId

export const getTodo = (state, id) => state[id]
```

in <i>reducers/createList.js</i>

``` js
const createList = (filter) => (state=[], action) => {
  if (action.filter !== filter) return state
  switch(action.type) {
    case 'RECEIVE_TODOS':
      return action.response.map(t => t.id)
    default:
      return state
  }
}
export default createList

export const getIds = (state) => state
```

The `todos` reducer is now the root reducer of the application, and its file has been renamed to <i>index.js</i>. We extracted the `byId` reducer into a separate file, and it is exported from this module as a default export. To encapsulate the knowledge about the state shape in this file, we export a new selector that just gets the todo by its id from the lookup table.

We also created a new function called `createList` that we use to generate the reducers, managing the lists of fetched todos for any given filter. It takes filter as an argument, and it returns a reducer that manages the fetched ids for this filter. The generated reducers will handle the `'RECEIVE_TODOS'` action, but they will keep any action that has the filter different from the one they were created with.

`createList` is the default export from this file, but we also export a selector that gets the ids from the current state. Right now, the ids are the current state, but we are free to change this in the future.

In <i>index.js</i>, we use the namespace import syntax to grab all selectors from the corresponding file into an object. The `listByFilter` reducer combines the reducers generated by `createList`, and it uses the filters as the keys.

Redux does not enforce that you encapsulate the knowledge about the state shape in particular reducer files. However, it's a nice pattern, because it lets you change the state that is stored by reducers without having to change your components or your tests if you use selectors together with reducers in your tests.







### Redux 21: Displaying Loading Indicators


in <i>actions/index.js</i>

``` js
export const requestTodos = (filter) => ({
  type: 'REQUEST_TODOS',
  filter
})
```

in <i>reducers/index.js</i>

``` js
export const getIsFetching = (state, filter) =>
  fromList.getIsFetching(state.listByFilter[filter])
```

in <i>reducers/createList.js</i>

``` js
import { combineReducers } from 'redux'
const createList = (filter) => {
  const ids = (state=[], action) => {
    if (action.filter !== filter) return state
    switch(action.type) {
      case 'RECEIVE_TODOS':
        return action.response.map(t => t.id)
      default:
        return state
    }
  }
  const isFetching = (state=false, action) => {
    if (action.filter !== filter) return state
    switch(action.type) {
      case 'REQUEST_TODOS':
        return true
      case 'RECEIVE_TODOS':
        return false
      default:
        return state
    }
  }
  return combineReducers({
    ids, isFetching
  })
}
export default createList
export const getIds = (state) => state.ids
export const getIsFetching = (state) => state.isFetching

```

in <i>containers/ListTodos.js</i>

``` js
class ListTodos extends Component {
  // ...
  fetchData() {
    const { filter, requestTodos, fetchTodos } = this.props
    requestTodos(filter)
    fetchTodos(filter)
  }
  render() {
    const { todos, isFetching, toggleTodoStatus } = this.props
    if (isFetching && !todos.length)
      return <p>Loading...</p>
    return <TodoList todos={todos} toggleTodoStatus={toggleTodoStatus} />
  }
}
```





### Redux 22: Dispatching Actions Asynchronously with Thunks

It would be great if I could make `requestTodos` dispatched automatically when I dispatch `fetchTodos` because I never want to fire them separately.

in <i>actions/index.js</i>

``` js
export const fetchTodos = (filter) => (dispatch) => {
  dispatch(requestTodos(filter))
  return api.fetchTodos(filter).then(response => {
    dispatch(receiveTodos(filter, response))
  })
}
```

in <i>configureStore.js</i>

``` js
// import promise from 'redux-promise'
// ...
const thunk = (store) => (next) => (action) =>
  typeof action === 'function' ?
    action(store.dispatch) :
    next(action)

const storeInitialize = () => {
  const persistedState = loadState()
  const middlewares = [thunk]
  // ...
```

The new middleware I'm writing is called the thunk middleware because it supports the dispatching of thunks, and it takes the store, the next middleware, and the action as current arguments, just like any other middleware.

If the action is not an action, but rather a function, we're going to assume that this is a thunk that wants the dispatch function to be injected into it, so I'm calling the action with `store.dispatch`. Otherwise, I'm just going to return the result of passing the action to the next middleware in chain.





### Redux 23: Avoiding Race Conditions with Thunks

in <i>configureStore.js</i>

``` js
import thunk from 'redux-thunk'
// ...
```

in <i>actions/index.js</i>

``` js
export const fetchTodos = (filter) => (dispatch, getState) => {
  if (getIsFetching(getState(), filter))
    return Promise.resolve()

  dispatch(requestTodos(filter))
  return api.fetchTodos(filter).then(response => {
    dispatch(receiveTodos(filter, response))
  })
}
```





### Redux 24: Displaying Error Messages

in <i>actions/index.js</i>

``` js
export const fetchTodos = (filter) => (dispatch, getState) => {
  if (getIsFetching(getState(), filter))
    return Promise.resolve()

  dispatch({
    type: 'FETCH_TODOS_REQUEST',
    filter
  })

  return api.fetchTodos(filter).then(
    response => {
      dispatch({
        type: 'FETCH_TODOS_SUCCESS',
        filter,
        response
      })
    },
    error => {
      dispatch({
        type: 'FETCH_TODOS_FAILURE',
        filter,
        message: error.message || 'Something went wrong.'
      })
    }
  )
}
```

in <i>containers/ListTodos.js</i>

``` js
import FetchError from './FetchError'
class ListTodos extends Component {
  // ...
  render() {
    const { todos, isFetching, errorMessage, toggleTodoStatus } = this.props
    if (isFetching && !todos.length)
      return <p>Loading...</p>
    if (errorMessage && !todos.length)
      return <FetchError message={errorMessage}
               onRetry={() => this.fetchData()} />
    return <TodoList todos={todos} toggleTodoStatus={toggleTodoStatus} />
  }
}
```

in <i>actions/createList.js</i>

``` js
const createList = (filter) => {
  // ...
  const errorMessage = (state=null, action) => {
    if (action.filter !== filter) return state
    switch(action.type) {
      case 'FETCH_TODOS_FAILURE':
        return action.message
      case 'FETCH_TODOS_REQUEST':
      case 'FETCH_TODOS_SUCCESS':
        return null
      default:
        return state
    }
  }

  return combineReducers({
    ids, isFetching, errorMessage
  })
}
```

in <i>actions/index.js</i>

``` js
export const getErrorMessage = (state, filter) =>
  fromList.getErrorMessage(state.listByFilter[filter])
```





### Redux 25: Creating Data on the Server

in <i>actions/index.js</i>

``` js
export const addTodo = (text) => (dispatch) =>
  api.addTodo(text).then(response => {
    dispatch({
      type: 'ADD_TODO_SUCCESS',
      response
    })
  })
```

in <i>reducers/byId.js</i>

``` js
const byId = (state={}, action) => {
  switch (action.type) {
    case 'FETCH_TODOS_SUCCESS':
      const nextState = {...state}
      action.response.forEach(todo => {
        nextState[todo.id] = todo
      })
      return nextState
    case 'ADD_TODO_SUCCESS':
      return {
        ...state,
        [action.response.id]: action.response
      }
    default:
      return state
  }
}
```

in <i>reducers/createList.js</i>

``` js
const createList = (filter) => {
  const ids = (state=[], action) => {
    switch(action.type) {
      case 'FETCH_TODOS_SUCCESS':
        return filter === action.filter ?
          action.response.map(t => t.id) : state
      case 'ADD_TODO_SUCCESS':
        return [...state, action.response.id]
      default:
        return state
    }
  }
```





### Redux 26: Normalizing API Responses with normalizr

in <i>actions/schema.js</i>

``` js
import { Schema, arrayOf } from 'normalizr'

export const todo = new Schema('todos')
export const arrayOfTodos = arrayOf(todo)

```

in <i>actions/index.js</i>

``` js
import { normalize } from 'normalizr'
import * as schema from './schema'

export const fetchTodos = (filter) => (dispatch, getState) => {
  // ...
  return api.fetchTodos(filter).then(
    response => {
      dispatch({
        type: 'FETCH_TODOS_SUCCESS',
        filter,
        response: normalize(response, schema.arrayOfTodos)
      })
    },
    // ...
  )
}
export const addTodo = (text) => (dispatch) =>
  api.addTodo(text).then(response => {
    dispatch({
      type: 'ADD_TODO_SUCCESS',
      response: normalize(response, schema.todo)
    })
  })
```

in <i>reducers/byId.js</i>

``` js
const byId = (state={}, action) => {
  if (action.response)
    return {
      ...state,
      ...action.response.entities.todos
    }

  return state
}
```

in <i>reducers/createList.js</i>

``` js
const createList = (filter) => {
  const ids = (state=[], action) => {
    switch(action.type) {
      case 'FETCH_TODOS_SUCCESS':
        return filter === action.filter ?
          action.response.result : state
      case 'ADD_TODO_SUCCESS':
        return [...state, action.response.result]
      default:
        return state
    }
  }
  // ...
}
```





### Redux 27: Updating Data on the Server

in <i>actions/index.js</i>

``` js
export const toggleTodoStatus = (id) => (dispatch) =>
  api.toggleTodo(id).then(response => {
    dispatch({
      type: 'TOGGLE_TODO_SUCCESS',
      response: normalize(response, schema.todo)
    })
  })
```

in <i>reducers/createList.js</i>

``` js
const createList = (filter) => {
  const ids = (state=[], action) => {
    const handleToggle = (state, action) => {
      const { result: toggledId, entities } = action.response
      return state.filter(id => id !== toggledId)
    }
    switch(action.type) {
      // ...
      case 'TOGGLE_TODO_SUCCESS':
        return handleToggle(state, action)
      default:
        return state
    }
  }
}
```

