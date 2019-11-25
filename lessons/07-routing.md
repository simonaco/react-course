---
path: "/routing"
title: "Routing"
order: 8
---

<iframe src="https://docs.google.com/presentation/d/e/2PACX-1vS59-YxmCSsmB21HkVFdJHTUmDE_HDpTRyj9BFgm5bTXn3H1A2IERw7RMnoMsJBVvjZY8c_-u4M87df/embed?start=false&loop=false&delayms=30000" frameborder="0" width="960" height="569" allowfullscreen="true" mozallowfullscreen="true" webkitallowfullscreen="true"></iframe>

---

In this session we'll learn:

- Use React Router in your application

---

```git checkout 06-react-redux-router```

If you see an error similar to _Please commit your changes or stash them before you switch branches._, you can either:

1. commit changes to your own repo ```git commit -a -m "message"```

1. stash changes by running ```git stash save "some_name_here"```--include-untracked`

---

## 1. C.R.U.D. ##
We need to add basic CRUD `(CREATE, READ, UPDATE, DELETE)`. In order to do that we also need to incorporate some sort of
routing mechanism to the application.

The React provided router is `"react-router-dom"`

### First we want to install the prerequisites for wiring up redux ###

`npm i react-router-dom`

### Let's get our hands dirty with some code ###

## 1. Add routing infra ##

Create routes and wire them into redux

Add `src/routes/index.js`

```javascript
import React, { Fragment } from "react";
import PropTypes from "prop-types";
import { Provider } from "react-redux";
import { BrowserRouter as Router, Switch, Route } from "react-router-dom";

import App from "../components/app";
import EditBook from "../components/edit";

const Routes = ({ store }) => (
  <Provider store={store}>
    <Router>
      <Fragment>
        <Switch>
          <Route path="/" render={props => <App {...props} />} />
          <Route path="/edit" render={() => <EditBook />} />
        </Switch>
      </Fragment>
    </Router>
  </Provider>
);

Routes.propTypes = {
  store: PropTypes.shape({}).isRequired
};

export default Routes;
```

## 2. Update index.js

```javascript
import Routes from "./routes";

ReactDOM.render(
  <Routes store={configureStore()}>
    <App />
  </Routes>,
  document.getElementById("root")
);
```

## 3. Include Link component

```javascript
import { Link } from 'react-router-dom'

...

<Link to={{ pathname: "/edit", state: { bookId: book.id } }}>
  <Fab color="secondary" aria-label="edit">
    <EditIcon />
  </Fab>
</Link>
```

## 4. Pass history as a prop

We need to inject `history` into our components to enable programatic navigation. Destructure the prop in the top component in our case the App component. This provides us with an API similar to the browser history.

```javascript
const { books, loading, history } = this.props;

...

<BookList
  books={filteredBooks}
  loading={loading}
  columnHeaders={headers}
  history={history}
/>
```

In booklist/index.js pass history

```javascript
const { classes, columnHeaders, books, loading, history } = this.props

<Book
  key={book.id}
  book={book}
  typesMapping={rowColumns}
  history={history}
/>
```

## 5. Extract bookId from history and display it

```javascript
const EditBook = ({ classes, history }) => {
  const { bookId } = history.location.state
  ...
  <h1>Book Editing Form for book id {bookId}</h1>
  ...
}
```
