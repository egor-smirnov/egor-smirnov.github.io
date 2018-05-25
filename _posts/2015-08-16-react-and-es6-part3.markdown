---
layout: post
title:  "React and ES6 - Part 3, Binding to methods of React class (ES7 included)"
excerpt: "When using ES6 classes with React, class methods are now not automatically binded to the class instance. We'll discover various ways to overcome this."
---

This is the third post of series in which we are going to explore the usage of React with ECMAScript6 and ECMAScript7. 

You could find links to all parts of series below: 

* [React and ES6 - Part 1, Introduction into ES6 and React]({{ site.url }}/2015/05/22/react-and-es6-part1.html)
* [React and ES6 - Part 2, React Classes and ES7 Property Initializers]({{ site.url }}/2015/06/14/react-and-es6-part2.html)
* **React and ES6 - Part 3, Binding to methods of React class (ES7 included)**
* [React and ES6 - Part 4, React Mixins when using ES6 and React]({{ site.url }}/2015/09/30/react-and-es6-part4.html)
* [React and ES6 - Part 5, React and ES6 Workflow with JSPM]({{ site.url }}/2015/10/11/react-and-es6-part5.html)
* [React and ES6 - Part 6, React and ES6 Workflow with Webpack]({{ site.url }}/2016/04/11/react-and-es6-part6.html)

