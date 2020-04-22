---
title: About authentication
date: 2020-04-22
---

Recently I have been doing 2 of my personal projects: [jTrello](http://trello.zhangkai.site) and [jContact](http://contact.zhangkai.site). Both of the two had the functionality of user register and login, which is about authentication. It means this web app is not fully available to all public users. It may has some parts which everyone can access into, but there are definitely some others which are only open to authenticated users. That's why it called authentication.

So how to do authentication in a web app? I will take MERN(Mongo, Express, React, Node) as my tech stack.

Firstly, Let's imagine this scenario: when a user comes to your app and by accidentally hit a non-public route, at this pointer, if the app finds out this user can't access the next content, which means he/she is not authenticated, you should remind the user to go register or login. Here is a important point: How does the app know a user is not authenticated? I will explain it later in this chapter. For now let's move on.

Secondly, let's say the user did go to register, and finished the step, you should send back a jwt token to the user, like this:

``` js
const token = jwt.sign({id: user._id}, process.env.JWT_SECRET, {expiresIn: process.env.JWT_EXPIRESIN});

res.json({ name: user.name, token });

```

As we can see above, you can set some params into the token.

When the frontend get the token, It should set the status 'isAuthenticated' as *true*, and bang now the user can access the route! Not only the route, any private routes now can be accessiable to the user! Is it easy? YES! But.... (You know there is a but, haha)

How do we know the route is private, how do we define private. Actually private routes are no difference but a pre-enter check to see if the status 'isAuthenticated' is *true*. As we set above, if it is true, move on, else, go to register/login. I will take React as a example to illustrate how to do pre-enter check.

``` js
function PrivateRoute({ children, ...rest }) {
  return (
    <Route
      {...rest}
      render={({ location }) =>
        isAuthenticated ? (
          children
        ) : (
          <Redirect
            to={{
              pathname: "/login",
              state: { from: location }
            }}
          />
        )
      }
    />
  );
}
```

The key of whole process for frontend is to setup the status 'isAuthenticated'. What is the initial value of this state? *false*? well, yes and no. Remember the question I threw out in the beginning? How does the app know a user is not authenticated? Let's answer it. In the intial app loading process, the app should check localStorage/sessionStorage if there is a token in it. If it does, then the app assumes it is an authenticated user! And set its status 'isAuthenticated' as *true*. Keep the ball rolling.

You may have a question, what if there is a token in localStorage, but the token for some reason is not correct, it may be overdued or disformated, anyways, it just not a correct token. Does the app still think it is an authenticated user? Yes it does! What?!

Let me explain. It needs the app has a global api error handling mechanism. If any api calls failed due to the incorrect token, do logout and reload. Like below:

``` js
axios.interceptors.response.use(
  (response) => response,
  (error) => {
    if (error.response.status === 401) {
      store.dispatch('logout');
      location.reload();
    }

    return Promise.reject(error)
  }
);
```

Well That's pretty much it. I know it's a lot. It takes time to go through the whole process and make it clear to you.

If you have any questions, welcome to reach out to me on [twitter](https://twitter.com/Jacknh418).
