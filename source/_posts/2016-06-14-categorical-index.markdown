---
layout: post
title: "Categorical Index [Week 1-2]"
date: 2016-06-14 07:54:47 +0000
comments: true
categories: 
---

Daru just got a new capability => Categorical Index.

Now one can organize vector and dataframe using index that is categorical.

Daru has got now 4 types of indexes to index data:

- Daru::Index
- Daru::MultiIndex
- Daru::DateTimeIndex
- Daru::CategoricalIndex (new)

`Daru::Index` is for usual index where every value is unique.

`Daru::MultiIndex` is for indexing with more than one level.

`Daru::DateTimeIndex` is to have indexing with dates. Its powerful means to analyze time series data.

The new `Daru::CategoricalIndex` is helpful with data indexed with sparsely populated index with each unique index value as category.

Please visit [this link](http://nbviewer.jupyter.org/github/SciRuby/sciruby-notebooks/blob/master/Data%20Analysis/Categorical%20Data/Indexing%20in%20Vector.ipynb) before to get a basic understanding of how indexing works in `Daru::Vector` and [this link](http://nbviewer.jupyter.org/github/SciRuby/sciruby-notebooks/blob/master/Data%20Analysis/Categorical%20Data/Indexing%20in%20DataFrame.ipynb) for `Daru::DataFrame`.

## Example

Let's see an example.

(Alternatively you can also see this example in iRuby notebook [here](http://nbviewer.jupyter.org/github/SciRuby/sciruby-notebooks/blob/master/Data%20Analysis/Categorical%20Data/examples/%5BExample%5D%20Categorical%20Index.ipynb))

```ruby
require 'daru'

require 'open-uri'
content = open('https://d37djvu3ytnwxt.cloudfront.net/asset-v1:MITx+15.071x_3+1T2016+type@asset+block/WHO.csv')
df = Daru::DataFrame.from_csv content

df[0..5]

  => #<Daru::DataFrame(194x6)>
                 Country     Region Population    Under15     Over60 FertilityR
            0 Afghanista Eastern Me      29825      47.42       3.82        5.4
            1    Albania     Europe       3162      21.33      14.93       1.75
            2    Algeria     Africa      38482      27.42       7.17       2.83
            3    Andorra     Europe         78       15.2      22.86        nil
            4     Angola     Africa      20821      47.58       3.84        6.1
            5 Antigua an   Americas         89      25.96      12.35       2.12
            6  Argentina   Americas      41087      24.42      14.97        2.2
            7    Armenia     Europe       2969      20.34      14.06       1.74
            8  Australia Western Pa      23050      18.95      19.46       1.89
            9    Austria     Europe       8464      14.51      23.52       1.44
           10 Azerbaijan     Europe       9309      22.25       8.24       1.96
           11    Bahamas   Americas        372      21.62      11.24        1.9
           12    Bahrain Eastern Me       1318      20.16       3.38       2.12
           13 Bangladesh South-East     155000      30.57       6.89       2.24
           14   Barbados   Americas        283      18.99      15.78       1.84
          ...        ...        ...        ...        ...        ...        ...
```

The data is about countries. The `region` column describes the region that country belongs to. A region can have more than one country.

This a ideal place where we can use Categorical Index if we want to study about different regions.

```ruby
> df.index = Daru::CategoricalIndex.new (df['Region']).to_a
  
  #<Daru::CategoricalIndex(194): {Eastern Mediterranean, Europe, Africa, Europe, Africa, Americas, Americas, Europe, Western Pacific, Europe, Europe, Americas, Eastern Mediterranean, South-East Asia, Americas, Europe, Europe, Americas, Africa, South-East Asia ... Africa}>
```

Let's see all regions there are:

```ruby
> df.index.categories
  ["Eastern Mediterranean", "Europe", "Africa", "Americas", "Western Pacific", "South-East Asia"]
```

Let's find out how many countries lie in Africa region.

```ruby
> df.row['Africa'].size
  46
```

Finding out the mean life expectancy of europe is as easy as

```ruby
> df.row['Europe']['LifeExpectancy'].mean
  76.73584905660377
```

Let's see the maximum life expectancy  of South-East Asia

```ruby
> df.row['South-East Asia']['LifeExpectancy'].min
  63
```

## Internal architecture


To efficiently store the index and make retrieval possible in constant time, `Daru::CategoricalIndex` uses two data structres-

- Hash-table: To map each category to positional values. It is represented as `@cat_hash`.
- Array: To map each position to a integer which represent a category.

For example:

```ruby
idx = Daru::CategoricalIndex.new [:a, :b, :a, :b, :c]
```

For `idx`, the hash table and array woul be:

```ruby
@cat_hash = {a: [0, 2], b: [1, 3], c: [4]}

@array = [0, 1, 0, 1, 2]
```

The hash table helps us in retriving all instances which belong to that category in real time.

Similary, the array helps us in retriving category of an instance in constant time.

And the reason to store integers in the array instead of name of categories itself is to avoid unnecessary usage of space.

## When and why to use Categorical Index

If you have a categorical variable or data where there are more than one instance of same object and you want to index the dataframe by that column.

It will save you a lot of space and make access to the same category fast.

Also if you want your dataframe to be indexed by a column in which not every entry is unique categorical index will come to the rescue.


