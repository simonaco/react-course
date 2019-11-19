---
path: "/testing"
title: "Testing"
order: 10
---

<iframe src="https://docs.google.com/presentation/d/1yRAzXctdr_Vp_PLQ1O9ODvzNZ4bOj1qvi3EOckpPq94/embed?start=false&loop=false&delayms=3000" frameborder="0" width="960" height="569" allowfullscreen="true" mozallowfullscreen="true" webkitallowfullscreen="true"></iframe>

---

```git checkout 10-jest-test```

If you see an error similar to *Please commit your changes or stash them before you switch branches.*, you can either:

1. commit changes to your own repo ```git commit -a -m "message"```

1. stash changes by running ```git stash save "some_name_here" --include-untracked```

---

### Install requirements ###
to install the additional dependencies run `npm i -D @babel/cli @babel/core @babel/plugin-proposal-class-properties
@babel/plugin-syntax-dynamic-import @babel/preset-env @babel/preset-react babel-jest react-test-renderer`
```json
+  "devDependencies": {
+    "@babel/cli": "^7.7.0",
+    "@babel/core": "^7.7.2",
+    "@babel/plugin-proposal-class-properties": "^7.7.0",
+    "@babel/plugin-syntax-dynamic-import": "^7.2.0",
+    "@babel/preset-env": "^7.7.1",
+    "@babel/preset-react": "^7.7.0",
+    "babel-jest": "^24.9.0",
+    "react-test-renderer": "^16.11.0"
```
### Configure to be able to run tests ###

Add a file named `babel.config.js` at the root

```javascript
module.exports = {
  env: {
    test: {
      presets: ['@babel/preset-env', '@babel/preset-react'],
      plugins: ['@babel/plugin-proposal-class-properties'],
    },
  },
}
```

Add a file named `jest.config.js` at the root. This is the way to pass options to the jest test runner. The option
documentation can be found [here](https://jestjs.io/docs/en/configuration)

```javascript
module.exports = {
  verbose: true,
  collectCoverageFrom: ['src/**/*.{js,jsx}'],
  testMatch: ['<rootDir>/**/__tests__/*.js?(x)'],
  transform: {
    '^.+\\.(js|jsx)$': '<rootDir>/node_modules/babel-jest',
    '^.+\\.css$': '<rootDir>/config/jest/cssTransform.js',
  },
  transformIgnorePatterns: ['[/\\\\]node_modules[/\\\\].+\\.(js|jsx)$'],
}
```

In the root also add a transformer for CSS files. Due to the fact that initially we added CSS for styling our components
we need to add some extra config for being able to transform them. Add `cssTransform.js` in `config/jest/`:

```javascript
'use strict'

// This is a custom Jest transformer turning style imports into empty objects.
// http://facebook.github.io/jest/docs/en/webpack.html

module.exports = {
  process() {
    return 'module.exports = {};'
  },
  getCacheKey() {
    // The output is always the same.
    return 'cssTransform'
  },
}
```

### Testing connected components ###

```bash
npm i -D redux-mock-store isomorphic-fetch fetch-mock
```

Things to notice:
- use MemoryRouter to avoid writing the boilerplate for the router
- use thunk to be able to run async actions on the mock store
- use some preformatted data in a js file to simulate fetched data

```javascript
import React from 'react'
import 'isomorphic-fetch'
import thunk from 'redux-thunk'
import { Provider } from 'react-redux'
import App from '../index'
import renderer from 'react-test-renderer'
import data from './books'
import { MemoryRouter } from 'react-router'
import configureStore from 'redux-mock-store'

const mockStore = configureStore([thunk])

let store

beforeEach(() => {
  store = mockStore({
    appReducer: {
      books: data,
      loading: false,
    },
  })
})

test('App is rendered ok', () => {
  const component = renderer.create(
    <Provider store={store}>
      <MemoryRouter>
        <App />
      </MemoryRouter>
    </Provider>
  )
  expect(component.toJSON()).toMatchSnapshot()
})
```
Add a sample data file in `src/components/app/__tests__/books.js` this will be the redux state books state:

```javascript
export default [
  {
    id: '1',
    isbn: '439023483',
    isbn13: '9.78043902348e+12',
    authors: 'Suzanne Collins',
    original_publication_year: '2008.0',
    original_title: 'The Hunger Games',
    language_code: 'eng',
    average_rating: '4.34',
    small_image_url:
      'https://images.gr-assets.com/books/1447303603s/2767052.jpg',
  },
  {
    id: '2',
    isbn: '439554934',
    isbn13: '9.78043955493e+12',
    authors: 'J.K. Rowling, Mary GrandPré',
    original_publication_year: '1997.0',
    original_title: "Harry Potter and the Philosopher's Stone",
    language_code: 'eng',
    average_rating: '4.44',
    small_image_url: 'https://images.gr-assets.com/books/1474154022s/3.jpg',
  },
  {
    id: '3',
    isbn: '316015849',
    isbn13: '9.78031601584e+12',
    authors: 'Stephenie Meyer',
    original_publication_year: '2005.0',
    original_title: 'Twilight',
    language_code: 'en-US',
    average_rating: '3.57',
    small_image_url: 'https://images.gr-assets.com/books/1361039443s/41865.jpg',
  },
  {
    id: '4',
    isbn: '61120081',
    isbn13: '9.78006112008e+12',
    authors: 'Harper Lee',
    original_publication_year: '1960.0',
    original_title: 'To Kill a Mockingbird',
    language_code: 'eng',
    average_rating: '4.25',
    small_image_url: 'https://images.gr-assets.com/books/1361975680s/2657.jpg',
  },
  {
    id: '5',
    isbn: '743273567',
    isbn13: '9.78074327356e+12',
    authors: 'F. Scott Fitzgerald',
    original_publication_year: '1925.0',
    original_title: 'The Great Gatsby',
    language_code: 'eng',
    average_rating: '3.89',
    small_image_url: 'https://images.gr-assets.com/books/1490528560s/4671.jpg',
  },
]
```
Let's add an extra test to the app component and an aftereach to clear the fetch mock
```javascript
afterEach(() => {
  fetchMock.restore()
})
......
test('Fetches books on load', () => {
  fetchMock.getOnce('/books', {
    body: data,
    headers: { 'content-type': 'application/json' },
  })

  const expectedActions = [
    { type: FETCH_BOOKS_STARTED, payload: { loading: true } },
    {
      type: FETCH_BOOKS_SUCCESS,
      payload: { books: data, loading: false },
    },
  ]

  const store = mockStore({ books: [] })

  return store.dispatch(fetchBooks('/books')).then(() => {
    expect(store.getActions()).toEqual(expectedActions)
  })
})
```


### Individual exericses ###
Fix the rest of failing tests. It will feel amazing once everything is green.
