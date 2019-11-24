---
path: "/redux"
title: "State Management with Redux"
order: 7
---
<iframe src="https://docs.google.com/presentation/d/e/2PACX-1vSjeUl84G2aUeST1imCbHrdN_GbIk_hG6ZcUf_8VmhgJGpzPY3FTFTlpRVriiYcCLX2CJc9eEWyzHJ7/embed?start=false&loop=false&delayms=30000" frameborder="0" width="960" height="569" allowfullscreen="true" mozallowfullscreen="true" webkitallowfullscreen="true"></iframe>

<iframe src="https://docs.google.com/presentation/d/e/2PACX-1vSn5zHbzMHIXWr3HMTDPBTR4po7kmvZALsthRaBn7JIDZ8ABV53nYs_-8r3bY4Zo4iLQT3Eurp0-0Ae/embed?start=false&loop=false&delayms=30000" frameborder="0" width="960" height="569" allowfullscreen="true" mozallowfullscreen="true" webkitallowfullscreen="true"></iframe>

---

```git checkout 04-react-components-material```

If you see an error similar to *Please commit your changes or stash them before you switch branches.*, you can either:

1. commit changes to your own repo ```git commit -a -m "message"```

1. stash changes by running ```git stash save "some_name_here" --include-untracked```

---

1. Install npm dependencies: ```npm i redux react-redux redux-thunk redux-devtools-extension```

## The react app architecture we're proposing is called ducks or re-ducks 🦆
The rule of thumb is all your state management and functionality should live beside your component


### First we want to install the prerequisites for wiring up redux ###

`npm i react-redux redux redux-devtools-extension redux-thunk`


### Let's get our hands dirty with some code ###

1. Create `actions.js` in `src/components/app/`

```javascript
import { FETCH_BOOKS } from './types'

export const fetchBooksAction = books => ({
  type: FETCH_BOOKS,
  payload: {
    books,
  },
})

export const fetchBooksAction = url => dispatch => {
  fetch(url)
    .then(res => res.json())
    .then(books => {
      dispatch(fetchBooksAction(books))
    })
}
```

Actions should be a plain javascript object with a `type` and optionally a `payload`. Good practice is to define your
action types as constants in `src/components/app/types.js`:

```javascript
export const FETCH_BOOKS = 'FETCH_BOOKS'
```

1. Create reducers in *reducer.js* in `src/components/app/reducers.js`

```javascript
import { FETCH_BOOKS } from './actions'

export default (state = {}, action) => {
  switch(action.type) {
    case FETCH_BOOKS:
      return {
        books: action.payload.books
      }
    default:
      return state
  }
}
```

1. Create root reducer in *rootReducer.js* in folder called *reducers*

```javascript
import { combineReducers } from 'redux'
import appReducer from '../components/app/reducers'

export default combineReducers({
  appReducer
})
```

1. Create *index.js* in a new folder called *store* and paste in

```javascript
import { createStore, applyMiddleware } from 'redux'
import { composeWithDevTools } from 'redux-devtools-extension'
import thunk from 'redux-thunk'
import rootReducer from './rootReducer'


const composeEnhancers = composeWithDevTools({
  trace: true
})

export default function configureStore(initialState={}) {
  return createStore(
    rootReducer,
    initialState,
    composeEnhancers(applyMiddleware(thunk))
  )
}
```

1. In index.js configure store:

```javascript
import { Provider } from 'react-redux'
import configureStore from './store'

ReactDOM.render(
  <Provider store={configureStore()}>
    <App />
  </Provider>,
  document.getElementById('root')
)
```

1. In `src/components/app/index.js` configure to read data from store:

```javascript

  componentDidMount() {
    const { fetchBooks } = this.props;
    fetchBooks('/books');
  }

App.defaultProps = {
  books: []
}
App.propTypes = {
  fetchBooks: PropTypes.func.isRequired,
  books: PropTypes.arrayOf(PropTypes.object)
}
const mapStateToProps = state => ({
  books: state.appReducer.books
})

const mapDispatchToProps = dispatch => ({
  fetchBooks: url => dispatch(fetchBooksAction(url))
})
export default connect(mapStateToProps, mapDispatchToProps)(App);

```

## Individual tasks

- Add missing actions - `FETCH_BOOKS_STARTED, FETCH_BOOKS_FAILED`
- Add error handling, API should be turned off
- Add loading state
