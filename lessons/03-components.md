---
path: "/components"
title: "React Components"
order: 4
---

<iframe src="https://docs.google.com/presentation/d/e/2PACX-1vSC31WRD2-hP7hsVeDeec9qr3I8MLb72VesnLs5WlhltpTHZyjSDWpJq8TroYpGDAdmmv1oSVuJJ076/embed?start=false&loop=false&delayms=30000" frameborder="0" width="960" height="569" allowfullscreen="true" mozallowfullscreen="true" webkitallowfullscreen="true"></iframe>

---

In this session we'll learn:

- Introducing JSX
- Types of components: functions & class based
- Props & State
- Lifecycle methods

By the end of this session you will have built a search component reading data from a JSON file.

---

We'll start off the first branch in the git repository

```git checkout 01-react-components```

If you see an error similar to *Please commit your changes or stash them before you switch branches.*, you can either:

1. commit changes to your repo ```git commit -a -m "message"```

1. stash changes by running ```git stash save "some_name_here" --include-untracked```

### Part 1: Display list of elements from object
---

1. Delete contents of App.js class

1. copy book.json from URL: https://github.com/adaschevici/goodreads/blob/01-react-components/src/books.json and import it: ```import {books} from './books.json'```

1. Create constructor and initialize state with books ```this.state = {books: books.slice(0,20)}```

1. Display books in ul element

```javascript
<ul>
  {this.state.books.map((book, index)=> (
    <li key={index}>{ `${book.title} - ${book.author}`}</li>
  ))}
</ul>
```

1. Run application ```npm start``` and check browser

## Part 2: Listen to events
---

1. Add text input and listen to changes to it:

```javascript
<input placeholder="Search here..." onChange={this.onChange}></input>

onChange = (event) => {this.setState({searchTerm:event.target.value})}
```

1. Add button and listen to click:

```javascript
<button onClick={this.search}>Search</button>

search = () => {
  this.setState({books:this.state.books.filter(book => book.title.includes(this.state.searchTerm))})
}

```

## Individual tasks I

- Fix search bug: search for Hunger first, search for Harry. Notice nothing is returned anymore. Why? Can you fix it?
- Update search to be case insensitive

## Part 3: Functional components
---

1. Refactor App.js to extract input and button into a Search component

- Create *components -> Search -> components* folder structure
- Create input component as functional:

```javascript
import React from 'react';
import PropTypes from 'prop-types'
const Input = ({onChange}) => {
  return (
    <input placeholder="Search here" onChange={onChange}></input>
  )
}

Input.defaultProps = {}
Input.propTypes = {
  onChange: PropTypes.func.isRequired
}

export default Input;
```

- Create smart component Search

```javascript
import React from 'react'
import Input from './components/input'

class Search extends React.Component {
  constructor(props){
    super(props);
    this.state = {
      input:''
    }
  }

  handleChange = value => this.setState({ input: value })

  render() {
    const { input } = this.state;
    return (
      <div className="search">
        <Input onChange={event => this.handleChange(event.target.value)}/>
      </div>
    )
  }
}

export default Search;
```

## Individual tasks II

- Extract search button into functional component
