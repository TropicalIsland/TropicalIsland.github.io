---
layout: post
title: "JavaScript Horrorshow: Function"
categories: JavaScript
keywords: JavaScript, Object, Grammar, Best Practice, Function, Method
---

# What's in a Function?
Ah functions, who doesn't love a good function? In a general sense functions are just the movements that make the data models that represent our domain problems contort and dance towards a useful result. That is in an essence software development. But what are functions in JavaScript? Well their weird, let's take a look at them.


# Not objects again!
Yep functions are objects. Like Arrays, functions are a type of exotic object that have unique interfaces over common objects. But just like Arrays, you can do the classic weird things to them like so
```javascript
var myFun = function() {return "Hello!"}
myFun.wat = "weird""
myFun[0] = "Not this again!" 
```
So evil aside, these things are very not useful and should typically be avoided. There are some arguments that creating function objects can be useful, but I don't want to get too into why that may or may not be useful

TODO: Finish this I guess

