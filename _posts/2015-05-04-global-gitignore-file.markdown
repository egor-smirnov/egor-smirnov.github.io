---
layout: post
title:  "Global .gitignore file"
date:   2015-05-04 00:00:00
excerpt: "Blog post explaining what for you might need global .gitignore file. 
Guides you through creation of this file for Mac OS X."
---
<img src="{{ site.url }}/images/posts/2015-05-04/git.png" alt="Git Logo">

Do you remember how much times during you developer career you forgot to add some IDE files to **.gitignore**?
There is a feature that could help you to get rid of creating .gitignore with common excludes for every new project.
And this feature is called global .gitignore file.

## Adding global .gitignore file on Mac

In order to start using it, go through these steps:

1. Open Terminal.
2. Run `touch ~/.gitignore_global`  - this will create global .gitignore file in your home directory.
3. Add some values that you would like to always ignore. For example, you could use 
[this file](https://gist.github.com/octocat/9257657){:target="_blank"}.
4. Run `git config --global core.excludesfile ~/.gitignore_global`. According to 
[this page at git-scm.com](http://git-scm.com/docs/gitignore/1.7.12){:target="_blank"}
this command will make all the patterns from `~/.gitignore_global` **ignored in all situations**.

Done. Now no need to worry about creating common ignore sections for every project your start.