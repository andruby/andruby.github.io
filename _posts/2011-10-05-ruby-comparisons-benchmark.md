---
layout: post
title: Ruby comparisons benchmark
tags: []
status: publish
type: post
published: true
meta: {}
---

I recently had to make a huge loop faster (about 10 milllion iterations). The loop spent quite some time checking the state of an object, so I benchmarked some different comparisons in Ruby, on different ruby implementations (Rubinius 2.0.0 pre, MRI 1.9.2-p290 and REE-1.8.7)

{% gist 1264985 comparison.rb %}

Conclusions:

* Comparing symbols is a faster than comparing strings (2.0x~2.5x)
* Integer comparison is even faster (2x in Rubinius, 1.4x in Ruby 1.9.2)
* Using constants that point to integers carries no overhead in 1.9.2, ~25% in ree-1.8.7 and rbx

Between implementations:

* Method calling: Rubinius is ~2x faster than Ruby 1.9.2, who in turn is ~2x faster than ree-1.8.7
* Comparing symbols, integers and integer constants in Rubinius is ~as fast as calling a (boolean) method.
* BUT: defining methods on the fly in Rubinius-2.0.0pre is 2x slower than regular Ruby
* Ruby 1.9.2 has significantly faster comparisons than ree-1.8.7

I might switch from string comparison to symbol (or constant integer) comparison in heavy loops.

**NOTE:** Benches were run on a 2011 MacBook Pro 2.66 Ghz Core i7.
