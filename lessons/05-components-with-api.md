---
path: "/components-with-api"
title: "Components using API"
order: 5
---
---

In this session we'll learn:

- Use our JSON mocked API and build a proper Material UI table
- Add sorting to our Material UI table
- Add client side pagination to our table

---

### Building our table

In order to have a table we need table headers. We will have them as a static collection on our client side.

We should add them at the top of our component hierarchy in `src/app/index.js`

```JSON
const columnData = [
  { id: 'isbn', numeric: false, disablePadding: false, label: 'ISBN' },
  { id: 'isbn13', numeric: false, disablePadding: false, label: 'ISBN13' },
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
    id: 'thumbnail',
    numeric: false,
    disablePadding: false,
    label: 'Thumbnail',
  },
]
```

In order to make our component work correctly with filtering we should move the `filteredBooks` to our `render()`
method. This is required because the a re-render needs to be triggered on every filter change and we managed to break
that when we changed the search function. This is a better approach because we are not passing as many callbacks into
the children and the logic is easier to reason about.

```javascript
  render = () => {
    const { books, filter } = this.state
    const filteredBooks = books.filter(book => book.title.includes(filter))
    ...
  }
```

The table will be its very own component since it will have extra functionality: sortability, pagination. When we have
an object that has rich functionality we generally want it to be its own component.

Add this new line replacing the material list in `src/components/app/index.js`:

```javascript
    <BookList books={filteredBooks} columnHeaders={columnData} />
```

And add the line that imports the component:
```javascript
import BookList from '../booklist'
```
This is broken because we have not yet implemented the child components, so let's do that now.


### Recommended React project structure

The recommended way of structuring components in your react project as recommended by Dan Abramov:

<iframe src="http://react-file-structure.surge.sh/" frameborder="0" width="100%" height="960" allowfullscreen="true" mozallowfullscreen="true" webkitallowfullscreen="true"></iframe>

### BookList
At the time of writing my preferred way of structuring my components is this:
```
▾ booklist/
  ▾ components/
      book-list-header.js
      book.js
      styles.js
    index.js
    styles.js

```
Advantages I noticed are that you can use copy paste to move these components around if needed. The `components/` folder
contains subcomponents of the main component.

Start by adding the `src/components/booklist/index.js` with the following content:

Imports:
```javascript
import React, { Component } from 'react'

import PropTypes from 'prop-types'
import { withStyles } from '@material-ui/core/styles'
import Paper from '@material-ui/core/Paper'
import Table from '@material-ui/core/Table'
import TableBody from '@material-ui/core/TableBody'

import styles from './styles'
import BookListHeader from './components/book-list-header'
import Book from './components/book'
```

Code:
```javascript
class BookList extends Component {
  static propTypes = {
    columnHeaders: PropTypes.arrayOf(PropTypes.object).isRequired,
    books: PropTypes.arrayOf(PropTypes.object).isRequired,
  }

  render = () => {
    const { classes, columnHeaders, books } = this.props
    return (
      <Paper className={classes.root}>
        <div className={classes.tableWrapper}>
          <Table className={classes.table} aria-labelledby="tableTitle">
            <BookListHeader
              columnHeaders={columnHeaders}
              rowCount={books.length}
            />
            <TableBody>
              {books.map(book => (
                <Book key={book.id} {...book} />
              ))}
            </TableBody>
          </Table>
        </div>
      </Paper>
    )
  }
}

export default withStyles(styles)(BookList)
```

React Material uses CSS in JS, and styles are injected using `withStyles` from the `src/components/booklist/styles.js`
```javascript
const styles = theme => ({
  root: {
    maxWidth: '80%',
    margin: 'auto',
    marginTop: theme.spacing(3),
  },
  table: {
    minWidth: 1020,
    fontFamily: 'Ubuntu',
  },
  tableWrapper: {
    overflowX: 'auto',
  },
})

export default styles
```

### Sub Components `./components/`
`./components/book-list-header.js`
Imports:
```javscript
import React, { Component } from 'react'
import PropTypes from 'prop-types'
import TableHead from '@material-ui/core/TableHead'
import TableRow from '@material-ui/core/TableRow'
import TableCell from '@material-ui/core/TableCell'
import { withStyles } from '@material-ui/core/styles'
import styles from './styles'
```
Code:
```javascript
class BookListHeader extends Component {
  static propTypes = {
    columnHeaders: PropTypes.arrayOf(PropTypes.object).isRequired,
  }

  render = () => {
    const { classes, columnHeaders } = this.props

    return (
      <TableHead>
        <TableRow>
          {columnHeaders.map(column => (
            <TableCell
              key={column.id}
              numeric={column.numeric ? column.value : undefined}
              padding={column.disablePadding ? 'none' : 'default'}
              className={classes.tableCell}
            >
              {column.label}
            </TableCell>
          ))}
        </TableRow>
      </TableHead>
    )
  }
}

export default withStyles(styles)(BookListHeader)
```

