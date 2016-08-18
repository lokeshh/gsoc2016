---
layout: post
title: "Improve missing values API in Daru [Week 11-12]"
date: 2016-08-18 09:22:15 +0000
comments: true
categories: 
---

The end of GSoC is near. I ended up finishing up a bit early on the formula language implementation and decided to devote the time on some other important issues.

During these last two weeks I solved some issues in Daru and mainly worked on [this issue](https://github.com/v0dro/daru/issues/137) regarding how missing values are handled in Daru.

The following were the shortcomings:

- Update operations like `#[]=`, `#set_at` were slow.
- Any value could be set as missing values, which made the checks for missing values somewhat hard.


Now, Daru follows a simple approach of only considering `nil` and `Float::NAN` as the missing values. Although one loses the flexibility of assigning an arbitrary value as missing but it has greatly simplified many things and also improvement in performance is significant. Further, one can simply uses `#replace` now to change the values which he/she wants to treat as missing to `nil`.

In addition to that, the updates have become blazingly fast without compromising the caching of missing values. I accomplished by the following strategy:

- During the updates the cache which stores the positions of `nil` and `Float::NAN` gets outdated and doesn't get updated until we require those positions.
- When missing positions of either `nil` or `Float::NAN` are required by any of the missing value method, those are returned if cache isn't outdated and if the cache is outdated then its rebuilt.

This way one has best of both worlds. The updates remain fast and also the caching of `nil` and `Float::NAN` is maintained.

I ran the following benchmarks:

```ruby
require 'benchmark'

n = 100000
dv = Daru::Vector.new 1..n

Benchmark.bm do |x|
  x.report do
    100.times { dv[0] = nil }
  end
  
  x.report do
    10.times do
      dv[0] = nil
      # Need to be replaced with only_valid when running for before
      10.times { dv.reject_values nil }
    end
  end
end
```

And these are the results before and after:

```ruby

# Before
       user     system      total        real
   1.840000   0.000000   1.840000 (  1.840055)
  15.080000   0.060000  15.140000 ( 15.462978)

# After
       user     system      total        real
   0.000000   0.000000   0.000000 (  0.000308)
  11.120000   0.160000  11.280000 ( 11.385459)
  
```

Here's the summary of the old and new API regarding handling of missing values:

Methods added in Daru::Vector (and category):

- reject_values
- include_values?
- indexes
- count_values
- replace values

Methods added in Daru::DataFrame:

- reject_values
- include_values?
- replace_values

Methods removed in Daru::Vector:

- missing_values
- missing_values=
- update
- exists?
- set_missing_positions

and other methods `#has_missing_data?`, `#n_valid` have been deprecated.

## Conclusion

- As you can notice the performance of Daru updating methods have undergone a major improvement and the its effects will be far reaching from improving other things in Daru to imporoving the performance in Statsample and Statsample-GLM.
- During the way I learned how to use tools like `ruby-prof` to benchmark the code and understand where's the performance is lagging. 
- I noticed that methods like `#[]` are proving to be bottleneck and there lie chances of their improvement.
- Thanks to [Victor](https://github.com/zverok) for suggestiong this change in Daru, providing with good API and helping me all the way to implement it.
