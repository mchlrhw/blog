---
title: "Mutually Recursive Prime Functions"
subtitle: "Part II - Prime Generation"
date: 2018-05-21
publishdate: 2018-05-21
type: post
categories: ["software"]
tags: ["prime numbers", "python", "recursion", "toy code"]
---

In the previous post I talked about what recursion is, what it takes for a
number to be prime, and how we can apply recursion to the problem of primality
testing and prime factorisation. In this post we'll take the next logical step:
finding primes in a sea of integers.

**Hunting for Primes**

So, after the work we did in the previous post, we now have two functions that
depend on each other to work at all; but we can do better. What about if we add
a third that generates prime numbers? So that instead of constantly having to
ask "is this prime?" manually for every integer, we just say "give me all the
primes up to this limit" and the function does it for us. That'd be pretty
sweet, no?

Well I think it would, so here it is:

```python3
def primes_to(limit, inclusive=False):
    if inclusive:
        limit += 1
    for i in range(2, limit):
        if is_prime(i):
            yield i
```

We can then call it like this:

```python3
def main():
    for p in primes_to(50):
        print(p, end=', ')
    print("...")


if __name__ == "__main__":
    main()
```

and get the output:

```
2, 3, 5, 7, 11, 13, 17, 19, 23, 29, 31, 37, 41, 43, 47, ...
```

Nice! We could even take this a step further by converting `primes_to` into
`primes_between` which would take two integers and give you all the primes
between them. However, this introduces problems like validating user input
(negative integers would unnecessarily complicate our algorithm) that we don't
really want to worry about, so we'll leave it as `primes_to`. Feel free to come
up with an implementation of your own for `primes_between`.

**Post Script**

Right, so to recap, we now have three functions relating to primes: one to test
for primality; one to produce the prime factors of a number; and one to
generate primes up to a limit. The first depends on the second; the second
depends on the first and itself; and the third depends on the first.

Nothing depends on the third though, that's a downer! It doesn't really feel
like `primes_to` is really contributing to the mutual recursion, just feeding
off it.

Never fear, we'll fix that in the next instalment!
