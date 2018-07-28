---
title: "Mutually Recursive Prime Functions"
subtitle: "Part III - Closing the Loop"
date: 2018-07-28
publishdate: 2018-07-28
type: post
categories: ["software"]
tags: ["prime numbers", "python", "recursion", "toy code"]
---

In the previous two posts I covered recursion, mutual recursion, primality
testing and prime generation. In this post we'll close the loop (or maybe that
should be *loops*) by making all of the functions we've covered so far
interdependent, so that no matter which one we call there's always a chance
that all of them will find there way onto the call stack.

**Everything Depends on Something**

What a shame `primes_to` is only ever called if _we_ call it. Wouldn't it be
great if we had three functions, all intertwined into the functional equivalent
of the [Borromean Rings](https://en.wikipedia.org/wiki/Borromean_rings)?
So that if we called any one function it may result in any one of the others
being called too? Or if we removed any one function it would result in two
"unlinked" functions that wouldn't work anymore? Well I do... and we can!

In `prime_factors` there's this bit of code:

```python3
    for factor in range(max_factor+1):
        if not is_prime(factor):
            continue
        if i % factor == 0:
            yield factor
```

That is iterating over a range up to some limit, checking that each number is
prime and then also checking whether it is a factor. Part of that is already
accomplished by `primes_to`, so we can just sub it in and we'll have achieved
our goal! So here are the final functions together in all their interdependent
glory.

```python3
from math import sqrt


def is_prime(i):
    if i < 2 or list(prime_factors(i)):
        return False
    return True


def primes_to(limit, inclusive=False):
    if inclusive:
        limit += 1
    for i in range(2, limit):
        if is_prime(i):
            yield i


def prime_factors(i):
    max_factor = int(sqrt(i))
    for prime in primes_to(max_factor, inclusive=True):
        if i % prime == 0:
            yield prime
            remainder = i // prime
            if is_prime(remainder):
                yield remainder
            else:
                yield from prime_factors(remainder)
            break
```

Now you have the full code listing I invite you to try and take it further.
Can you make it four interdependent functions, or more? What other
functionality could you incorporate?

Also, don't forget to test it! Don't just blindly take my word for it that it
works, see if you can break it; really put it through its paces.

And until next time, happy coding!
