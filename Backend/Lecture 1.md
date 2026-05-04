
Express and mongo connect the computer or the device to the server, mongoose is of databse.

/: home route

/login is one of the login setup ( listening is done by the express)

The most basic is GET request.

```
npm init
```
It is the npm package installer
it is used to install a package.json file.

## How to run using Package.js

``` js
{

  "name": "backend_dev",

  "version": "1.0.0",

  "description": "A basic Application to deploy",

  "keywords": [

    "node",

    "hello"

  ],

  "license": "ISC",

  "author": "Affaan Qureshi",

  "type": "commonjs",

  "main": "index.js",

  "scripts": {

    "start": "node index.js",

    "test": "echo \"Error: no test specified\" && exit 1"

  }

}
```

Express: Web framework

## How we install the Express web framework

### Install using "npm install express"
### Use the hello world command provided to paste in to the index file

``` js
const express = require('express')
const app = express() // Create an instance of the Express application
const port = 3000


app.get('/', (req, res) => {
  res.send('Hello World!')
})

app.get('/twitter', (req, res) => {
  res.send('Hello Twitter!')

})
 
app.get("/login", (req, res) => {
    res.send("<h1>Please login at website</h1>")

})
app.listen(port, () => {
  console.log(`Example app listening on port ${port}`)
})
```

env file: