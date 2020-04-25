---
title: Web performance(1)
date: 2020-04-23
---

When talking about web performance, it's not a one time thing. It contains a lot! I will continue this topic in different articles, this one is just a beginning.

So today, I want to discuss *throttling* and *debouncing*.

We have a pain point in real world. Imagine a user repeatly trigger an action in your page rapidly, and it's so fast that your server can not handle it properly.(Normally throttling and debouncing concerns about interaction with server.) It will crush the system! We don't want that happen, but we can't stop users doing that directly! That's where throttling and debouncing come from.

Those actions are probably like these:

- Window resize
- Scrolling
- Ajax calls

Now let's see how to solve it.

### Debouncing

What is debouncing? The main idea is when an action happens, setup a condition, it could be a timer or counter, anything. If the condition being fulfilled, respond to the action, otherwise, no matter how many times the action triggers during the period, neglect it! Let me give you an example.

Say, you are a bus driver, and you got a passenager on board, if you are not crazy, you would not drive away immediately, you will wait for others. Because if you do, the whole bus gonna only contains you and the other passenager, just 2! That's not reasonable, right? However, how do you wait? Well, you have one choice:

- Debouncing: You wait 10 minutes, if during the time span, no any other passenagers getting on board, you drive away, otherwise, wait another 10 minutes counting from the last passenager on board.

``` js
var timerId;

function debounce(drive, time) {
  if (timerId) {
    clearTimeout(timerId);
  }

  timerId = setTimeout(drive, time);

}
```

### Throttling

What is throttling? The main idea is this: You setup a condition, no matter what happens, if the condition fulfilled, do the action! If not, do nothing!

We still use the case above. Do the driver(you) has another choice? Surely he does:

- Throttling: You just barely wait 10 minutes no matter what. You don't care if you got zero passenager, or one, or two, or a hundred, you just wait 10 minutes. When time's up, you go.

``` js
var timerId;

function throttle(drive, time) {
  if (timerId) {
    return;
  }

  timerId = setTimeout(() => {
    drive();
    clearTimeout(timerId);
  }, time);

}
```

These are basicly the gist of *debouncing* and *throttling*. It's quite confusing honestly. I'm not an expert on this topic, actually I still keep learning the two concepts. If there are any incorrect places, please let me know.