`./components/book.js`

Imports:
```javascript
import React from 'react'
import PropTypes from 'prop-types'

import { withStyles } from '@material-ui/core/styles'
import TableCell from '@material-ui/core/TableCell'
import TableRow from '@material-ui/core/TableRow'

import styles from './styles'
```
Code:
```javascript
const Book = ({
  classes,
  isbn,
  isbn13,
  authors,
  original_title,
  original_publication_year,
  average_rating,
  language_code,
  small_image_url,
}) => {
  return (
    <TableRow>
      <TableCell className={classes.tableCell}>{isbn}</TableCell>
      ...
      <TableCell>
        <img src={small_image_url} alt={original_title} />
      </TableCell>
    </TableRow>
  )
}

Book.propTypes = {
  ...
}

export default withStyles(styles)(Book)
```

### Individual exercise
Convert the `./src/components/booklist/components/book.js` to avoid repetitive jsx using map.
For this we will have to:
1) Reduce payload coming from the API so that the table columns map to it 1 to 1
2) Pass the `props` to the component so that we can have a loop for rendering the columns in the header
3) Refactor `Book` component to consume the new passed in `props`

### Sortable by columns
Add a function that constructs a sorting function based on the order required
```javascript
function getSorting(order, orderBy) {
  return order === 'desc'
    ? (a, b) => (b[orderBy] < a[orderBy] ? -1 : 1)
    : (a, b) => (a[orderBy] < b[orderBy] ? -1 : 1)
}
```

Inside the `BookList` component we need to set a default sorting
```javascript
  constructor(props) {
    ...
    this.state = {
      order: 'asc',
      orderBy: 'original_title',
    }
  }
```

And we also need to add a handler for sorting
```javascript
  handleRequestSort = (event, property) => {
    const orderBy = property
    const { order } = this.state
    let currentOrder = 'desc'

    if (orderBy === property && order === 'desc') {
      currentOrder = 'asc'
    }

    this.setState({ order: currentOrder, orderBy })
  }
```

In the `src/components/booklist/components/book-list-header.js` we need to add the sorting capabilities on each of the
table column headers

Add a handler creator
```javascript
  createSortHandler = property => event => {
    const { onRequestSort } = this.props
    return onRequestSort(event, property)
  }
```

Component `render()` update:
```javascript
  render = () => {
    const { order, orderBy } = this.props
    const { classes, columnHeaders } = this.props

    return (
      <TableHead>
        <TableRow>
          {columnHeaders.map(column => (
            <TableCell
              key={column.id}
              numeric={column.numeric ? column.value : undefined}
              padding={column.disablePadding ? 'none' : 'default'}
              className={classes.tableCell}
              sortDirection={orderBy === column.id ? order : false}
            >
              <Tooltip
                title="Sort"
                placement={column.numeric ? 'bottom-end' : 'bottom-start'}
                enterDelay={300}
              >
                <TableSortLabel
                  active={orderBy === column.id}
                  direction={order}
                  onClick={this.createSortHandler(column.id)}
                >
                  {column.label}
                </TableSortLabel>
              </Tooltip>
            </TableCell>
          ))}
        </TableRow>
      </TableHead>
    )
  }
```

### Pagination
Luckily `@material-ui/core/TablePagination` gives a great option for a drop in component for client side pagination

It only needs to be imported into the `src/components/booklist/index.js`
```javascript
import TablePagination from '@material-ui/core/TablePagination'
```

Add the initial state to the BookList component:
```javascript
  constructor(props) {
    super(props)
    this.state = {
      ...
      page: 0,
      rowsPerPage: 10,
    }
  }
```

Add handlers for changing the page and rowsPerPage, storing them in the component state
```javascript
  handleChangePage = (event, page) => {
    this.setState({ page })
  }

  handleChangeRowsPerPage = event => {
    this.setState({ rowsPerPage: event.target.value })
  }

```

Update the render method to make use of the pagination abilities
```javascript
  render = () => {
    ...
    const { order, orderBy, rowsPerPage, page } = this.state
    const start = page * rowsPerPage
    const end = start + rowsPerPage
    return (
      <Paper className={classes.root}>
        ...
        <TablePagination
          component="div"
          count={books.length}
          rowsPerPage={rowsPerPage}
          page={page}
          backIconButtonProps={{ 'aria-label': 'Previous Page' }}
          nextIconButtonProps={{ 'aria-label': 'Next Page' }}
          onChangePage={this.handleChangePage}
          onChangeRowsPerPage={this.handleChangeRowsPerPage}
        />
      </Paper>
    )
  }
```

## Exercises:
We managed to introduce quite a few warnings in the browser console, let's try and clean it up before we move on to the
next chapter
