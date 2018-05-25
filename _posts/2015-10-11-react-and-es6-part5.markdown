---
layout: post
title:  "React and ES6 - Part 5, React and ES6 Workflow with JSPM"
excerpt: "Walk through JSPM and React"
---

This is the 5th post of series in which we are going to explore the usage of React with JSPM. 

You could find links to all parts of series below: 

* [React and ES6 - Part 1, Introduction into ES6 and React]({{ site.url }}/2015/05/22/react-and-es6-part1.html)
* [React and ES6 - Part 2, React Classes and ES7 Property Initializers]({{ site.url }}/2015/06/14/react-and-es6-part2.html)
* [React and ES6 - Part 3, Binding to methods of React class (ES7 included)]({{ site.url }}/2015/08/16/react-and-es6-part3.html)
* [React and ES6 - Part 4, React Mixins when using ES6 and React]({{ site.url }}/2015/09/30/react-and-es6-part4.html)
* **React and ES6 - Part 5, React and ES6 Workflow with JSPM**
* [React and ES6 - Part 6, React and ES6 Workflow with Webpack]({{ site.url }}/2016/04/11/react-and-es6-part6.html)

> The code corresponding to this article is available at
[GitHub](https://github.com/egor-smirnov/egorsmirnov.me-examples/tree/master/react-and-es6-part-5){:target="_blank"}.

> *Update from 18.06.2016:* Fixed the JSPM version because of the problem found by Mahdi (refer to a comments below the article). Thanks!

<img src="{{ site.url }}/images/posts/react.png" alt="React Logo" width="200" height="200">
<img src="{{ site.url }}/images/posts/js.png" alt="JavaScript Logo" width="200" height="200">

## What is JSPM?

Nowadays to start a new frontend project we as developers have to do too many actions before writing any line of code.
You have to think what JavaScript module loader system you are going to use, prepare gulp / grunt / npm / whatever script to
prepare assets for production, you have to select CSS pre-processing tool, you have to think about sourcemaps integration and a lot of other things.

What if we don't want to mess with all that and want to start coding in 15 minutes?
We just want to have a tool which will allow this.

One of such a tools is [JSPM](http://jspm.io/){:target="_blank"} (JavaScript Package Manager). 

- **JSPM** allows you to load JavaScript modules in different formats (ES6, CommonJS, AMD and global ).
- It allows you to install dependencies from different registries - npm or github.
- JSPM has ES6+ support out of the box.
- You are  able to load CSS / images / fonts / other formats of files without additional hassle. 
That is possible with a help of **plugins** (described later in this article).
- JSPM makes it easy to prepare your files for production use.

In this post, we will re-create project from previous posts, but using JSPM.

I would also recommend you to go through [JSPM wiki](https://github.com/jspm/jspm-cli/wiki){:target="_blank"}.

## Initial setup our JSPM + React project

First of all run this command:

`install -g jspm@0.16.12`

**update from 18.06.2016** we have to fixate the version because of some further problems with newer version of JSPM.
Please refer to Mahdi in a comments below the article.

This will install JSPM as a global npm module. After that, it would be possible to use `jspm` command from any place in your system.

Next, go to your project directory and run `jspm init` and provide default answers for all prompts. 
The output in your terminal should look like this: 

{% highlight bash %}
Package.json file does not exist, create it? [yes]:
Would you like jspm to prefix the jspm package.json properties under jspm? [yes]:
Enter server baseURL (public folder path) [./]:
Enter jspm packages folder [./jspm_packages]:
Enter config file path [./config.js]:
Configuration file config.js doesn't exist, create it? [yes]:
Enter client baseURL (public folder URL) [/]:
Do you wish to use a transpiler? [yes]:
Which ES6 transpiler would you like to use, Babel, TypeScript or Traceur? [babel]:
{% endhighlight %}

You will have to wait while JSPM is doing its own magic. 
It will download all the initial dependencies needed (like babel etc.) and place them to the folder named `jspm_packages` 
(I advise you to add this folder in .gitignore).

`jspm_packages` folder is used by JSPM for storing and managing packages like jQuery / React / AngularJS etc. which you eventually would use inside your project.
Looks like already familiar `node_modules` and `bower_components` , isn't it? 

But there is a big difference in the case of `jspm_packages` folder. It is used by JSPM to store dependencies from multiple sources (from Github / npm / bower).
What's even nicer - inside your code you are able to treat all the dependencies as identical not dependable whether it's coming from npm or Github repository.

## Install needed dependencies

Next thing that will be produced after running `jspm init` is a file named `config.js`.

First of all, `config.js` provides configuration for versions of dependencies we install. 
Additionally, you are able to rename your packages to the name you like the most.

Let's understand what I mean by all that. Run this command:

{% highlight bash %}
jspm install npm:jspm-loader-jsx
{% endhighlight %}

If you'll take a look at `config.js` now you should see excerpt like below:

{% highlight json %}
map: {
    ...
    "jspm-loader-jsx": "npm:jspm-loader-jsx@0.0.7"
    ...
}
{% endhighlight %}

Now it's possible to use this module inside the code by its name `jspm-loader-jsx`. 
Let's say we don't like this name and would like to reference it as `jsx` inside our code.

Run following commands:

{% highlight bash %}
jspm uninstall jspm-loader-jsx
jspm install jsx=npm:jspm-loader-jsx
{% endhighlight %}

Our plugin will be now referenced as `jsx` and you could include it into your code by `import jsx` 
(though we don't need to do that as this package is JSPM plugin). 
By the way, the package we've just installed will be needed for our project (JSPM doesn't support loading of JSX files by default). 
So don't delete `jspm-loader-jsx` package :)

Let's install other packages we are going to use:

{% highlight bash %}
jspm install react react-dom
{% endhighlight %}


We haven't used `npm:` or `github:` prefix here because JSPM has the registry of commonly used packages.
It's located [here](https://github.com/jspm/registry){:target="_blank"} and you could always add a package to the list by opening a new PR.

If you want to dive deeper I recommend reading 
[this JSPM wiki article](https://github.com/jspm/jspm-cli/blob/master/docs/installing-packages.md){:target="_blank"}.

## Tweaking config.js

Inside `config.js` find key named `babelOptions` and replace it with following lines: 

{% highlight json %}
babelOptions: {
    "stage": 0,
    "optional": [
      "runtime",
      "optimisation.modules.system"
    ]
  }
{% endhighlight %}

This is needed to make Babel (which is one of default JSPM packages installed into `jspm_packages`) support all the experimental features like class properties.

## Finishing project structure

Now it's time to code something. 
I won't copy their source code as it's very similar to what we've written in previous parts of series.

Create a file named `app.jsx` with content copied from [this location](https://github.com/egor-smirnov/egorsmirnov.me-examples/blob/master/react-and-es6-part-5/app.jsx){:target="_blank"}.
Next, create file `cartItem.jsx` and copy it from [here](https://github.com/egor-smirnov/egorsmirnov.me-examples/blob/master/react-and-es6-part-5/cartItem.jsx){:target="_blank"}.
I hope that you are already familiar with contents of that files. It should be like so if you've read previous posts from this series.

Grab the [contents of `index.html`](https://github.com/egor-smirnov/egorsmirnov.me-examples/blob/master/react-and-es6-part-5/index.html){:target="_blank"}. 
Following is the interesting part for us:

{% highlight html %}
<script src="jspm_packages/system.js"></script>
<script src="config.js"></script>
<script>
    System.import('app.jsx!');
</script>
{% endhighlight %}

Dynamic module loading in JSPM is made possible via library called [SystemJS](https://github.com/systemjs/systemjs){:target="_blank"}.
It's developed by [Guy Bedford](https://github.com/guybedford){:target="_blank"} who is an author of both JSPM and SystemJS.

On the first line, we include SystemJS, on the second one we include `config.js` that is created by JSPM.
By writing `System.import('app.jsx!');` (*) we load entry point script `app.jsx` which then loads everything else.

We are basically done. Now open `index.html` in your browser. You should see image like below:

<img src="{{ site.url }}/images/posts/2015-09-30/screenshot.png" alt="Screenshot" width="605" height="528">

(*) Notice the `!` sign in `app.jsx!`. This is a convention used in JSPM for loading non-JavaScript files.

## Additional features of JSPM

What we've covered in this article is not all the features of JSPM.

Below are notable features I would like to highlight:

## Universal JavaScript module loading via JSPM

**JSPM** supports different formats of modules. 
So it's absolutely legal to load some require.js module along with ES6 class in the same file.

Read [more info about module loaders](https://github.com/systemjs/systemjs/blob/master/docs/module-formats.md){:target="_blank"} available in JSPM.

## Loading of text files, CSS, and other assets via JSPM

It's possible to load not only JavaScript files, but also other formats. That is made possible via **JSPM plugins**.

For example, run this command:

{% highlight bash %}
jspm install css
{% endhighlight %}

After that it'll be possible to use this piece of code directly inside JavaScript:

{% highlight javascript %}
import './some/style.css!';
{% endhighlight %}

Read [more info about JSPM plugins](https://github.com/jspm/jspm-cli/blob/master/docs/plugins.md){:target="_blank"}.

## Building for production

You could concatenate & minify your source code with JSPM without using additional tools.
 
Run the following command from application directory:

{% highlight javascript %}
jspm bundle-sfx app.jsx! app.js --skip-source-maps --minify
{% endhighlight %}

And you will have single minified file with all modules / dependencies included.

Read [more info about JSPM production workflow](https://github.com/jspm/jspm-cli/blob/master/docs/production-workflows.md){:target="_blank"}.


## Further Reading

- [Official JSPM website](http://jspm.io/){:target="_blank"}
- [JSPM wiki](https://github.com/jspm/jspm-cli/wiki){:target="_blank"}
- [SystemJS wiki](https://github.com/systemjs/systemjs){:target="_blank"}
- [SystemJS Module Loaders](https://github.com/systemjs/systemjs/blob/master/docs/module-formats.md){:target="_blank"}
- [JSPM plugins](https://github.com/jspm/jspm-cli/blob/master/docs/plugins.md){:target="_blank"}
- [JSPM production workflow](https://github.com/jspm/jspm-cli/blob/master/docs/production-workflows.md){:target="_blank"}
