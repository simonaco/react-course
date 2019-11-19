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
npm install yup
npm install formik
```

#### The form component that will help add or edit this object should look something like this. Some #### 
#### form fields have been excluded for brevity. Add this code in `src/components/edit/form.js` ####

```javascript
+import React from 'react'
+import Button from '@material-ui/core/Button'
+import TextField from '@material-ui/core/TextField'
+
+export default props => {
+  const {
+    isSubmitting,
+    handleSubmit,
+    values: {
+      authors,
+      original_title: originalTitle,
+      average_rating: avgRating,
+    },
+    errors,
+    handleChange,
+    isValid,
+  } = props
+  return (
+    <form onSubmit={values => console.log(values)}>
+      <TextField
+        id="originalTitle"
+        name="original_title"
+        label="Original Title"
+        value={originalTitle}
+        helperText={errors.original_title}
+        error={Boolean(errors.original_title)}
+        onChange={handleChange}
+        fullWidth
+      />
+      <TextField
+        id="authors"
+        name="authors"
+        label="Author"
+        value={authors}
+        helperText={errors.authors}
+        error={Boolean(errors.authors)}
+        onChange={handleChange}
+        fullWidth
+      />
+      <TextField
+        id="avgRating"
+        name="average_rating"
+        label="Star Rating"
+        value={avgRating}
+        helperText={errors.average_rating}
+        error={Boolean(errors.average_rating)}
+        onChange={handleChange}
+        fullWidth
+      />
+      <Button
+        type="submit"
+        fullWidth
+        variant="contained"
+        color="primary"
+        disabled={!isValid || isSubmitting}
+      >
+        {isSubmitting ? 'Submitting' : 'Submit'}
+      </Button>
+    </form>
+  )
+}
```

#### The edit form main component should look like this ###
#### Update the `src/components/edit/index.js` ####

```javascript
 import React, { Fragment } from 'react'
 import Paper from '@material-ui/core/Paper'
 import withStyles from '@material-ui/core/styles/withStyles'
+import { Formik } from 'formik'
 import { withRouter } from 'react-router'
+import validationSchema from './schema'
 import styles from './styles'
+import Form from './form'

 const EditBook = ({ classes, history }) => {
+  const locationState = history.location.state
+  const { book } = locationState ? locationState : {}
   return (
     <Fragment>
       <div className={classes.container}>
         <Paper elevation={1} className={classes.paper}>
-          <h1>Book Editing Form for book id {bookId}</h1>
+          <h1>Book Editing Form for book id {book.id}</h1>
+          <Formik initialValues={book} validationSchema={validationSchema}>
+            {fields => <Form {...fields} />}
+          </Formik>
// -------------Skipped for brevity ----------------
```

#### Even though we may be able to build our own validation IMO using the Yup validationSchema is much easier to use
####

Add this to `src/components/edit/schema.js`. It will validate the fields in the form.

```javascript
+import * as Yup from 'yup'
+
+const validationSchema = Yup.object({
+  original_title: Yup.string('Enter the title').required('A title is required'),
+  authors: Yup.string('Enter authors').required('Authors are required'),
+  average_rating: Yup.number('Enter average rating').test(
+    'test-valid-rating',
+    'Must be greater than 1',
+    rating => rating > 1
+  ),
+})
+
+export default validationSchema
```

## And now for the 🎉🎉🎉🎉FUN🎉🎉🎉🎉 part ##
#### Lets add CRUD abilities to our form in order to modify the mock API book list ####

- `editBook` actions that submit the payload to the endpoints
- `connect` the `EditBook` component so that it can interact with the endpoints

#### 1. What you want to do is get the edit form connected to the store so that you are able to use async actions ####
```javascript
 import React, { Fragment } from 'react'
+import { connect } from 'react-redux'
 import Paper from '@material-ui/core/Paper'
 import withStyles from '@material-ui/core/styles/withStyles'
 import { Formik } from 'formik'
// -------------Skipped for brevity ----------------
 import validationSchema from './schema'
 import styles from './styles'
 import Form from './form'
+import { editBook } from './actions'

+const EditBook = ({ classes, history, addBook, editBook }) => {
   const locationState = history.location.state
   const { book } = locationState ? locationState : {}
+  const formHandler = values => editBook(`/books/${book.id}`, values)
   return (
     <Fragment>
       <div className={classes.container}>
         <Paper elevation={1} className={classes.paper}>
           <BookTitle book={book} />
+          <Formik
+            initialValues={book}
+            validationSchema={validationSchema}
+            onSubmit={(values, { setSubmitting }) => {
+              setTimeout(() => {
+                formHandler(values)
+                setSubmitting(false)
+              }, 50)
+            }}
+          >
             {fields => <Form {...fields} />}
           </Formik>
         </Paper>
// -------------Skipped for brevity ----------------
+const mapDispatchToProps = dispatch => ({
+  editBook: (url, values) => dispatch(editBook(url, values)),
+})
+
+export default connect(
+  null,
+  mapDispatchToProps
+)(withRouter(withStyles(styles)(EditBook)))
```

In the form component we want to replace the dummy handler with the formHandler `src/components/edit/form.js`
```javascript
<form onSubmit={handleSubmit}>
```

### Individual practice ###
1. Redirect to main route after updating entry
2. Implement `ADD_BOOK` action and hook handler to `BookList`

Bonus:
1. Test that the editing works and fix the subtle bug in the search
2. Implement `DELETE_BOOK` action and hook handler to `BookList`
