---
layout: post
title:  "Welcome to Jekyll!"
date:   2015-07-11 11:34:20
categories: jekyll
author: Google Developers Group Managua
image: http://i1.wp.com/wptavern.com/wp-content/uploads/2014/12/jekyll.png
---
## 选择的书

现在实验室举办了机器学习的沙龙，选择的是Bishop的 _Pattern recognition and machine learning_ 一书

[pic]: /images/PRML_cover.jpg

# Introduction

### 数据处理原则

> test data must be pre-processed using the same steps as the training data

对test data的预处理，必须使用training data 预处理的参数，使用相同的模型(例如归一化，PCA)，不能重新建立模型。

##Supervised learning

书中对监督学习的定义：

>Applications in which the training data comprises examples of the input vectors along with their corresponding target vectors as know as _supervised learning_

训练集中包含有 _输入向量_ 和 _目标向量(target vector)_ 的应用为监督学习

监督学习又分为 _分类(Classification)_ 和 _回归(Regression)_ 两种问题

分类的定义为

> Aim is to assign each input vector to one of a finite number of discrete categories

任务目标是将输入向量分类为一组有限的离散类别,为 _分类(Classification)_ 

回归的定义为

>Desired output consists of one or more continuouts variabels

目标输出是一个或多个连续的变量,则是 _回归(Regression)_

## Unsupervised Learning

还有一种模式识别问题:

> In other `parttern recognition problems`, the training data consists of a set of input vectors X without any corresponding target values.

训练集只包含输入向量X,没有相应的目标向量,则此问题为无监督学习.

聚类

>The goal is such _unsupervised learning_ problems may be to discover groups of similar examples within the data.




You’ll find this post in your `_posts` directory. Go ahead and edit it and re-build the site to see your changes. You can rebuild the site in many different ways, but the most common way is to run `jekyll serve`, which launches a web server and auto-regenerates your site when a file is updated.

To add new posts, simply add a file in the `_posts` directory that follows the convention `YYYY-MM-DD-name-of-post.ext` and includes the necessary front matter. Take a look at the source for this post to get an idea about how it works.

Jekyll also offers powerful support for code snippets:

{% highlight ruby %}
def print_hi(name)
  puts "Hi, #{name}"
end
print_hi('Tom')
#=> prints 'Hi, Tom' to STDOUT.
{% endhighlight %}

Check out the [Jekyll docs][jekyll] for more info on how to get the most out of Jekyll. File all bugs/feature requests at [Jekyll’s GitHub repo][jekyll-gh]. If you have questions, you can ask them on [Jekyll’s dedicated Help repository][jekyll-help].

[jekyll]:      http://jekyllrb.com
[jekyll-gh]:   https://github.com/jekyll/jekyll
[jekyll-help]: https://github.com/jekyll/jekyll-help
