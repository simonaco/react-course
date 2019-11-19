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

```git checkout 06-redux-routing```


If you see an error similar to *Please commit your changes or stash them before you switch branches.*, you can either:

1. commit changes to your own repo ```git commit -a -m "message"```

1. stash changes by running ```git stash save "some_name_here" --include-untracked```

---

## 1. C.R.U.D. ##
We need to add basic CRUD `(CREATE, READ, UPDATE, DELETE)`. In order to do that we also need to incorporate some sort of
routing mechanism to the application.

The React provided router is `"react-router-dom"`


####**Open discussion**: How should we configure the config data so that it is usable for the extra UI elements ?####

**Requirements**:

 Add book button and edit/delete components:
 - the add book button header is a bit different from the other headers because it spans 2 columns so we need to define the different
 structure in the column config object
 - the add button will not be sortable so we need a way to differentiate from the others
 - we need to use the column config object in multiple places now so we should make it an external dependency

####_Possible solution_:####

create file named `src/app/data-mapping.js`
```javascript
export default {
  actionButtons: [
    {
      id: 'edit',
      numeric: false,
      align: false,
      disablePadding: false,
      label: 'Edit',
    },
    {
      id: 'delete',
      numeric: false,
      align: false,
      disablePadding: false,
      label: 'Delete',
    },
  ],
  addButton: {
    id: 'add',
    numeric: false,
    align: false,
    disablePadding: false,
    label: 'Add',
    span: 2,
  },
  sortableColumns: [
    { id: 'isbn', numeric: true, disablePadding: false, label: 'ISBN' },
    { id: 'isbn13', numeric: true, disablePadding: false, label: 'ISBN13' },
    { id: 'authors', numeric: false, disablePadding: false, label: 'Author' },
    {
      id: 'original_title',
      numeric: false,
      disablePadding: false,
      label: 'Title',
    },
    {
      id: 'original_publication_year',
      numeric: true,
      disablePadding: false,
      label: 'Year',
    },
    {
      id: 'average_rating',
      numeric: false,
      disablePadding: false,
      label: 'Star Rating',
    },
    {
      id: 'language_code',
      numeric: false,
      disablePadding: false,
      label: 'Language',
    },
    {
      id: 'small_image_url',
      numeric: false,
      disablePadding: false,
      label: 'Thumbnail',
    },
  ],
}
```

We want to also create components for the two types of headers:

```javascript
import React from 'react'
import Fab from '@material-ui/core/Fab'
import AddIcon from '@material-ui/icons/Add'
import TableSortLabel from '@material-ui/core/TableSortLabel'
import Tooltip from '@material-ui/core/Tooltip'

export const SortableColumnHeader = ({ column, orderBy, order, sorter }) => (
  <Tooltip
    title="Sort"
    placement={column.numeric ? 'bottom-end' : 'bottom-start'}
    enterDelay={300}
  >
    <TableSortLabel
      active={orderBy === column.id}
      direction={order}
      onClick={sorter}
    >
      {column.label}
    </TableSortLabel>
  </Tooltip>
)

export const ActionColumnHeader = ({ column }) => {
  switch (column.id) {
    case 'add':
      return (
        <Fab variant="extended" color="primary" aria-label="add">
          <AddIcon />
          Add book
        </Fab>
      )
    default:
      return null
  }
}
```

## 2. Add routing infra ##

Create routes and wire them into redux

Add `src/routes/index.js`

```javascript
import React, { Fragment } from 'react'
import PropTypes from 'prop-types'
import { Provider } from 'react-redux'
import { BrowserRouter as Router, Switch, Route } from 'react-router-dom'

import App from '../components/app'

const Routes = ({ store }) => (
  <Provider store={store}>
    <Router>
      <Fragment>
        <Switch>
          <Route path="/" render={props => <App {...props} />} />
        </Switch>
      </Fragment>
    </Router>
  </Provider>
)

Routes.propTypes = {
  store: PropTypes.shape({}).isRequired,
}

export default Routes
```

## 3. Add the C.R.U.D. component for editing and adding books##

```javascript
<Route path="/edit" render={() => <EditBook />} />
```

We need to inject `history` into our components to enable programatic navigation. Destructure the prop in the top component in our
case the App component. This provides us with an API similar to the browser history.
```javascript
const { books, loading, history } = this.props
```

For editing books we need to add `<Link />` action to the edit button and we need to be able to pass the book id into
the  Edit form to enable the fields population.

In the Book component which renders each row we add the `<Link ... />` wrapper to pass the id like so:
```javascript
<Link to={{ pathname: '/edit', state: { bookId: book.id } }}>
  <Fab color="secondary" aria-label="edit">
    <EditIcon />
  </Fab>
</Link>
```

Let's also change the edit component and print out the ID of the book
```javascript
import React, { Fragment } from 'react'
import Paper from '@material-ui/core/Paper'
import withStyles from '@material-ui/core/styles/withStyles'
import { withRouter } from 'react-router'
import styles from './styles'

const EditBook = ({ classes, history }) => {
  const { bookId } = history.location.state // <--- this is where the state is stored
  return (
    <Fragment>
      <div className={classes.container}>
        <Paper elevation={1} className={classes.paper}>
          <h1>Book Editing Form for book id {bookId}</h1>
        </Paper>
      </div>
    </Fragment>
  )
}

export default withRouter(withStyles(styles)(EditBook))
```
