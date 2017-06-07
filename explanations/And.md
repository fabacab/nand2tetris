# `AND` logic gate

The Hardware Description Language (HDL) description of the `AND` functionality begins like this:

```
CHIP And {
    IN a, b;
    OUT out;

    PARTS:
    // Put your code here:
}
```

The `AND` gate is the first elementary logic gate I chose to implement. I chose this because it bears a clear relationship to the premise of the nand2tetris course. The premise of the course is that we have been presented with a single logical operation (`NAND`), and from here we must build a computer. `AND` is of course related to `NAND`; it is its inverse.

By this we mean that the functionality of `NAND` is equivalent to that of `NOT(AND(a, b))`. In other words, given some inputs `a` and `b`, "`NAND`'ing" them together would produce the same exact result as if we "`AND`'ed" them and then also "`NOT`'ed" that subsequent result.

This insight provides a hint regarding how to approach the challenge of building an `AND` logic gate merely from `NAND` functionality: a double-negative is a positive.

For example, if I were to tell you "I do not not want ice cream," I would actually be implying that I *do* want ice cream (or at least something like it). This is beacuse the first "not" negates the meaning of the second. Although a human who expressed their feelings in this way may indeed be speaking about vaguaries in their desire, a computer has no such ambiguity, for it deals solely in binary math, meaning boolean logic, meaning ones and zeroes, meaning true or false. Either the computer does want ice cream, or it does not. There is no "maybe."

If we are given only the ability to `NAND` inputs together, then we can simulate the logic of `AND` by negating the logic of our own `NAND`. First, we `NAND` our original inputs, and remember the result; this is like saying "*not* want ice cream." We then `NAND` the original inputs *again* and remember that same result a second time; this is like saying "*not* not want ice cream." Finally, we use those remembered results as inputs to a third `NAND`, and output its result; this is the equivalent of our input phrase: "I do want ice cream."

In the nand2tetris Hardware Description Language (HDL):

```
// Remember the result of NAND'ing `a` with `b` a first time as `w1`.
Nand(a=a, b=b, out=w1);

// Remember the result of NAND'ing `a` with `b` a second time as `w2`.
Nand(a=a, b=b, out=w2);

// Now NAND our remembered results with one another as though they were
// our original `a` and `b` inputs, outputting the result.
Nand(a=w1, b=w2, out=out);
```

This naïve implementation of `AND` uses three `NAND` gates, but it gets the job done.
