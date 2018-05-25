---
layout: post
title:  "React and ES6 - Part 4, React Mixins when using ES6 and React"
excerpt: "React mixins are no longer supported when using React components written in ES6. But there are ways to overcome this issue."
---

This is the 4th post of series in which we are going to explore the usage of React with ECMAScript6 and ECMAScript7. 

You could find links to all parts of series below: 

* [React and ES6 - Part 1, Introduction into ES6 and React]({{ site.url }}/2015/05/22/react-and-es6-part1.html)
* [React and ES6 - Part 2, React Classes and ES7 Property Initializers]({{ site.url }}/2015/06/14/react-and-es6-part2.html)
* [React and ES6 - Part 3, Binding to methods of React class (ES7 included)]({{ site.url }}/2015/08/16/react-and-es6-part3.html)
* **React and ES6 - Part 4, React Mixins when using ES6 and React**
* [React and ES6 - Part 5, React and ES6 Workflow with JSPM]({{ site.url }}/2015/10/11/react-and-es6-part5.html)
* [React and ES6 - Part 6, React and ES6 Workflow with Webpack]({{ site.url }}/2016/04/11/react-and-es6-part6.html)

> Code corresponding to this article is available at
[GitHub](https://github.com/egor-smirnov/egorsmirnov.me-examples/tree/master/react-and-es6-part-4){:target="_blank"}.

> *Update from 18.06.2016:* Updated the code and text to use React 15 and Babel 6.

<img src="{{ site.url }}/images/posts/react.png" alt="React Logo" width="200" height="200">
<img src="{{ site.url }}/images/posts/js.png" alt="JavaScript Logo" width="200" height="200">

When using `React.createClass()` you have a possibility to use so-called mixins.
They allow you to mix some additional functionality into your React components. 
This concept is not unique for React, it also exists in vanilla JS and other languages / frameworks.

It's no longer possible to use React mixin mechanism for components written in ES6.
I won't dive into the reasons of such a decision (a lot around this topic was written before me). 
If you are interested, you could check links below for more information around this:

- [About mixins in ES6 in official React blog](https://facebook.github.io/react/blog/2015/01/27/react-v0.13.0-beta-1.html#mixins){:target="_blank"}
- [Mixins Are Dead. Long Live Composition by Dan Abramov](https://medium.com/@dan_abramov/mixins-are-dead-long-live-higher-order-components-94a0d2f9e750){:target="_blank"}

Instead, we are going to concentrate more on concrete examples.

## Higher-Order Components instead of Mixins

We will work with `CartItem` component from part 2 of this series of articles. 
You could grab its code from [here](https://github.com/egor-smirnov/egorsmirnov.me-examples/blob/master/react-and-es6-part-2/cartItem.jsx){:target="_blank"}.

Let's say that along with other controls we would like to show timer with increasing number each second. 

To better illustrate the approach, we won't change the code of `CartItem`. 
Instead, we are going to provide some component that will wrap original `CartItem` and "enhance" `CartItem` with additional functionality.
Such a component is called Higher-Order Component.

This might sound cryptic, but it should become clearer while moving along.

P.S. By "enhancing" I mean simply wrapping component in some function.

Let's imagine we have `IntervalEnhance` component:

{% highlight javascript %}
import React from 'react';
import { IntervalEnhance } from "./intervalEnhance";

class CartItem extends React.Component {
    // component code here
}

export default IntervalEnhance(CartItem);
{% endhighlight %}

Now it's time to write this `intervalEnhance` component.
We'll add new file called `intervalEnhance.jsx`:

{% highlight javascript  linenos=table %}
import React from 'react';

export var IntervalEnhance = ComposedComponent => class extends React.Component {

    static displayName = 'ComponentEnhancedWithIntervalHOC';

    constructor(props) {
        super(props);
        this.state = {
            seconds: 0
        };
    }

    componentDidMount() {
        this.interval = setInterval(this.tick.bind(this), 1000);
    }

    componentWillUnmount() {
        clearInterval(this.interval);
    }

    tick() {
        this.setState({
            seconds: this.state.seconds + 1000
        });
    }

    render() {
        return <ComposedComponent {...this.props} {...this.state} />;
    }
};

{% endhighlight %}

Let's take a closer look.

* *Line 3.* Part `ComposedComponent => class extends React.Component` - this is the same as defining the function that returns class. 
`ComposedComponent` is component we want to "enhance" (in the code above it is `CartItem`).
By using `export var IntervalEnhance` we are able to export the whole function as `IntervalEnhance` (refer to `CartItem` code above).
* *Line 5.* Helpful for debugging. The component will be named as `ComponentEnhancedWithIntervalHOC` in React DevTools.
* *Lines 7-12.* We initialize the state of a component with `seconds` equal to zero.
* *Lines 14-26.* Providing lifecycle hooks that will start and stop interval for this component.
* *Line 29.* Most interesting thing here. This line will bring all state / props of our "enhancer" component and transfer to `CartItem` component.
By doing this `CartItem` will get access to `this.state.seconds` property.

Last our step is to change `render` method of `CartItem` component. We'll output `this.state.seconds` directly to the view:

{% highlight javascript %}
import React from 'react';
import { IntervalEnhance } from "./intervalEnhance";

class CartItem extends React.Component {
    // component code here
    
    render() {
        return <article className="row large-4">
                <!-- some other tags here -->                    
                <p className="large-12 column">
                    <strong>Time elapsed for interval: </strong>
                    {this.props.seconds} ms
                </p>    
            </article>;
        }
}
export default IntervalEnhance(CartItem);
{% endhighlight %}

Open the page in a browser and you will see label ticking each second:

<img src="{{ site.url }}/images/posts/2015-09-30/screenshot.png" alt="Screenshot" width="605" height="528">

Notice - all this is done without touching `CartItem` component (apart from `render` method)!
This is why Higher-Order Components are so powerful.

Now, please take a look at the [final code](https://github.com/egor-smirnov/egorsmirnov.me-examples/tree/master/react-and-es6-part-4){:target="_blank"}.
If something is unclear I am glad to see your feedback in comments.

## Use ES7 Decorators instead of Mixins

If you like ES7 Decorators more then it's possible to use them in a similar manner.

First, install `babel-plugin-transform-decorators-legacy`:

{% highlight javascript %}
npm install --save-dev babel-plugin-transform-decorators-legacy
{% endhighlight %}

And grab modified [gulpfile.js](https://github.com/egor-smirnov/egorsmirnov.me-examples/blob/master/react-and-es6-part-4/gulpfile.js){:target="_blank"} from the GitHub repository.

Then:
 
{% highlight javascript %}
import React from 'react';
import { IntervalEnhance } from "./intervalEnhance";

@IntervalEnhance
export default class CartItem extends React.Component {
    // component code here
}
{% endhighlight %}

## What about PureRenderMixin?

If you use mixins like `PureRenderMixin` then there are different approaches bringing such a functionality to React components written in ES6.
One of them is following (all credits go to @aputinski at this [gist](https://gist.github.com/ryanflorence/a93fd88d93cbf42d4d24){:target="_blank"}):

{% highlight javascript %}
class Foo extends React.Component {
   constructor(props) {
     super(props);
     this.shouldComponentUpdate = React.addons.PureRenderMixin.shouldComponentUpdate.bind(this);
   }
   render () {
     return <div>Helllo</div>
   }
 }
{% endhighlight %}

## Conclusion

Higher-Order Components are powerful and expressive. 
Currently, they are widely used and are a replacement for old mixin syntax. 
Feel free to create your own mechanism for dealing with mixing functionality between components.

One of the examples of its usage is [Relay](https://facebook.github.io/relay/){:target="_blank"} framework. 
Relay is complete React-based framework released by Facebook. 
Each component your write could be wrapped into Relay Container for automatic retrieval of data dependencies (and many other things).
And this is truly convenient and seems like a magic from the first look.
 
Next two articles would be about a bit different topic - integrating of our project with JSPM or Webpack. 

This should bring not only ES6 support but a bunch of other helpful utilities. Stay tuned!

## Further Reading

- [About mixins in ES6 in official React blog](https://facebook.github.io/react/blog/2015/01/27/react-v0.13.0-beta-1.html#mixins){:target="_blank"}
- [Mixins Are Dead. Long Live Composition by Dan Abramov](https://medium.com/@dan_abramov/mixins-are-dead-long-live-higher-order-components-94a0d2f9e750){:target="_blank"}
- [Original gist by Sebastian Markbåge showing usage of composition for React components](https://gist.github.com/sebmarkbage/ef0bf1f338a7182b6775){:target="_blank"}
- [JSX Spread Attributes](https://facebook.github.io/react/docs/jsx-spread.html){:target="_blank"}
- [Gist with PureRenderMixin in ES6](https://gist.github.com/ryanflorence/a93fd88d93cbf42d4d24){:target="_blank"}