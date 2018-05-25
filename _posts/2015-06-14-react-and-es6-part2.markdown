---
layout: post
title:  "React and ES6 - Part 2, React Classes and ES7 Property Initializers"
excerpt: "We will start with creating CartItem React component based on ES6 class. 
As a bonus, we'll see how we could use ES7 property initializers to beautify our code."
---

This is the second post of series in which we are going to explore the usage of React with ECMAScript6. 

You could find links to all parts of series below: 

* [React and ES6 - Part 1, Introduction into ES6 and React]({{ site.url }}/2015/05/22/react-and-es6-part1.html)
* **React and ES6 - React and ES6 - Part 2, React Classes and ES7 Property Initializers**
* [React and ES6 - Part 3, Binding to methods of React class (ES7 included)]({{ site.url }}/2015/08/16/react-and-es6-part3.html)
* [React and ES6 - Part 4, React Mixins when using ES6 and React]({{ site.url }}/2015/09/30/react-and-es6-part4.html)
* [React and ES6 - Part 5, React and ES6 Workflow with JSPM]({{ site.url }}/2015/10/11/react-and-es6-part5.html)
* [React and ES6 - Part 6, React and ES6 Workflow with Webpack]({{ site.url }}/2016/04/11/react-and-es6-part6.html)

> The code corresponding to this article is available on the
[GitHub](https://github.com/egor-smirnov/egorsmirnov.me-examples/tree/master/react-and-es6-part-2){:target="_blank"}.

> *Update from 14.06.2016:* Updated the code and text to use React 15 and Babel 6.

<img src="{{ site.url }}/images/posts/react.png" alt="React Logo" width="200" height="200">
<img src="{{ site.url }}/images/posts/js.png" alt="JavaScript Logo" width="200" height="200">

In the [first article]({{ site.url }}/2015/05/22/react-and-es6-part1.html), we started with an introduction into ES6 and 
creating static React component outputting "Hello from ES6".
Not so exciting :)

In this article, we are going to create a bit more sophisticated React component named `CartItem`.
It will output information about some product in the cart including image, title and price. 
Additionally, a user could interact with CartItem component by increasing or decreasing a number of items.
Our component will respond to this interaction by changing a total price.

This is what we'll see in the end:

<img src="{{ site.url }}/images/posts/2015-06-14/screenshot.png" alt="Screenshot" width="689" height="498">

## Creation of index.html file

Let's start with the creation of simple HTML file that will be our HTML template file.

{% highlight html %}

<!DOCTYPE html>
<html>
<head lang="en">
    <meta charset="UTF-8">
    <title>React and ES6 Part 2</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/foundation/5.5.2/css/foundation.min.css">
</head>
<body>
<div class="root"></div>
<script src="dist/bundle.js"></script>
</body>
</html>

{% endhighlight %}

Notice we've added [Foundation CSS](http://foundation.zurb.com/){:target="_blank"} framework served via CDN. It was done just for the prettier look of our micro application.
Also `div.root` will be the place where we will mount React application.

## Gulpfile.js

