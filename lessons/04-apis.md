---
path: "/api"
title: "Connect to an API"
order: 5
---

<iframe src="https://docs.google.com/presentation/d/e/2PACX-1vRdoBNP7v2MpLM_4ra47p4xqrKeupSa5agh5FJdNolj1bAbeEwXEWJGPv7s6Nc3Weechu70axXXk-tz/embed?start=false&loop=false&delayms=30000" frameborder="0" width="960" height="569" allowfullscreen="true" mozallowfullscreen="true" webkitallowfullscreen="true"></iframe>

---

In this session we'll learn:

- Use JSON server to create an API from a JSON file
- Read data using fetch

---

```git checkout 02-react-components-breakdown```

If you see an error similar to *Please commit your changes or stash them before you switch branches.*, you can either:

1. commit changes to your repo ```git commit -a -m "message"```

1. stash changes by running ```git stash save "some_name_here" --include-untracked```

### Create CRUD API using JSON Server

1. Install JSON Server ```npm install -g json-server```

1. Create folder *server* and *server-config.json* file with:

```json
{
  "port": 4242
}
```

1. Move *books.json* file to the *server* folder

1. Test that local server works by running it locally ```json-server server/books.json --port 4242``` and then opening this url in the browser http://localhost:4242/books/1

### Start json server and webpack when starting app using npm

1. Install npm-run-all ```npm i npm-run-all```

1. Update list of scripts in package.json to start server & client

```json
"scripts": {
  "start": "npm-run-all --parallel server client",
  "server": "npx json-server server/books.json --port 4242",
  "client": "react-scripts start",
  "build": "react-scripts build",
  "test": "react-scripts test",
  "eject": "react-scripts eject"
}
```

### Fetch books list from JSON server API

1. Update package.json to use proxy api requests in development: ```"proxy": "http://localhost:4242"```

1. Update App.js component to fetch books from the API in the componentDidMount lifecycle method:

```javascript
fetch('/books/?_page=1')
  .then(response => response.json())
  .then(json => this.setState({
    books: json
}))
```
