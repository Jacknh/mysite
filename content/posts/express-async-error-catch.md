---
title: Express Async Error Catch
date: 2020-04-02
---

When we deal with express middlewares or controllers, It is very often that we are handling async function. In express, sync errors can be caught up automatically, but what about errors happend in async handlers? We have to **manually** pass the error into *next* function.

So how do we do that?

If you use *async await* syntax, then you have to encapsulate your async function in a *try catch* block. Like below :

``` js
app.get('/', async (req, res, next) => {
  try {
    await someAsyncFunc();
  } catch (error) {
    next(error);
  }
})
```

Imagine if you have a hundred of these functions, you gonna have to write the same code snippet a hundred times ! That's not acceptable.

Express gives a short way to format this. Like below :

``` js
app.get('/', (req, res, next) => {
  Promise.resolve().then(() => {
    someAsyncFunc();
  }).catch(next);
})
```

This way can shorten a little bit. But I would suggest a util function to wrap the whole middleware to make your middleware looks clean. Like below :

``` js
// asyncHandler.js
export default (fn) => (req, res, next) => Promise.resolve(fn(req, res, next)).catch(next)

```

Then you can import this util into your middleware file, and wrap your middleware function with it. You don't need to deal with error case in your middleware now ! How cool is that.