Just copy and paste [gulpfile.js from the previous part of series](https://github.com/egor-smirnov/egorsmirnov.me-examples/blob/master/reactjs-and-es6-part-1/gulpfile.js){:target="_blank"}. 
No changes to it by now. 

Same copy & pasting for [package.json](https://github.com/egor-smirnov/egorsmirnov.me-examples/blob/master/react-and-es6-part-2/package.json){:target="_blank"}. 

Next, run `npm install` (to install node dependencies) and then `gulp`. Gulp will continuously watch for your changes.


## Main application React Component

Create `app.jsx`:

{% highlight javascript linenos=table %}

import React from 'react';
import ReactDOM from 'react-dom';
import CartItem from './cartItem';

const order = {
    title: 'Fresh fruits package',
    image: 'http://images.all-free-download.com/images/graphiclarge/citrus_fruit_184416.jpg',
    initialQty: 3,
    price: 8
};

ReactDOM.render(
    <CartItem title={order.title} 
              image={order.image} 
              initialQty={order.initialQty}
              price={order.price}/>,
    document.querySelector('.root')
);

{% endhighlight %}

What's going on here:

* *Lines 1-2.* We import React / ReactDOM libraries.
* *Line 3.* Import `CartItem` React component that we'll create later.
* *Lines 5-10.* Sending props to `CartItem` component (props include item title, image, initial quantity and price).
* *Lines 12-18.* Mounting `CartItem` component to DOM element specified by its CSS class `.root`.

## CartItem React Component skeleton

Now it's time to create component responsible for displaying item's data and also for interaction with a user.

Add this code to file named `cartItem.jsx`:

{% highlight javascript linenos=table %}

import React from 'react';

export default class CartItem extends React.Component {
    constructor(props) {
        super(props);
        this.state = {
            qty: props.initialQty,
            total: 0
        };
    }
    componentWillMount() {
        this.recalculateTotal();
    }
    increaseQty() {
        this.setState({qty: this.state.qty + 1}, this.recalculateTotal);
    }
    decreaseQty() {
        let newQty = this.state.qty > 0 ? this.state.qty - 1 : 0;
        this.setState({qty: newQty}, this.recalculateTotal);
    }
    recalculateTotal() {
        this.setState({total: this.state.qty * this.props.price});
    }
}

{% endhighlight %}

Explanation:

* *Lines 4-10.* This is the constructor of newly added React class. The first thing to note here is call to `super(props)` which is required. 
Another thing - instead of using React `getInitialState()` lifecycle method, React team recommends to use instance property called `this.state`.
We initialize our state with props sent from `app.jsx` file. Personally, I like this improvement.
* *Lines 11-13.* Declaration of `componentWillMount()` lifecycle method (which is eventually just `CartItem` class method).
Inside this method, we do a recalculation of the total price.
To calculate total price we use `recalculateTotal()` which multiplies quantity (which is state of our component) by price (which is props).
* *Lines 14-20.* These are methods for increasing or decreasing the amount of items by a user.
These methods will be called when user clicks corresponding buttons in the view (refer to the screenshot of the application at the beginning of post).
Also notice that we are using second callback parameter of `this.setState()` method to recalculate the total price.

## CartItem render method

Add this new method inside `CartItem` class:

{% highlight javascript %}

export default class CartItem extends React.Component {

    // previous code we wrote here
    
    render() {
        return <article className="row large-4">
            <figure className="text-center">
                <p>
                    <img src={this.props.image}/>
                </p>
                <figcaption>
                    <h2>{this.props.title}</h2>
                </figcaption>
            </figure>
            <p className="large-4 column"><strong>Quantity: {this.state.qty}</strong></p>

            <p className="large-4 column">
                <button onClick={this.increaseQty.bind(this)} className="button success">+</button>
                <button onClick={this.decreaseQty.bind(this)} className="button alert">-</button>
            </p>

            <p className="large-4 column"><strong>Price per item:</strong> ${this.props.price}</p>

            <h3 className="large-12 column text-center">
                Total: ${this.state.total}
            </h3>

        </article>;
    }
}
{% endhighlight %}

Here we just output tags using `JSX` + component state and apply some Foundation CSS beauty.

Don't worry about `{this.increaseQty.bind(this)}` - I am going to cover this in the next part of series. 
For now just believe me that this line will call `increaseQty()` method of `CartItem` class.

So, now we have pretty simple application interacting with a user. 
Even this example is simple, it showed us how to write React components using ECMAScript6. Personally I like the new syntax that ES6 brings to us.

We are not done yet. Before finishing the article, I would like to take a look at a couple of additional ES6 features.

## Default Props and Prop Types for ES6 React classes

Imagine we want to add some validation and default values for `CartItem` component.

Luckily, this is built-in into React and called Default Props and Prop Types.
You could familiarize yourself with both of them [here](https://facebook.github.io/react/docs/reusable-components.html){:target="_blank"}.

Right after `CartItem` class add these lines:

{% highlight javascript linenos=table %}
CartItem.propTypes = {
    title: React.PropTypes.string.isRequired,
    price: React.PropTypes.number.isRequired,
    initialQty: React.PropTypes.number
};
CartItem.defaultProps = {
    title: 'Undefined Product',
    price: 100,
    initialQty: 0
};
{% endhighlight %}

As a result, if you now send numeric `title` prop inside `app.jsx` you'll have a warning in console.
This is the power of React props validation.

## Bringing ES7 into the project

You could ask a very reasonable question - why do you have ES7 in heading above if even ES6 is not yet finalized?

I will answer that let's look into the future. And start using non-breaking features like property initializers or decorators.

Even if ES7 is still in the very early stage, there is number of proposed features that are already implemented in Babel. 
And these experimental features are transpiled to valid ES5 code from ES7. Awesome!

First, install the missing npm module by running `npm install --save-dev babel-preset-stage-0`.

Next, in order to bring that additional syntax sugar to our project, we'll need to make the very small change on line 8 of `gulpfile.js`.
Substitute this line with the code below:

{% highlight javascript %}
     .transform('babelify', {presets: ['react', 'es2015', 'stage-0']})
{% endhighlight %}

You could grab final `gulpfile.js` from [GitHub repository](https://github.com/egor-smirnov/egorsmirnov.me-examples/blob/master/react-and-es6-part-2/gulpfile.js){:target="_blank"}


## ES7 Property Initialiazers for Default Props and Prop Types of React component

Inside `CartItem` class add this right above constructor:

{% highlight javascript %}

export default class CartItem extends React.Component {
    static propTypes = {
        title: React.PropTypes.string.isRequired,
        price: React.PropTypes.number.isRequired,
        initialQty: React.PropTypes.number
    };    
    static defaultProps = {
        title: 'Undefined Product',
        price: 100,
        initialQty: 0
    };
    
    constructor() {
        ...
    }
    
    // .. all other code
}
{% endhighlight %}

This is doing the same thing as code after the class definition, just in a bit more neatly way (in my opinion). 
You could also delete code related to propTypes / defaultProps that goes after `CartItem` class definition as it is not needed anymore.

## ES7 Property Initialiazers for initial state of React component

The finishing step will be transferring initial state from the constructor to property initializer.

Add this right before constructor of `CartItem` class:

{% highlight javascript %}
export default class CartItem extends React.Component {
    // .. some code here
    state = {
        qty: this.props.initialQty,
        total: 0
    };
    // .. constructor starts here
{% endhighlight %}

You could also delete code related to initializing of state from the constructor.

Done. You could check out final `cartItem.jsx` for this part on the series on the 
[GitHub](https://github.com/egor-smirnov/egorsmirnov.me-examples/blob/master/react-and-es6-part-2/cartItem.jsx){:target="_blank"}.

## Conclusion

In this part, we familiarized ourselves with writing React components using ES6 classes and introduced ES7 property initializers. 

Stay tuned for the next parts of React + ES6 series!

## Further Reading

- [Props vs state in React](https://github.com/uberVU/react-guide/blob/master/props-vs-state.md){:target="_blank"}
- [About Prop Validation and more, official React documentation](https://facebook.github.io/react/docs/reusable-components.html){:target="_blank"}
- [About experimental features in BabelJS](https://babeljs.io/docs/usage/experimental/){:target="_blank"}
- [ES7 Class Properties Proposal](https://gist.github.com/jeffmo/054df782c05639da2adb){:target="_blank"}
