---
layout: post
title:  "Upgrading to Babel 6. Babel 6 for Babelify and WebPack."
excerpt: "How could you migrate your Babelify / Webpack projects to Babel 6."
---

<img src="{{ site.url }}/images/posts/js.png" alt="JavaScript Logo" width="200" height="200">

> The code corresponding to Gulp + Browserify + Babelify + Babel 6 setup is available on the
[GitHub](https://github.com/egor-smirnov/egorsmirnov.me-examples/tree/master/babel-5-to-6-babelify){:target="_blank"}.

> The code corresponding to Webpack + Babel 6 setup is available on the
[GitHub](https://github.com/egor-smirnov/egorsmirnov.me-examples/tree/master/babel-5-to-6-webpack){:target="_blank"}.

## Babel 6

Few days ago [Babel 6 was released](http://babeljs.io/blog/2015/10/29/6.0.0/){:target="_blank"}. 
Babel 6 helps you to transpile your next-generation JavaScript (ES2015+) code to ES5 Javascript.

> If you are not familiar with Babel, there is short info about Babel
[in my previous article]({{ site.url }}/2015/05/22/react-and-es6-part1.html) about React and ES2015.

Major changes include improved performance, modularization, the introduction of the system of plugins and presets. 
You could read more about that new things using [this link](http://babeljs.io/blog/2015/10/29/6.0.0/){:target="_blank"}.

The introduction of plugins / presets results in some additional work with you existing project. But nothing crazy.

In this short article, I am going to provide examples of migration in cases of usage of Babelify and Webpack.

## Gulp + Browserify + Babelify + Babel 6

> More info about Browserify and Babelify is available 
[in my previous article]({{ site.url }}/2015/05/25/browserify-babelify-and-es6.html).

1. Install latest versions of needed packages `npm install --save-dev babelify babel-preset-es2015 babel-preset-react gulp vinyl-source-stream`.
Compared to Babel 5 new packages here are `babel-preset-es2015` and `babel-preset-react`. If you are not using React, installing `babel-preset-react` is not necessary.

2. Your `gulpfile.js` should look like this:

{% highlight javascript %}

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
{% endhighlight %}

The part that is changing from Babel 5 here is a call of `babelify`. 
Notice how we've used `presets` parameter by passing `es2015` and `react` there.

We are basically done. In the case of using Browserify / Babelify all that you need to do is to add additional npm packages and provide `presets` additional parameter.

> The code corresponding to Gulp + Browserify + Babelify + Babel 6 setup is available on the
[GitHub](https://github.com/egor-smirnov/egorsmirnov.me-examples/tree/master/babel-5-to-6-babelify){:target="_blank"}.


## Webpack + Babel 6

1. Run `npm install babel-loader babel-core babel-preset-es2015 babel-preset-react webpack webpack-dev-server --save-dev.`
Same as with Babelify we've added various Babel presets and additionally webpack itself.

2. It doesn't make sense to go through whole webpack config here. You could grab the config from
[this repository](https://github.com/egor-smirnov/egorsmirnov.me-examples/tree/master/babel-5-to-6-webpack){:target="_blank"}.
The interesting part for us is following:

{% highlight javascript %}
module: {
        loaders: [
            {
                test: /\.js$/,
                loader: 'babel',
                exclude: /node_modules/,
                query: {
                    cacheDirectory: true,
                    presets: ['es2015', 'react']
                }
            }
        ]
    }
{% endhighlight %}

Notice how we've used `presets` key here. Very similar to the case of Browserify / Babelify.

So, when you are upgrading your webpack project to Babel 6 don't forget to install necessary Babel presets / plugins and add these presets / plugins to webpack config.

> The code corresponding to Webpack + Babel 6 setup is available on the
[GitHub](https://github.com/egor-smirnov/egorsmirnov.me-examples/tree/master/babel-5-to-6-webpack){:target="_blank"}.

## Babel 6 + ???

In case you are using something else (not Browserify nor Webpack) I basically recommend:

1. Going to [this page](http://babeljs.io/docs/setup/){:target="_blank"}.
2. Selecting tool you are using.
3. Going to tool's Github repository and checking the information about Babel 6 (if presented).

## Conclusion

As Babel 6 was introduced quite recently, not every tool is upgraded at the moment. Upgrading will take some time.
But the most popular tools are already ready to be used with Babel 6.

There are also some bugs, but they are fixed very fast. So I expect that in some weeks we'll have quite a stable ecosystem around Babel 6.

## Further Reading

- [Babel 6.0.0 Released](http://babeljs.io/blog/2015/10/29/6.0.0/){:target="_blank"}