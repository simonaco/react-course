---
path: "/create-react-app"
title: "Create React App"
order: 3
---

<iframe src="https://docs.google.com/presentation/d/e/2PACX-1vR35X7kZ1ERi5zWgwQ131ngwceNJD5mbA7-5y0dX76rAQ7QszO9ifQJXsseNm89Uys9OOydiFGsBCYt/embed?start=false&loop=false&delayms=60000" frameborder="0" width="960" height="569" allowfullscreen="true" mozallowfullscreen="true" webkitallowfullscreen="true"></iframe>

---

In this session we'll learn:

- How to bootstrap a new React application
- Build and run a React application using react-scripts
- Advanced create-react concepts

## Steps

1. Install react cli ```npm i create-react-app```

1. Create new project ```npx create-react-app goodreads --use-npm```

1. `cd goodreads`

1. Install `prettier`, this is an almost universal tool for auto-format and lint that is widely accepted. Run `npm i -D
   prettier`

1. Add a file called `.prettierrc` in the goodreads project folder:

```json
{
  "trailingComma": "es5",
  "semi": false,
  "singleQuote": true
}
```

## Tasks

1. Change the style in the app (links, bonus logo)

1. Run the build and run npm script

## Optional

1. Eject configuration ```npm run eject```

The directory structure should become more similar to the following:
```
▾ [ ]config/
  ▾ [ ]jest/
      [ ]cssTransform.js
      [ ]fileTransform.js
    [ ]env.js
    [ ]modules.js
    [ ]paths.js
    [ ]pnpTs.js
    [ ]webpack.config.js
    [ ]webpackDevServer.config.js
▸ [ ]public/
▾ [ ]scripts/
    [ ]build.js
    [ ]start.js
    [ ]test.js
▸ [ ]src/

```
