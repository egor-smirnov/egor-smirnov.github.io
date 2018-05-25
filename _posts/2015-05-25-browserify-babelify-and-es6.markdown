---
layout: post
title:  "Browserify, Babelify and ES6"
date:   2015-05-25 00:00:00
excerpt: "This post briefly describes Browserify, Babel and Babelify."
---

<img src="{{ site.url }}/images/posts/2015-05-25/browserify.png" alt="Browserify Logo">

*This post is connected with [React and ES6 - Part 1, Introduction into ES6 and React]({{ site.url }}/2015/05/22/react-and-es6-part1.html).*
*It explains the usage of node.js modules described in this article.*

This post briefly describes the purpose of 3 npm modules - [Browserify](http://browserify.org/){:target="_blank"}, 
[Babel](https://babeljs.io/){:target="_blank"} and [Babelify](https://github.com/babel/babelify){:target="_blank"}.

### Example gulpfile.js

Let's assume we have the following `gulpfile.js`:

{% highlight javascript linenos=table %}
var gulp = require('gulp');
var browserify = require('browserify');
var babelify = require('babelify');
var source = require('vinyl-source-stream');

gulp.task('build', function () {
    return browserify({entries: './app.jsx', extensions: ['.jsx'], debug: true})
        .transform(babelify)
        .bundle()
        .pipe(source('bundle.js'))
        .pipe(gulp.dest('dist'));
});

gulp.task('watch', ['build'], function () {
    gulp.watch('*.jsx', ['build']);
});

gulp.task('default', ['watch']);
{% endhighlight %}

You could notice we are using Browserify and Babelify here. Let's dive a bit into them.

### Browserify

> Browserify lets you require('modules') in the browser by bundling up all of your dependencies.
> Source: [http://browserify.org/](http://browserify.org/){:target="_blank"}

To quickly grasp what [Browserify](http://browserify.org/){:target="_blank"} does, let's examine the simple example. 

Say, we have `multiply.js` file with the following content:

{% highlight javascript %}
module.exports = function multiply(a, b) {
  return a * b;
}
{% endhighlight %}

And `app.js`:

{% highlight javascript %}
var multiply = require('./multiply');
console.log(multiply(2, 3)); // => 2 * 3 = 6
{% endhighlight %}

See this `require('./multiply')` statement? You used function from `multiply.js` file inside your `app.js`. 
Without adding `<script>` tag!

The aim of Browserify is to make it possible to run the code above in browser.

How Browserify does this magic is out of the scope of this article. If you are interested,
I would suggest going through various resources about Browserify
[here](http://browserify.org/articles.html){:target="_blank"}.

### Browserify + Babel

Ok, let's assume we now understand what Browserify does. But how Browserify relates to ECMAScript6?

It turns out that ES6 introduces long-awaited module support ([learn more](https://babeljs.io/docs/learn-es6/#modules){:target="_blank"}). 
Usage of modules leads to better-structured code and eliminates issues with the globally defined state.

Let's see how above two files might look using ES6 modules.

*multiply.js*

{% highlight javascript %}
export default function multiply(a, b) {
  return a * b;
}
{% endhighlight %}

*app.js*

{% highlight javascript %}
import {multiply} from 'multiply';
console.log(multiply(2, 3)); // => 2 * 3 = 6
{% endhighlight %}

Nowadays, such a syntax is not supported by browsers out of the box. To tackle this problem, a lot of different solution were proposed.

One of these solutions is using of Babel in conjunction with Browserify. 
Behind the scenes, Babel will transform our code to ECMAScript 5:

*multiply.js*

{% highlight javascript %}

.. some was thrown away for brevity ..

exports["default"] = multiply;
function multiply(a, b) {
  return a * b;
}
module.exports = exports["default"];
{% endhighlight %}

*app.js*

{% highlight javascript %}

var _multiply = require('multiply');
console.log((0, _multiply.multiply)(2, 3)); // => 2 * 3 = 6

{% endhighlight %}

You could notice that Babel added `require` statements in the resulting code.
Later, these require statements will be handled by Browserify.

*Please note, that using of Browserify is not the only option - you could use a number of tools like JSPM, System.js, Webpack, require.js etc. 
Moreover, Babel currently supports a lot of different module loaders - learn more [here](https://babeljs.io/docs/usage/modules/){:target="_blank"}.*

### Babelify

Browserify supports so-called *transforms*. These transforms will be executed for your code prior to running Browserify. 
It allows pre-processing of code before sending it to Browserify opening a lot of possibilities for developers.

There is a [big list](https://github.com/substack/node-browserify/wiki/list-of-transforms){:target="_blank"} of various Browserify transforms.
We are going to use [babelify](https://github.com/babel/babelify){:target="_blank"} in order to transform our ES6 code to ES5 version.
This ES5 version (with `require` statements) will be processed by Browserify later.

To sum up, our code transformation looks like following:

* We write code using ECMAScript6 capabilities including (but not limited to) ES6 module loading.
* **Babelify** translates this code to ES5-compatible code with `require` statements included.
* ES5 code with `require` statements is transformed to the version that is fully understandable by browsers by **Browserify**.
