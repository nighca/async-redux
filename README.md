# async-redux

Solution for async state management in React + Redux application

# Usage

Use async-redux with [react-redux](https://github.com/reactjs/react-redux), [reselect](https://github.com/reactjs/reselect).

async-redux provides a middleware, a reducer creator, a selector creator & two helper methods: `getData`, `isFetching`. It affects many parts in your application: create store, create actions, create reducers, create selectors & prepare props for components.

### middleware

It works like [redux-promise-middleware](https://github.com/pburtchaell/redux-promise-middleware) as a middleware.

```javascript
import { middleware as asyncMiddleware } from 'asyc-redux'

const middlewares  = applyMiddleware(
    thunkMiddleware,
    asyncMiddleware(options),
    // ...
)

const store = createStore(reducer, {}, middlewares)
```

### action

Actions whose `payload` is a promise will be processed.

```javascript
function fetchFoo() {
  return {
    type: 'FETCH_FOO',
    // assume this promise resolves with `foo`
    payload: fetch(apiFoo)
  }
}
```

### reducer

```javascript
import { createAsyncReducer } from 'async-redux'

const fooAsync = createAsyncReducer(initialState, {
  FETCH_FOO: (state, action) => action.payload
})

const reducer = combineReducer(
  fooAsync,
  // ...
)
```

### selector

```javascript
import { createAsyncSelector } from 'async-redux'

const fooAsyncSelector = state => state.fooAsync
const barSelector = state => state.foo
const fooAndBarAsyncSelector = createAsyncSelector(
  fooAsyncSelector,
  barSelector,
  (foo, bar) => ({ foo, bar })
)
```

### component

```javascript
import { isFetching, getData } from 'async-redux'

const stateToProps = state => {
  const fooAndBarAsync = fooAndBarAsyncSelector(state)
  return {
    loading: isFetching(fooAndBarAsync),
    ...getData(fooAndBarAsync)    // getData(fooAndBarAsync): { foo, bar }
  }
}

function Panel({loading, foo, bar}) {
  return loading
    ? <p>loading...</p>
    : <p>foo: {foo}, bar: {bar}</p>
}

export default connect(stateToProps)(Panel)

```
