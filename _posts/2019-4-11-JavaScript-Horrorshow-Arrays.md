---
layout: post
title: "JavaScript Horrorshow: Arrays"
categories: JavaScript
keywords: JavaScript, Array, Object, Grammar, Best Practice, Function, Method
---

## An Education
JavaScript is a weird language. Since apparently I now write it professionally, I've spent the last little while trying to tease out its grisly secrets and understand them. Because by design, it is fundamentally different to almost all popular languages, it might be valuable to explore these "pleasant surprises" and try to reason about some of the insanity that is JavaScript. With that in mind, I figured that a small series of short posts on some of the most "how much can I break the JavaScript?", "Why on earth does the grammar permit this?" moments and have a bit of a collective point and laugh as well as promising never to do any of these naughty things. With this all said and done please enjoy our first installment on Arrays.

## Arrays
What is an Array? If you've come from the land of stack pointers and registers, you'd tell me "it's typically a one-dimensional data structure that contains some fixed-size collection of elements in sequential order". I've fallen asleep. If you've come from the land of applicative functors and monoids then you'd tell me "well it's just sort of a function whose domain is isomorphic to contiguous subsets of the integers". Now my brain has exploded. Lastly, we have JavaScript, tell me JavaScript what is an array? It's an Object.

### A What?
Yes, that's right. "An Object". As with all non-fundamental types &ndash; things that are not numbers, strings, etc. &ndash; at the heart of it, Arrays are just Objects. This means you can do all the things you could do to an Object, to an Array. For example, my Array is sad that it doesn't have a name? 
```javascript
sadArray = [1,2,3]
sadArray.name = "Dave"
```
Well bam, now Dave has a name. You really can do all sorts of crazy things with Objects and almost all of them apply to Arrays too. But that's pretty boring. I'm sure you've seen plenty of weird things like this before, whether it was memoizing a recursive function by giving it a property which holds a results cache or you've already personally been naming Arrays like it's your life mission. That's not what we come to see. That's not _JavaScript_ enough.

### A WHAT?
So if an Array is an Object, can an Object be an Array? Why yes of course, why not?
```javascript
myArray = {}
myArray[0]="YAY"
```
Ok, let's not get too excited, it's just using an integer key to map to a value. We have to use the square bracket notation as `myArray.1` is not valid in the grammar since 1 is an Integer literal and not a valid identifier. Meh boring, let's get weirder!
```javascript
watArray = {}
watArray[-1]="I'm on the wrong side of the real line"
watArray[5.434]="I'm definitely not an integer index"
```
So like before, any number literal appears to be a suitable property key for an Object. As per the grammar, any String or Symbol type can be a key to an Object property. But Numbers aren't any of those!? Do not fear young one, since a key has to be one of those types, when you assign a new property to an Object, if the key is not already a String or Symbol, the runtime will call ToString on your key and store the transformed String value. [ToString](https://www.ecma-international.org/ecma-262/9.0/index.html#sec-tostring) is one of the many builtin type conversions done quite very opaquely by JavaScript, literally all the time, everywhere. As you can see if you look at the grammar, there are lots of types that have default conversions, including Numbers[^1]. This is exactly why you can do the madness above, it's completely legal and required by the grammar.

### The Apex of Array Weirdness
Ok, that's still not too interesting. My Arrays in JavaScript can _do_ things, they have methods to do functional things like map, filter, reduce and normal things like push, pop, shift and unshift. These methods are inherited from the Array.prototype which defines all these. We could call these methods using the `myArray.method()` notation, i.e. a Member Expression, or we could be weird and slightly broken. Weird and slightly broken it is, i.e. `Array.prototype.method.call()`. This calls the method on a `this` and some arguments[^2]. Turns out Array.prototype's methods don't check the `this` value before doing all its magic. It has to coerce the `this`to an Object but that is a fundamentally satisfied aside from when `this` is `Null` or `Undefined`. When we use `Function.prototype.call`, `this` is set to the first argument. So this means we can do the following,
```javascript
waaat = {}
Array.prototype.push.apply(waaat, 1)
```
Go on, try it! You'll see that `waaat` is now `{0:1, length:1}`. Ok, so this is super weird. SUPER WEIRD, love it. You can keep playing calling your Array.prototype methods on your ~~Object~~ array, sorry. If you keep `push`ing on your array, the length will continue to increment and behave all array-like. Now try reassigning length to a lower value,
```javascript
arr = {}
Array.prototype.push.apply(arr, 1)
Array.prototype.push.apply(arr, 2)
arr.length = 1
Array.prototype.push.apply(arr, "WAT")
```
Yep, it'll just write into where it thinks there is space. What, you didn't think calling builtin Array methods on a not Array could go wrong? Well it will. This is a very, very silly idea. Next, try making length too long,
```javascript
arr = {}
Array.prototype.push.apply(arr, 1)
Array.prototype.push.apply(arr, 2)
arr.length = 4
Array.prototype.push.apply(arr, "WAT")
```
Ooo! It doesn't pad the array like actual Arrays. How convenient. The length is totally wrong now though, oh well. 

Wow, at this point I think we've had enough weirdness to make even a JavaScript maestro sweat a little. That should probably do for today.

Despite all this dreadfulness, Arrays are very useful in JavaScript, allowing you to implement a more declarative style using methods like map and also add collection semantics to your code &ndash; i.e. I can have and do stuff to a list of things, not just one thing &ndash; both of these features facilitating the authoring of expressive, useful code. Everything we have seen today is an absolute defamation of these lovely things. Please, if you ever find yourself writing anything at all like that seen above, you are doing it wrong. Let the emotions of this whirlwind tour through JavaScript weird settle into a resolve never to do things as silly as this anywhere near production and be thankful that Arrays are considered Exotic Objects that do exotic things and we get all of this magic functionality in complete correctness for free. That is to say, don't forget to be nice to your Arrays. They're fantastic Objects!

[^1]: As you might have seen, that also includes Objects! To make a String from an Object requires calling the ToPrimitive conversion with a String hint. This delegates to the `toString` and `valueOf` methods of your object if they exist and in that explicit order. So make sure to implement `toString` methods for your objects and you can almost have a real map!

[^2]: This also displays some funky JavaScript inheritance. Array's prototype has methods, these are all hard coded and not rewritable. Since the methods also are Function Objects, they inherit the Function.prototype which gives the call method. For example, `myArray = [1,2,3]; myArray.map.call()`, finds map in the Array.prototype property and then finds call in the prototype of that method, being the Function.prototype. What a wacky language.
