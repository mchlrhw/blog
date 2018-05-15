---
title: "Mutually Recursive Prime Functions"
subtitle: "Part I - Primality"
publishdate: 2018-05-15
type: post
categories: ["software"]
tags: ["prime numbers", "python", "recursion", "toy code"]
---

OK, I admit it... I love prime numbers. I don't know why, but I know I'm not
alone. There's just something about them. Maybe it's their indivisible nature
-- their atomicity -- that seems so appealing. Maybe it's the fact that they're
well understood, but nevertheless intangible -- there's no equation for finding
primes and most of our methods for finding them involve brute-force. Anyway,
whatever it is, I just love them and I love writing code to generate them.

Another love of mine is recursion. It too has a certain _je ne sais quois_
about it, which I think is rooted in the almost Ouroborean way recursive
functions interact with themselves.

So, as the title suggests, this series of posts will focus on recursion (or
more specifically _mutual_ recursion) and how it can be used to test for
primality. An intoxicating partnership, I hope you'll agree.

**Recursion**

The classic example of recursion in computing is a function to generate the
Fibonacci sequence. Here's what that might look like in Python:

```python3
from itertools import takewhile


def fib_gen(a, b):
    yield a
    a, b = b, a+b
    yield from fib_gen(a, b)


def main():
    for i in takewhile(lambda i: i<150, fib_gen(0, 1)):
        print(i, end=", ")
    print("...")


if __name__ == "__main__":
    main()
```

Which produces the output:

```
0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89, 144, ...
```

Nothing new so far. We have a function that calls itself to generate higher
order terms in the sequence; we have recursion!


Mutual recursion is like regular recursion, except better! It involves two or
more functions, each of which is dependent on one or more of the others to do
its job. Give me moar recursion, I want MOAR!

OK, so now that we've had a refresher in recursion (and I've come down from
my recursion high) it's time to look at primality testing.

**Primes**

The definition of a prime number is a positive integer that has exactly two
factors: 1 and a positive integer that is not 1. Or in other words, any
positive integer greater than 1 that has no prime factors, other than itself.

Errr, hang on... that looks almost _recursive_ to me. And if you thought that
then you'd be right. It's a slight rephrasing of the Fundamental Theorem of
Arithmetic where "every integer larger than 1 can be written as a product of
one or more primes". This fact alone means that it should be possible to create
two mutually recursive functions: one to test for primality using the prime
factorisation of a number; and one to generate prime factors of a number by
testing for primality.

**Finding Primes with Recursion**

Enough talk, let's see some code:

```python3
def is_prime(i):
    if i < 2 or list(prime_factors(i)):
        return False
    return True
```

The first function we'll look at is `is_prime`. It can't really do a whole lot
because it's reliant on an implementation of `prime_factors` to work, but the
logic makes sense. If the integer we're testing is less than 2 then we already
know it can't be prime. If the integer has any prime factors at all then we
also know it can't be prime. Otherwise it is prime.

This puts a requirement on our implementation of `prime_factors`: it *cannot*
under any circumstances return the number itself as a factor, even if it is
prime. But if you think about it, that is a reasonable constraint to place on a
function that tells you the prime factors of a number. We already know that
primes have themselves and 1 as factors, because every integer does, so
returning them from a call to the function is about as useful as saying the
factors of 1 are 1 x 1 x 1 x 1 x 1 ...

So what we really want our `prime_factors` function to do is to break apart
non-primes into pieces. If those pieces can be broken down further then we
want to keep on going until all we have left are primes. Wow, looks like we
might have another recursion site. So these functions won't just be mutually
recursive, one of them will also be recursive in the regular sense!

Here's what that looks like:

```python3
from math import sqrt


def prime_factors(i):
    max_factor = int(sqrt(i))
    for factor in range(max_factor+1):
        if not is_prime(factor):
            continue
        if i % factor == 0:
            yield factor
            remainder = i // factor
            if is_prime(remainder):
                yield remainder
            else:
                yield from prime_factors(remainder)
            break
```

(I've tried to eliminate optimisations as much as possible because that's not
what this post is about; however, I did choose to make use of the fact that,
when factorising, the largest candidate you need to consider is the square root
of the number.)

So, we've done it. We have two prime-related functions that are mutually
recursive. But wait, we're not done yet, are we? We can detect primes, and find
the prime factors of composite numbers, but what about generating primes like
we generated the Fibonacci numbers earlier? Well, in the next post we'll take
it one step further and make this a trifecta of recursive goodness! Stay
tuned...

**Post Script**

Putting my love for it to one side for a moment, recursion does have a pretty
major flaw in that it's possible to build up very large call stacks, especially
if your algorithm isn't tail-call-optimised and/or your language of choice
doesn't support it.

This means that eventually we'll blow the stack because we will run out of
memory to store each function call's stack frame, but I've chosen to gloss over
that detail in this series of posts. This is just a little toy example after
all and you'd never generate primes like this in a production environment, so
let's not worry about it, OK? Good!
