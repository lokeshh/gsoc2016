---
layout: post
title: "GSoC Summary"
date: 2016-08-18 17:08:46 +0000
comments: true
categories: 
---

## Summary

During this GSoC I implemented support for categorical data in Daru and regression in Statsample-GLM with formula language.


## Pull Requests
The following are the main pull requests regarding my project:

- [Daru#134](https://github.com/v0dro/daru/pull/134) In this I implemented the following:
    - `CategoricalIndex` class for handling categorical index
    - `Category` module to add categorical data support in `Daru::Vector` and `Daru::DataFrame`
    - Visualization support for categorical data
- [Statsample-GLM#31](https://github.com/SciRuby/statsample-glm/pull/31) In this I implemented the following:
    - Formula language support
    - Categorical data support in regression
- [Statsample#51](https://github.com/SciRuby/statsample/pull/51) It implements formula language and categorical data support for regression in Statsample. This is unmerged, reason being that we are not sure whether we should remove the linear regression support from Stastsample or not. See [here](https://github.com/SciRuby/statsample/issues/53). We will either end up merging this pull request or moving the linear regression form here to Statsample-GLM.
- [Daru#208](https://github.com/v0dro/daru/pull/208/) It does the following:
    - Implements missing value support for categorical data
    - Improves the missing values API to make it simple and improve performance of Daru update operations

[Here](https://github.com/v0dro/daru/pulls?utf8=%E2%9C%93&q=is%3Apr%20author%3Alokeshh) are other pull requests not necessary related to the project.

Now I will talk in detail about the work in these pull requests:

### Daru#134

This was my major work during the weeks from 1 to 6th week.

You can find every detail of my work like what exactly I implemented, why I made certain decisions and how to use it in the following posts:

- [Categorical Index](http://lokeshh.github.io/blog/2016/06/14/categorical-index/)
- [Categorical Data](http://lokeshh.github.io/blog/2016/06/21/categorical-data/)
- [Visualization](http://lokeshh.github.io/blog/2016/07/02/visualization/)

This PR has been merged.

### Statsample-GLM#31

The following posts discusses in detail my work:

- [Formula Language Implementation and Categorical Data Support in Regression](http://lokeshh.github.io/blog/2016/07/19/formula-language-week7-8/)
- [Shorcut Symbols](http://lokeshh.github.io/blog/2016/07/31/shortcut-symbols/)

This PR is about to get merged. Just waiting for the new Daru to be released.

### Statsample#51

This pull request is currently unmerged. It implements the same functionality as the above pull request does for Statsample-GLM.

Earlier our plan was to implement support for categorical data in both Statsample and Stastsample-GLM but because linear regression is also present in Statsample-GLM. And since linear regression in Statsample is better in terms of performance as compared to Statsample-GLM we are looking to remove the linear regression from Statsample and move it to Statsample-GLM. More information is [here](https://github.com/SciRuby/statsample/issues/53).

So, we will doing one of these two things:

- Merge this pull request and do not remove linear regression from Statsample.
- Or move linear regression from Statsample to Statsample-GLM.

### [Daru#208](https://github.com/v0dro/daru/pull/208/)

This improves the current structure of missing values API in Daru and introduces missing values support for categorical data. More information can found [here](http://lokeshh.github.io/blog/2016/08/18/improve-missing-values-api-in-daru/).

![The End](http://www.donnymiller.com/fineart/universe/THATSALLFOLKS.jpg)