## Serving Static Files(获取静态网页)


![](img/2019-12-20-11-08-17.png)

- there is no way directly get the static .html 
- if we want to get the static file system
- using express
- update `app2.js`

```js
//serving static files
const express = require('express');
const morgan = require('morgan');

const tourRouter = require('./routes/tourRoutes');
const userRouter = require('./routes/userRoutes');

const app = express();

//1. middleware
app.use(morgan('dev'));
app.use(express.json()); //middleware, 中间件
app.use(express.static(`${__dirname}/public`)); //set public to be root folder, thus we don't need to input `public/`
//中间件获得静态文件
//middleware, which get the static files

app.use((req, res, next) => { //middleware
    console.log('Hello from the middleware 🐳');
    next();
});

app.use((req, res, next) => {//middleware
    req.requestTime = new Date().toISOString();
    next();
});

//3. ROUTE

app.use('/api/v1/tours', tourRouter);
app.use('/api/v1/users', userRouter);

module.exports = app;   

```

![](img/2019-12-20-11-18-47.png)

- remember, for here we just server from a folder and not from a route.

---

## Environment Variables

- update `server.js`

```js
//Environment Variables
const app = require('./app2');

console.log(app.get('env'));

const port = 3000;
app.listen(port, () => {
    console.log(`App running on port ${port}...`);
    console.log(`${__dirname}`)
});
```

- console

![](img/2019-12-20-11-39-25.png)

- we see development
- To summary, environment variables are global variables

----

- `server.js`


```js
console.log(process.env);
```

![](img/2019-12-20-11-42-19.png)

- for here, we have a bunch of different variables


### set a environment variable 

- `NODE_ENV=development X=23 nodemon server.js `

![](img/2019-12-20-11-54-25.png)

---

## Create a `config.env`


![](img/2019-12-20-11-51-29.png)

- btw, we can install a plugin: `DotENV`

![](img/2019-12-20-11-57-24.png)

- so now we have syntax highlighting


### now install `dotenv`

![](img/2019-12-20-12-00-00.png)

- now we config environment variable in `server.js`

```js
//Environment Variables
const dotenv = require('dotenv');
dotenv.config({ path: './config.env' });

const app = require('./app2');

//envrionment variables is global variables
// console.log(app.get('env')); 

//
// console.log(process.env);

const port = 3000;
app.listen(port, () => {
    console.log(`App running on port ${port}...`);
    console.log(`${__dirname}`)
});
```

- update `config.env`

![](img/2019-12-20-12-14-20.png)

- run `npm start`, since we configed in package.json

![](img/2019-12-20-12-08-58.png)

- now we hope that `dotenv.config({ path: './config.env' });` happens onec

- in `app2.js`

```js
//Environment Variables
const express = require('express');
const morgan = require('morgan');

const tourRouter = require('./routes/tourRoutes');
const userRouter = require('./routes/userRoutes');

const app = express();

//1. middleware
if (process.env.NODE_ENV === 'development') {
    app.use(morgan('dev'));
}

```

---

### update `server.js`

```js
//Environment Variables
const dotenv = require('dotenv');
dotenv.config({ path: './config.env' });

const app = require('./app2');

//envrionment variables is global variables
// console.log(app.get('env'));

// console.log(process.env);


const port = process.env.PORT || 3000;
app.listen(port, () => {
    console.log(`App running on port ${port}...`);
    console.log(`${__dirname}`)
});
```


### update `app2.js`

```js
//Environment Variables
const express = require('express');
const morgan = require('morgan');

const tourRouter = require('./routes/tourRoutes');
const userRouter = require('./routes/userRoutes');

const app = express();

//1. middleware
console.log(process.env.NODE_ENV);
if (process.env.NODE_ENV === 'development') {
    app.use(morgan('dev'));
}

app.use(express.json()); //middleware, 中间件
app.use(express.static(`${__dirname}/public`));//set public to be root folder, thus we don't need to input `public/`
//中间件获得静态文件
//middleware, which get the static files

app.use((req, res, next) => { //middleware
    console.log('Hello from the middleware 🐳');
    next();
});

app.use((req, res, next) => {//middleware
    req.requestTime = new Date().toISOString();
    next();
});

//3. ROUTE

app.use('/api/v1/tours', tourRouter);
app.use('/api/v1/users', userRouter);

module.exports = app;   

```

### update `package.json`

```json
{
  "name": "natours",
  "version": "1.0.0",
  "description": "Learning node, express and mongoDB",
  "main": "app.js",
  "scripts": {
    "start:dev": "nodemon server.js",
    "start:prod": "NODE_ENV=production nodemon server.js"
  },
  "author": "Jonas Schmedtamnn",
  "license": "ISC",
  "dependencies": {
    "dotenv": "^8.2.0",
    "express": "^4.17.1",
    "mongoose": "^5.8.1",
    "morgan": "^1.9.1"
  }
}
```

- `npm run start:prod`

![](img/2019-12-20-13-30-20.png)

---

- `npm run start:dev`

![](img/2019-12-20-13-33-30.png)