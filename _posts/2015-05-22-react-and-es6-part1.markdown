---
layout: post
title:  "React and ES6 - Part 1, Introduction"
date:   2015-05-22 00:00:00
excerpt: "Introduction into what is ECMAScript6, how to start using it in browsers. 
Describes steps to setup development workflow to build React components in ES6 with help of Gulp and Babel."
---

This is the first post of series in which we are going to explore the usage of React with ECMAScript6.

You could find links to all parts of series below:

* React and ES6 - Part 1, Introduction into ES6 and React
* [React and ES6 - Part 2, React Classes and ES7 Property Initializers]({{ site.url }}/2015/06/14/react-and-es6-part2.html)
* [React and ES6 - Part 3, Binding to methods of React class (ES7 included)]({{ site.url }}/2015/08/16/react-and-es6-part3.html)
* [React and ES6 - Part 4, React Mixins when using ES6 and React]({{ site.url }}/2015/09/30/react-and-es6-part4.html)
* [React and ES6 - Part 5, React and ES6 Workflow with JSPM]({{ site.url }}/2015/10/11/react-and-es6-part5.html)
* [React and ES6 - Part 6, React and ES6 Workflow with Webpack]({{ site.url }}/2016/04/11/react-and-es6-part6.html)

> The code corresponding to this article is available on the
[GitHub](https://github.com/egor-smirnov/egorsmirnov.me-examples/tree/master/reactjs-and-es6-part-1){:target="_blank"}.

> *Update from 03.11.2015:* Updated the code and text to use Babel 6 instead of Babel 5.

> *Update from 20.05.2016:* Updated the code and text to use React 15.

<img src="{{ site.url }}/images/posts/react.png" alt="React Logo" width="200" height="200">
<img src="{{ site.url }}/images/posts/js.png" alt="JavaScript Logo" width="200" height="200">

Since announce of [ReactJS v0.13.0 Beta 1](https://facebook.github.io/react/blog/2015/01/27/react-v0.13.0-beta-1.html){:target="_blank"} 
it's possible to use capabilities of ECMAScript 6 for your **React** components. What advantages it brings to us as developers?
 
Well, **ECMAScript6** (or ECMAScript 2015) is new standard 
(<s>that will hopefully be finalized in 2015,</s>
 **update from 26.06.2015** - it is now officially standard - [link](https://esdiscuss.org/topic/ecmascript-2015-is-now-an-ecma-standard){:target="_blank"}!)
 that brings whole bunch of new features into the world of JavaScript.
Such a features include classes, arrow functions, rest parameters, iterators, generators ... and a lot, lot more.

If you are not familiar with new features of ECMAScript 2015, I would highly recommend you to go through them using 
[this link](https://babeljs.io/docs/learn-es6/){:target="_blank"}.

By looking at [ES6 compatibility table](http://kangax.github.io/compat-table/es6/){:target="_blank"} 
we could notice that not all the browsers support every single feature of ES2015. Luckily, you don't have to spend ages until vendors will ship ECMAScript 6 features to the browsers.
There are already existing solutions called **transpilers** that convert your code written in ES6 to ES5-compatible code.
That's very similar to how CoffeeScript turns your Coffee code into JavaScript.

One of these solutions is [Babel](https://babeljs.io/){:target="_blank"}, really amazing tool. Many thanks to its authors.
What's good, Babel supports a huge amount of different frameworks, build systems, test frameworks, template engines - 
[look here](https://babeljs.io/docs/using-babel/){:target="_blank"}.

To give you a quick idea of how **babel** works here is an example.
Say, we have following code written in ECMAScript 6:

{% highlight javascript %}
var evenNumbers = numbers.filter((num) => num % 2 === 0);
{% endhighlight %}

After running babel for that code you will have:

{% highlight javascript %}
var evenNumbers = numbers.filter(function (num) {
  return num % 2 === 0;
});
{% endhighlight %}

A similar concept applies to other ES6 language constructs.

## Preparing development environment

In order to have a continuous workflow with babel, we will use [Gulp](http://gulpjs.com/){:target="_blank"}. 
This is task runner built on top of node.js which could improve your life by automating tedious tasks. 
If you heard about Grunt then Gulp has the same purpose.

1. Obviously, you will need node.js. Install it on your system if you don't have it.
2. Next, you will need to install **Gulp** globally: `npm install --g gulp`.
3. Switch to your project's directory. Initialize your `package.json` file by using `npm init` and answering appeared questions.
4. Run `npm install --save react react-dom`. This will install `react` npm module into your `node_modules` folder and save React library as dependency inside your package.json file.
5. Run `npm install --save-dev gulp browserify babelify vinyl-source-stream babel-preset-es2015 babel-preset-react`. This will install more **development** dependencies to your `node_modules`.

*To learn more about different modules installed at step 5, please refer to [my article about Browserify, Babelify and ES6]({{base}}/2015/05/25/browserify-babelify-and-es6.html).*

## Creating gulpfile.js

Create file named `gulpfile.js` in the root directory of your project with following content:

{% highlight javascript linenos=table %}
var gulp = require('gulp');
var browserify = require('browserify');
var babelify = require('babelify');
var source = require('vinyl-source-stream');

gulp.task('build', function () {
    return browserify({entries: './app.jsx', extensions: ['.jsx'], debug: true})
        .transform('babelify', {presets: ['es2015', 'react']})
        .bundle()
        .pipe(source('bundle.js'))
        .pipe(gulp.dest('dist'));
});

gulp.task('watch', ['build'], function () {
    gulp.watch('*.jsx', ['build']);
});

gulp.task('default', ['watch']);
{% endhighlight %}

I guess, some explanations would be helpful.

* *Lines 1-4.* We require installed node.js modules and assign them to variables.
* *Line 6.* We define gulp task named `build` that could be run by typing `gulp build`.
* *Line 7.* We start to describe what our `build` task will do. We tell Gulp to use Browserify for `app.jsx`. Additionally, we turn on debug mode which is beneficial for development.
* *Lines 8-11.* We apply Babelify transform to our code. This allows us to convert code written in ECMAScript6 to ECMAScript5. Next we output the result to `dist/bundle.js` file. 
*Update from 03.11.2015:* These lines now use new feature of Babel 6 called **presets**.
* *Lines 14-16.* We define gulp task named `watch` that could be run by typing `gulp watch`. This task will run `build` task whenever any of `jsx` file changes.
* *Line 18*. We define default gulp task that could be run by typing `gulp`. This task simply executes `watch` task. 

Your typical workflow will include typing `gulp` in a terminal and pressing Enter key.
It will watch for changes inside your React components and re-build everything continuously.

## JSX and Babel

You might already notice that we are using `.jsx` extension instead of `.js`.
JSX is special JavaScript syntax extension developed by ReactJS team. 
This format is delivered especially for the more convenient development of ReactJS components.

Here is [more information](https://facebook.github.io/react/docs/jsx-in-depth.html){:target="_blank"} about JSX.

Another useful thing - Babelify understands JSX syntax out of the box (more about this in  
[blog post](http://babeljs.io/blog/2015/02/23/babel-loves-react/){:target="_blank"}).

## Writing first React component using ECMAScript 6

Hope you are not too bored by this time :)

It's time to create our first very simple component using ES6. Add file named `hello-world.jsx` to root of the project:

{% highlight javascript linenos=table %}
import React from 'react';

class HelloWorld extends React.Component {
    render() {
        return <h1>Hello from {this.props.phrase}!</h1>;
    }
}

export default HelloWorld;
{% endhighlight %}

Some explanation:

* *Line 1.* We import React library and put it to a variable named `React`.
* *Lines 3-8.* Creation of React component using ES6 class by extending `React.Component` class. We add very simple `render` method which returns `<h1>` tag containing `phrase` prop.
* *Line 9.* We export just created a component to outside world using `export default HelloWorld`.

For simpler understanding, I placed the code for the same component, but written without usage of ES6 classes:

{% highlight javascript linenos=table %}
import React from 'react';

var HelloWorld = React.createClass({
    render: function() {
        return (
            <h1>Hello from {this.props.phrase}!</h1>
        );
    }
});

export default HelloWorld;
{% endhighlight %}

## Wrapping up

Let's finish our simple example.

Create file named `app.jsx`:

{% highlight javascript linenos=table %}
import HelloWorld from './hello-world';
import React from 'react';
import ReactDOM from 'react-dom';

ReactDOM.render(
    <HelloWorld phrase="ES6"/>,
    document.querySelector('.root')
);
{% endhighlight %}

Here we import `HelloWorld` component created on the previous step and set `phrase` prop of that component.
Please also note that we use special `react-dom` package for rendering of the `HelloWorld` component. 
That's because core React package is separated from rendering package starting from React version 0.14.

Next, let's create `index.html`:

{% highlight html linenos=table %}

<!DOCTYPE html>
<html>
<head lang="en">
    <meta charset="UTF-8">
    <title>ReactJS and ES6</title>
</head>
<body>
<div class="root"></div>
<script src="dist/bundle.js"></script>
</body>
</html>
{% endhighlight %}

Now run `gulp` from a terminal (it will create `dist/bundle.js` file) and open this HTML file in your browser.

You should see below image.

<img src="{{ site.url }}/images/posts/2015-05-22/screenshot.png" alt="Screenshot" width="411" height="176">

## Further Reading

- [Classes in ECMAScript 6](http://www.2ality.com/2015/02/es6-classes-final.html){:target="_blank"}
- [Exploring ES6, e-book](https://leanpub.com/exploring-es6/){:target="_blank"}
- [BabelJS](https://babeljs.io/){:target="_blank"}
- [An Introduction to Gulp.js](http://www.sitepoint.com/introduction-gulp-js/){:target="_blank"}
- [JSX in Depth](https://facebook.github.io/react/docs/jsx-in-depth.html){:target="_blank"}
