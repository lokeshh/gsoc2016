---
layout: post
title: "Visualization [Week 5-6]"
date: 2016-07-02 15:48:03 +0000
comments: true
categories: 
---

During these two weeks I added visualization of categorical data in addition to support of a new plotting library [Gruff](https://github.com/topfunky/gruff)

Daru supports visualization via three libraries:

- [Nyaplot](https://github.com/SciRuby/nyaplot)
- [GnuplotRB](https://github.com/SciRuby/gnuplotrb/)
- [Gruff](https://github.com/topfunky/gruff) (new)

Lets discuss them one by one

## Nyaplot

Nyaplot is the default plotting library for Daru. Nyaplot allows creation of a variety of plots with Daru easily. Its biggest strength lies in its ablity to draw interactive plots.

Now Daru also supports categorical data visualization using Nyaplot. It mainly has two aspects:

- In case of a category vector it allows to view the frequencies of categories in a bar graph.
- And in case of dataframe containing a category vector, it allows to have scatter and line plots categorized by a category vector visualized by different shape, size and color.

[Here](http://nbviewer.jupyter.org/github/lokeshh/cat_notebook/blob/master/Visualization.ipynb) are some examples of visualization of category data using Nyaplot in Daru.

## GnuplotRB

GnuplotRB is another great library which has inbuilt support for Daru datastructres: `Daru::Vector` and `Daru::DataFrame`. Though it doesn't directly operate on vectors and dataframes but uses its own API.

GnuplotRB strength lies in its offering of highly customized plots with yet a very simple to use API.

No work was done regarding supporting categorical data visualization in GnuplotRB because it supports it out of the box owing to its easy to API that lets plot multiple plots with a variety of features.

[Here's](http://nbviewer.jupyter.org/github/lokeshh/cat_notebook/blob/master/Gnuplotrb.ipynb) a notebook demonstrating examples in the manner category data can be visualizaed using GnuplotRB.

## Gruff

Gruff is a new plotting library that has just been added to Daru. [Gruff](https://github.com/topfunky/gruff) offers remarkably beautiful plots with very less effort. It also offers pie and sidebar plots which currently the other two libraries don't offer.

These two notebooks show examples related to plotting of `Daru::Vector` and `Daru::DataFrame`:

- [Plotting of Vector](http://nbviewer.jupyter.org/github/lokeshh/cat_notebook/blob/master/Gruff%20Vector.ipynb)
- [Plotting of DataFrame](http://nbviewer.jupyter.org/github/lokeshh/cat_notebook/blob/master/Gruff%20DataFrame.ipynb)

## Choose from different libraries

To easily move between these all these libraries, Daru has following functions:

- `Daru.plotting_library`
- `Daru::Vector#plotting_library`
- `Daru::DataFrame#plotting_library`

`Daru.plotting_library` can be used to set the current plotting library. For example, using `Daru.plotting_library = :gruff` one can switch the plotting library to Gruff. This means all the plots created here after will be using Gruff for plotting.

Inorder to change plotting library for only a specific vector, one can use `Daru::Vector#plotting_library`. For example, `dv.plotting_library = :gruff` will only change plotting library for vector `dv` and all other vector created will created using the library as set by `Daru.plotting_library`.

The same goes for dataframes, one can use `df.plotting_library = :gruff` to set plotting library for data frame `df` to Gruff.

## Summary

Along with the support of categorical data, Daru now also owns the ability to visualize catgory data. I realized and addressed a few shortcoming of some of these libraries and we at SciRuby are motivated to overcome those shortcoming and make visualization in Daru more complete.