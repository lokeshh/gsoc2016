---
layout: post
title: "Shortcut Symbols [Week 9-10]"
date: 2016-07-31 03:29:37 +0000
comments: true
categories: 
---


With the work done in Week 9 and 10, Statsample-GLM now supports shortcut symbols in Formula Language.

With this addition, the regression has become more [R/Patsy like]() and more convenient.

## Symbols Added

There are two shortcut symbols now being supported:

- `*`
- `/`

`a*b` is shortcut for `a+b+a:b`. This is commonly used within regression models.

`a/b` is shortcut for `a+a:b`. Its quite useful while dealing with nested categorical variables. `a/b` makes sense when `b` is nested inside `a`.

## Brackets

This week brackets support has been added so one can form expression involving use of brackets. For example `(a+b):c` would evaluate to `a:c + b:c`.

It supports any level of sophistication with symbols and brackets. For example `(a+b)*(c+d)` would give `a+b+c+d+a:c+a:d+b:c+b:d`.

## Note

Although there are certain limitations to the current formula language:

1. Since more than 2-way interactions are not supported yet, formula like `a*b*c` wouldn't work.
2. There's not a mechanism to deal with cases such as `a*a`.

## Formula Language in Statsample

Earlier, the plan was to implement the formula language also in Statsample but because Statsample which supports just linear regression is also supported by name Normal Regression in Statsample-GLM, we are planning to not implement formula language in Statsample but rather remove the linear regression support from Statsample if it doesn't offer any advantage to Normal Regression in Statsample-GLM. For info, see [here](https://github.com/SciRuby/statsample/issues/53).

## Example using shortcut symbols

```ruby
> df = Daru::DataFrame.from_csv 'spec/data/df.csv'
=> #<Daru::DataFrame(14x6)>
             y      a      b      c      d      e
      0      0      6   62.1     no female      A
      1      1     18   34.7    yes   male      B
      2      1      6   29.7     no female      C
      3      0      4     71     no   male      C
      4      1      5   36.9    yes   male      B
      5      0     11   58.7     no female      B
      6      0      8   63.3     no   male      B
      7      1     21   20.4    yes   male      A
      8      1      2   20.5    yes   male      C
      9      0     11   59.2     no   male      B
     10      0      1   76.4    yes female      A
     11      0      8   71.7     no female      B
     12      1      2   77.5     no   male      C
     13      1      3   31.1     no   male      B
> df.to_category 'c', 'd', 'e'
> train = df.first 10
> test = df.last 4
> reg = Statsample::GLM::Regression.new 'a~b*c', train, :normal, algorithm: :mle
> reg.model.coefficients :hash
{:c_yes=>5.678447231711081,
 :b=>0.0007560417597709064,
 :"c_yes:b"=>-0.06481888635745593,
 :constant=>7.6233202721217825}
# Now lets obtain predictions from this model
> reg.model test
<Daru::Vector(4)>
0 8.407366176569727
1 7.677528466297357
2 7.681913508504028
3 7.646833170850658
```

## Internal Structure of Formula Language

There are three classes by which formula language works:

- `FormulaWrapper`
- `Formula`
- `Token`


When creation of a new model is invoked by `Statsample::GLM::Regression#new`, `FormulaWrapper` is first called.

`FormulaWrapper` class does the necessary preprocessing. It does mainly two things:

- Apply the shortcut symbols and reduce the expression to only containing `:` and `+`
- After reducing to simple expression containing only `:` and `+`, it groups terms based on the numerical terms they are interacting with.

After `FormulaWrapper` has form groups, it processes each of these groups using the `Formula` class.

The `Formula` class takes each group and form tokens which do not overlap, that is if they are converted to dataframe they won't contain redundancy in that dataframe.

The `Token` class stores the column names and can expand these columns when fed a dataframe.

Sounds confusing?

Lets try an example:

Lets say our expression is `x*a + b*c`, where `x` is numerical vector and `a`, `b` and `c` are categorical.

1. First it will converted to simple expression by `FormulaWrapper`. It will be simplified as `1+x+a+x:a+b+c+b:c`. Notice shortcut symbols have disappeared and only `+` and `:` are remaining.
2. Now `1+x+a+x:a+b+c+b:c` is grouped into two groups [`1`, `a`, `b`, `c`, `b:c`] and [`1`, `a`]. The first group has the common numerical interaction terms as `1`, while the second group has common numerical interaction terms as `x`.
3. Now both the groups will be processed by `Formula` to produce dataframe with full rank.
4. First group will be parsed to `1+a(-)+b(-)+c(-)+b(-):c(-)` by `Formula` class. `a(-)` implies that vector `a` is contrast coded to reduced rank, while `a` implies its coded to full rank.
5. Second group will be parsed to `x + x:a(-)`.
6. In the end these terms are combined and resultant parsed expression is the sum of the above two expressions, i.e. `1+a(-)+b(-)+c(-)+b(-):c(-)+x+x:a(-)`.
7. Then are expanded into dataframes by `Token` class and these dataframes are concatted to form the final dataframe for the given expression.

## Conclusion

We saw the overview of how formula language works inside `Statsample::GLM` and shortcut symbols with brackets has made the usage much more convenient and powerful.