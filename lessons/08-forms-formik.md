---
path: "/forms"
title: "Forms with Formik"
order: 9
---

<iframe src="https://docs.google.com/presentation/d/e/2PACX-1vSk-gzr_tcJPY-ldS9BcIFhlV9H-UXVuLVqJRY6rA4Uy8-BNcAFgP19Mh-88UYpzMoXke6cjJELp8LJ/embed?start=false&loop=false&delayms=3000" frameborder="0" width="960" height="569" allowfullscreen="true" mozallowfullscreen="true" webkitallowfullscreen="true"></iframe>

---

In this session we'll learn:

- Use Forms and validators in your application

---

```git checkout 07-formik-forms```


If you see an error similar to *Please commit your changes or stash them before you switch branches.*, you can either:

1. commit changes to your own repo ```git commit -a -m "message"```

1. stash changes by running ```git stash save "some_name_here" --include-untracked```

---

### Need to install the prerequisites for building forms and adding validation ###

```bash
npm install yup formik
```

#### The form component that will help add or edit this object should look something like this. Some #### 
#### form fields have been excluded for brevity. Add this code in `src/components/edit/form.js` ####

```javascript
import React from 'react'
import Button from '@material-ui/core/Button'
import TextField from '@material-ui/core/TextField'

export default props => {
  const {
    isSubmitting,
    handleSubmit,
    values: {
        authors: authors
    },
    errors,
    handleChange,
    isValid,
  } = props
  return (
    <form onSubmit={handleSubmit}>
      <TextField
        id="authors"
        name="authors"
        label="Author"
        value={authors}
        helperText={errors.authors}
        error={Boolean(errors.authors)}
        onChange={handleChange}
        fullWidth
      />
      <Button
        type="submit"
        fullWidth
        variant="contained"
        color="primary"
        disabled={!isValid || isSubmitting}
      >
        {isSubmitting ? 'Submitting' : 'Submit'}
      </Button>
    </form>
  )
}
```

#### The edit form main component should look like this ###
#### Update the `src/components/edit/index.js` ####

```javascript
import Form from './form'
import validationSchema from './schema'
import { Formik } from 'formik'

const EditBook = ({ classes, history, editBook }) => {
  const locationState = history.location.state

  const { book } = locationState ? locationState : {}
  const formHandler = values => editBook(`/books/${book.id}`, values)
  .....
          <Formik
            initialValues={book}
            validationSchema={validationSchema}
            onSubmit={(values, { setSubmitting }) => {
              setTimeout(() => {
                formHandler(values)
                setSubmitting(false)
              }, 50)
            }}
          >
            {fields => <Form {...fields} />}
          </Formik>
```

#### Even though we may be able to build our own validation IMO using the Yup validationSchema is much easier to use
####

Add this to `src/components/edit/schema.js`. It will validate the fields in the form.

```javascript
import * as Yup from 'yup'

const validationSchema = Yup.object({
    authors: Yup.string('Enter authors').required('Authors are required'),
})

export default validationSchema
```

## Update book.js to pass the entire book object  ####

```javascript
<Link to={{ pathname: '/edit', state: { book } }}>
```

### Individual practice ###
1. Redirect to main route after updating entry
2. Implement `ADD_BOOK` action and hook handler to `BookList`

Bonus:
1. Test that the editing works and fix the subtle bug in the search
2. Implement `DELETE_BOOK` action and hook handler to `BookList`
