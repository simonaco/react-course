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
PS: if you haven't cloned the repo, now is a good time to do it. here's the
[link](https://github.com/adaschevici/goodreads)

We'll start off the first branch in the git repository

```git checkout 01-react-components```

If you see an error similar to *Please commit your changes or stash them before you switch branches.*, you can either:

1. commit changes to your repo ```git commit -a -m "message"```

1. stash changes by running ```git stash save "some_name_here" --include-untracked```

### Part 1: Display list of elements from object
---

1. create a new folder `src/components/app/`

1. Move App.* files in the new folder and rename to `index.*`

1. Inside `src/components/app/index.js` create a class that will serve as our main App component
```javascript
class App extends Component {
  constructor(props) {
    super(props)
  }

  render = () => {
    return (
      <div>Your code will go here</div>
    )
  }
}

export default App
```

1. copy book.json from URL: https://github.com/adaschevici/goodreads/blob/02-react-components-breakdown/src/books.json to `src/books.json`

1. import it: ```import { books } from '../../books.json'```

1. Create constructor and initialize state with books ```this.state = {books: books.slice(0,20)}```

1. Display books in ul element

```javascript
<ul>
  {this.state.books.map((book, index)=> (
    <li key={index}>{ `${book.title} - ${book.authors}`}</li>
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
2. Now because we have adjacent elements in a react component we need to wrap them in a parent element otherwise the render would throw
   errors. In React we can do this using `Fragment`. Add these imports at the top in `src/components/app/index.js`
   ```javascript
   import React, { Component, Fragment } from 'react'
   ```
3. Add button and listen to click:

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
React does type validation on props to enforce a standardized API, but it does not raise errors, it's like type hints
To do this we need to add the `prop-types` npm package. Previously this was part of react but now it's its own package

to install it run `npm i prop-types`

1. Refactor `src/components/app/index.js` to extract input and button into a Search component

- Create *`src/components/search/components`* folder structure
- Create input component as functional inside `src/components/search/components/input.js`:

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

- Create smart component Search in `src/components/search/index.js`

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
