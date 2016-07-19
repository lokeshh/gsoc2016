---
layout: post
title: "Formula language implementation [Week 7-8]"
date: 2016-07-19 18:26:41 +0000
comments: true
categories: 
---

After the end of 6 weeks we have category data support in Daru. Now in the coming weeks we will be adding support for category data in Statsample and Statsample-GLM.

Currently, Statsample and Statsample-GLM do not support regression with category data.

With the introduction of formula language I am looking to accomplish the following:

- To support regression with category data
- To provide convenience of formula language to create regression models

In these two weeks I have implemented a formula language but it is limited in certain ways. The work of following weeks will fill this gap.

Lets talk about the formula language I have implemented in these two weeks.

## Formula Language

The formula language which I aim to implement is similar to that used within [R and Patsy](https://patsy.readthedocs.io/en/stable/formulas.html#how-formulas-work)

With the work of these two weeks, the formula language has the following features:

- It supports 2-way interaction.
- It supports `:` and `+`.
- It supports inclusion/exclusion of contant or intercept term.

And since I have followed the Patsy way of implementing the formula langauge it has an edge over R. Since, Patsy has a more accurate algorithm for deciding whether to use a full- or reduced-rank coding scheme for categorical factors, the same is inherited in Statsample and Statsample-GLM.

R sometimes can give under-specified model but this is not the case with our implementation. One example is expansion of `0 + a:x + a:b`, where `x` is numeric. More information about this can be found [here](https://patsy.readthedocs.io/en/stable/R-comparison.html).

I am thankful to Patsy for it made my work very easy by providing all the details in their [documentation](https://patsy.readthedocs.io/en/stable/formulas.html#how-formulas-work). Without it I would have fallen into many pitfalls.

Now lets see formula language in action in Statsample and Statsample-GLM.

## Regression in Statsample-GLM

Regression in Statsample-GLM has become an easy task and in addition it now supports category data as predictor variables.

Lets see this by an example.

Lets assume a dataframe `df` with numeric columns `a`, `b`, and having category column `c`, `d`, `e`.

Lets create a logistic model with predictors `a`, `a*b`, `c` and `c:d`.

If we were to do this earlier, we would have done the following.

Since we can't code category variables, so lets leave `c` and `c:d`.

```ruby
> train['a:b'] = train['a'] * train['b']
> train = train['a', 'a:b', 'y']
> mod = Statsample::GLM.compute train, 'y', :logistic, constant: 1
> # Now lets obtain predictions
> test['a:b'] = test['a'] * test['b']
> test = test['a', 'a:b']
> mod.predict test
=> #<Daru::Vector(3)>
      0 0.9999
      1 0.0123
      2 0.5925
```

Now with the introduction of formula langauge it has become a very easy task with no work required to preprocess the dataframe.

```ruby
> reg = Statsample::GLM::Regression.new 'y~a+a:b+c+c:d', train, :logistic
> reg.predict test
=> #<Daru::Vector(3)>
      0 0.2999
      1 0.1523
      2 0.8925
```

The above code not only enables predictions with caetgory data but also reflects the powerful formula langauge.

Lets have a look at Statsample now.

## Statsample

With Statsample, its the same. Now one can perform multiple regression with formula language and category variables as predictors.

```ruby
> reg = Statsample::FitModel.new 'y~a+a:b+c+c:d', train
> mod = reg.model
```

This will give a multiple linear regression model.

## Conclusion

The introduction of formula language and ability to handle category data has given a great boost to Data Analysis in Ruby and I really hope we keep improving it further and further.

In the coming weeks I will look forward to implement the following:

- Add more than 2-way interaction support
- Support for shortcut symbols '*', '/', etc.