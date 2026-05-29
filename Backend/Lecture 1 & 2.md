# Express Backend Server – Concepts & Notes

## 1. Project Structure
-  **package.json**  
  - Defines metadata about the project.
  - Lists dependencies, entry points, and scripts.
- **server.js**  
  - Main backend server file using [Express](https://expressjs.com/).

---

## 2. Initializing a Node.js Project
### Terminal Commands

```bash
# Initialize new Node.js project and answer prompts
npm init
# |– package.json gets created
# If you want to quickly create a package.json with default answers
npm init -y
```

---

## 3. Installing Dependencies
### What are Dependencies?

> Code libraries your project needs to function (listed in `"dependencies"` in `package.json`).
- **express**: Minimalist web framework for Node.js, makes backend routing and server setup simple.
- **start**: Not usually needed; comes from the npm init prompt. (You don't need to keep this for a normal Express project.)
### Terminal Commands
```bash
# Install Express (and update package.json)
npm install express

```

---

## 4. Understanding `package.json`

```json
{
  "name": "backend",
  "version": "1.0.0",
  "description": "Start backend",
  "type": "module", // very important as we are using import statement for express
  "main": "Server.js",
  "scripts": {
    "start": "node server.js"
  },
  "dependencies": {
    "express": "^5.2.1"
  }
}
```

- `"type": "module"`: Allows `import` syntax in JS files (ESModules, not CommonJS / `require()`).
- `"main"`: Entry point for the app.
- `"scripts"`: Shorthand for common commands (`npm start`).

---

## 5. Writing & Understanding `server.js`

### Import Modules

```js
import express from "express"
```

- `import` – loads the Express framework.

### Create Application

```js
const app = express();
```

- `app` is your main server instance.

``` js
require("dotenv").config()
const express = require('express')
const app = express() // Create an instance of the Express application

app.get('/', (req, res) => {
  res.send('Hello World!')

})
  
app.get('/twitter', (req, res) => {

  res.send('Hello Twitter!')
})

app.get("/login", (req, res) => {
    res.send("<h1>Please login at website</h1>")
})

app.listen(process.env.PORT, () => {
  console.log(`Example app listening on port ${port}`)

})
```

---

### Serve Static Files

```js
app.use(express.static('dist'))
```

- Serves all files in `/dist` folder (e.g., client HTML, JS, CSS) as static assets.

---

### Define Routes

```js
app.get('/', (req, res) => {
  res.send("Server is ready");
});
```

- `app.get` creates a handler for GET requests to `/`.
- `res.send` sends a plain string as a response.
- **(Bug:** parameter order should be `(req, res)` not `(res, req)`!)

---

### Set Port & Start Server

```js
const port = process.env.PORT || 5000;
app.listen(port, () => {
  console.log(`Server at http://localhost:${port}`);
});
```

- Prefer a port from environment variables for deployment (e.g., Heroku, Vercel).
- Local default (`5000`) for local development.
- `.listen()` starts the server.

---

## 6. Running the Server

### Install Dependencies

One time:
```bash
npm install      # Installs all dependencies listed in package.json
```

### Start The Server

Every time you want to run it:
```bash
npm start        # Runs 'node server.js' as defined in package.json scripts
# or, directly:
node server.js
```

---

## 7. Common Issues

- **SyntaxError:** Must use `import` if `"type": "module"` is set.
- **Port already in use:** Change the port in code or stop other servers.
- **DeprecationWarning:** Always check docs for new method signatures, especially with major version upgrades.

---

# Axios

- **Axios** is a Promise-based HTTP client for JavaScript.
- Used to make HTTP requests (GET, POST, etc.) from browsers or Node.js.
- Install: `npm install axios`
- Example:
  ```js
  import axios from "axios";
  axios.get('/api/data')
    .then(res => console.log(res.data))
    .catch(err => console.error(err));
  ```
- Easier syntax and better error handling than `fetch`.

## 8. Quick Reference

| Command            | Purpose                    |
|--------------------|---------------------------|
| npm init           | Start a Node.js project   |
| npm install <pkg>  | Add package dependency    |
| npm start          | Run the server            |
| node server.js     | Run server directly       |

---

## 9. Useful Links

- [npm: package.json docs](https://docs.npmjs.com/cli/v10/configuring-npm/package-json)
- [Express.js Guide](https://expressjs.com/en/starter/hello-world.html)
- [Node.js Modules (ES Modules)](https://nodejs.org/api/esm.html)

---

## 10. Things To Try

- Change the `'dist'` folder to `'public'` and move an `index.html` there to test static file serving.
- Add more routes (e.g., `/api/info`) for experimenting with API responses.
- Install [nodemon](https://nodemon.io/) for auto-reload on code changes.

```bash
npm install --save-dev nodemon
npx nodemon server.js    # Runs with auto-reload
```

---