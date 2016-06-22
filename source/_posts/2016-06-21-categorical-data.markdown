---
layout: post
title: "Categorical Data [Week 3-4]"
date: 2016-06-21 20:19:08 +0000
comments: true
categories:
published: false
---

Daru has now the capability to store and process categorical data.

Daru has now three types of vector
- :object
- :numeric
- :category (new)

With introduction of categorical data, Daru has now two benefits
1. Storage of categorical data is very effective.
2. Tasks related to categorical data have become a lot easier

The reason for 1 is that it in ordinary vector the data is stored as an array.
It doesn't consider the fact that most of the entries are same. Look at (Internal Structure) for more information.

Lets discuss the various tasks which can now be done easily related to categorical vector.

(The purpose of this blog is to give an overview of what tasks you can accomplish with categorical data. To learn about what each method I highly looking at [this notebook](http://nbviewer.jupyter.org/github/lokeshh/cat_notebook/blob/master/Categorical%20Data.ipynb))

As soon as one declares a categorical variable, one can look at frequency count of each category to get judgement of the data:

```ruby
> rank = Daru::Vector.new ['III']*10 + ['II']*5 + ['I']*5, type: :category, categories: ['I', 'II', 'III']
> rank.frequencies
=> #<Daru::Vector(3)>
   I   5
  II   5
 III  10
```
One can look over the summary of the data to get to know common numbers about categorical data like how many categories are present, which is the most frequenct category, etc.
```ruby
> rank.summary
=> #<Daru::Vector(6)>
         size           20
   categories            3
     max_freq           10
 max_category          III
     min_freq            5
 min_category            I
```
Its possible to convert a numerical variable into categorical variable. For example you have measures of height and you want to categorize them:
```ruby
> heights = Daru::Vector.new [30, 35, 32, 50, 42, 51]
> heights.cut [30, 40, 50, 60]
> height_cat = heights.cut [30, 40, 50, 60]                            
> height_cat.frequencies
=> #<Daru::Vector(3)>
 30-39     3
 40-49     1
 50-59     2
```
Given a dataframe its possible to extract rows based on the categories. It uses the same Area-like query syntax like an ordinary vector. For example
```ruby
> df = Daru::DataFrame.new({
  id: [0, 1, 2, 3, 4, 5, 6, 7],    
  grade: %w[A C B A C C B B],
  name: ['a', 'b', 'c', 'd', 'e', 'f', 'g', 'h']
})    

> df[:grade] = df[:grade].to_category ordered: true, categories: %w[A B C]

> df.where df[:grade].lt('C')
=> #<Daru::DataFrame(5x3)>
       grade    id  name
     0     A     0     a
     2     B     2     c
     3     A     3     d
     6     B     6     g
     7     B     7     h
```

The benefit as you can see is that were able to order the categories and based on that queried the dataframe.

By defining the custom order of categories and setting `ordered` to `true`, one can sort the categories, find the min, max, etc. For example

```ruby
# Assuming df defined as above
# Lets rename the categories to show you that lexical order is not followed while sorting with categorical data
> df[:grade].rename_categories 'A' => 'Good', 'B' => 'Average', 'C' => 'Bad'
> df
=> #<Daru::DataFrame(8x3)>
           grade      id    name
       0    Good       0       a
       1     Bad       1       b
       2 Average       2       c
       3    Good       3       d
       4     Bad       4       e
       5     Bad       5       f
       6 Average       6       g
       7 Average       7       h
> df.sort! [:grade]
=> #<Daru::DataFrame(8x3)>
           grade      id    name
       0    Good       0       a
       3    Good       3       d
       2 Average       2       c
       6 Average       6       g
       7 Average       7       h
       1     Bad       1       b
       4     Bad       4       e
       5     Bad       5       f
```

## Internal Structure

Its similar to the internal structure of categorical index.

To efficiently store the duplicates of catgories and make retrieval possible in constant time, categorical data in Daru uses two data structres-
- Hash-table: To map each category to positional values. It is represented as `@cat_hash`.
- Array: To map each position to a integer which represent a category.

For example:

```ruby
dv = Daru::Vector.new [:a, :b, :a, :b, :C], type: :category
```

For `dv`, the hash table and array would be:

```ruby
@cat_hash = {a: [0, 2], b: [1, 3], c: [4]}

@array = [0, 1, 0, 1, 2]
```

(Of course, we would need to have a variable to map `0` to `:a`, `1` to `:b` and `2` to `:c`.)

The hash table helps us in retriving all instances which belong to that category in real time.

Similary, the array helps us in retriving category of an instance in constant time.

And the reason to store integers in the array instead of name of categories itself is to avoid unnecessary usage of space.