> The code corresponding to this article is available at
[GitHub](https://github.com/egor-smirnov/egorsmirnov.me-examples/tree/master/react-and-es6-part-3){:target="_blank"}.

> *Update from 18.06.2016:* Updated the code and text to use React 15 and Babel 6.

<img src="{{ site.url }}/images/posts/react.png" alt="React Logo" width="200" height="200">
<img src="{{ site.url }}/images/posts/js.png" alt="JavaScript Logo" width="200" height="200">

If you look at paragraph "CartItem render method" of the [previous article in the series]({{ site.url }}/2015/06/14/react-and-es6-part2.html#cartitem-render-method){:target="_blank"} 
you might be confused by the usage of `{this.increaseQty.bind(this)}`.

If we try the same example with just `{this.increaseQty}` we'll see `Uncaught TypeError: Cannot read property 'setState' of undefined` in browser console:

<img src="{{ site.url }}/images/posts/2015-08-16/console.png" alt="Browser console with error" width="670" height="95">

This is because when we call a function in that way binding to `this` is not a class itself, it's `undefined`. 
It's default JavaScript behavior and is quite expected. 
In opposite to this, in case you use `React.createClass()` all the methods are autobinded to the instance of an object.
Which might be counter-intuitive for some developers.

No autobinding was the decision of React team when they implemented support of ES6 classes for React components.
You could find out more about reasons for doing so in [this blog post](http://facebook.github.io/react/blog/2015/01/27/react-v0.13.0-beta-1.html#autobinding){:target="_blank"}.

Let's now see various ways of how to call class methods from your JSX in case you use ES6 classes.
All the different methods we consider here are available at [this GitHub repository](https://github.com/egor-smirnov/egorsmirnov.me-examples/tree/master/react-and-es6-part-3){:target="_blank"}.

## Method 1. Using of Function.prototype.bind().

We've already seen this:

{% highlight javascript %}
export default class CartItem extends React.Component {
    render() {
        <button onClick={this.increaseQty.bind(this)} className="button success">+</button>
    }
}
{% endhighlight %}

As any method of ES6 class is plain JavaScript function it inherits `bind()` from Function prototype. 
So now when we call `increaseQty()` inside JSX, `this` will point to our class instance. 
You could read more about Function.prototype.bind() in this [MDN article](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/bind){:target="_blank"}.

## Method 2. Using function defined in constructor.

This method is a mix of the previous one with usage of class constructor function:

{% highlight javascript %}
export default class CartItem extends React.Component {
    
    constructor(props) {
        super(props);
        this.increaseQty = this.increaseQty.bind(this);
    }

    render() {
        <button onClick={this.increaseQty} className="button success">+</button>
    }
}
{% endhighlight %}

You don't have to use `bind()` inside your JSX anymore, but this comes with the cost of increasing the size of constructor code.

## Method 3. Using fat arrow function and constructor.

[ES6 fat arrow functions](https://babeljs.io/docs/learn-es2015/#arrows){:target="_blank"} preserve `this` context when they are called.
We could use this feature and redefine `increaseQty()` inside constructor in the following way:

{% highlight javascript %}
export default class CartItem extends React.Component {
    
    constructor(props) {
        super(props);
        this._increaseQty = () => this.increaseQty();
    }

    render() {
        <button onClick={_this.increaseQty} className="button success">+</button>
    }
}
{% endhighlight %}

## Method 4. Using fat arrow function and ES2015+ class properties.

Additionally, you could use fat arrow function in combination with experimental ES2015+ class properties syntax:

{% highlight javascript %}
export default class CartItem extends React.Component {
      
    increaseQty = () => this.increaseQty();

    render() {
        <button onClick={this.increaseQty} className="button success">+</button>
    }
}
{% endhighlight %}

So, instead of defining our class method in a constructor as in method number 3, we use property initializer.

**Warning:** Class properties are not yet part of current JavaScript standard. 
But your are free to use them in Babel using corresponding experimental flag (stage 0 in our case).
Your could read more about how to do this in [Babel documentation](https://babeljs.io/docs/usage/experimental/){:target="_blank"}.

We've already switched to stage 0 in [React and ES6 - Part 2, React Classes and ES7 Property Initializers]({{ site.url }}/2015/06/14/react-and-es6-part2.html#bringing-es7-into-the-project){:target="_blank"}, 
so this is not an issue for our example.

## Method 5. Using ES2015+ function bind syntax.

Quite recently Babel introduced syntactic sugar for `Function.prototype.bind()` with the usage of `::`.
I won't go into details of how it works here. Other guys already have done the pretty good explanation. 
You could refer to [this official Babel blog post](http://babeljs.io/blog/2015/05/14/function-bind/){:target="_blank"} for more details.

Below is code with usage of ES2015+ function bind syntax:

{% highlight javascript %}
export default class CartItem extends React.Component {
    
    constructor(props) {
        super(props);
        this.increaseQty = ::this.increaseQty;
        // line above is an equivalent to this.increaseQty = this.increaseQty.bind(this);
    }

    render() {
        <button onClick={this.increaseQty} className="button success">+</button>
    }
}
{% endhighlight %}

Again, this feature is highly experimental. Use it at your own risk.

## Method 6. Using ES2015+ Function Bind Syntax in-place.

You also have a possibility to use ES2015+ function bind syntax directly in your JSX without touching constructor.
It will look like:

{% highlight javascript %}
export default class CartItem extends React.Component {
    render() {
        <button onClick={::this.increaseQty} className="button success">+</button>
    }
}
{% endhighlight %}

Very concise, the only drawback is that this function will be re-created on each subsequent render of the component. 
This is not optimal. What is more important that will cause problems if you use something like PureRenderMixin (or its equivalent for ES2015 classes).

## Conclusion

In this article, we've discovered various possibilities of binding class methods of React components. I've prepared test project based on code from part 2 of this series.
It's available [here](https://github.com/egor-smirnov/egorsmirnov.me-examples/tree/master/react-and-es6-part-3){:target="_blank"}.

Next time we'll see what is the state of React mixins for ES2015 classes.

## Further Reading

- [About autobinding in official React blog](http://facebook.github.io/react/blog/2015/01/27/react-v0.13.0-beta-1.html#autobinding){:target="_blank"}
- [Autobinding, React and ES6 Classes](http://www.ian-thomas.net/autobinding-react-and-es6-classes/){:target="_blank"}
- [Function Bind Syntax in official Babel blog](http://babeljs.io/blog/2015/05/14/function-bind){:target="_blank"}
- [Function.prototype.bind()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/bind){:target="_blank"}
- [Experimental ES7 Class Properties](https://gist.github.com/jeffmo/054df782c05639da2adb){:target="_blank"}
- [Experimental ES7 Function Bind Syntax](https://github.com/zenparsing/es-function-bind){:target="_blank"}