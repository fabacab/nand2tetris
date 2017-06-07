# `OR` logic gate

The Hardware Description Language (HDL) description of the `OR` functionality begins like this:

```
CHIP Or {
    IN a, b;
    OUT out;

    PARTS:
    // Put your code here:
}
```

Having only described `AND`- and `NOT`-like logics to myself, `OR` was a bit of a puzzle. The idea here is "either `a` or `b`," which sounds simple enough but it was not intuitively obvious to me how to achieve this with merely `AND` and `NOT` logic. Thankfully, examining the truth table for `OR` and comparing it to existing functionality we already have provides a clue.

The truth table, available in the comparison file, for `OR` is:

|   a   |   b   |  OR out (desired result)  |
|-------|-------|---------------------------|
|   0   |   0   |   0                       |
|   0   |   1   |   1                       |
|   1   |   0   |   1                       |
|   1   |   1   |   1                       |

The `out` column here is most similar to the truth table for `NAND`, which is tricky because we do not have that in a comparison file because we simply assume its presence. However, when we write it out, we see the similarity.

The truth table for `NAND` is:

|   a   |   b   |  NAND out  |
|-------|-------|------------|
|   0   |   0   |   1        |
|   0   |   1   |   1        |
|   1   |   0   |   1        |
|   1   |   1   |   0        |

In both tables we have three cases where there a 1 is emitted and one case where a 0 is emitted. The challenge is that only the different cases are inverted while the others are the same. This means our implementation requires an additional, intermediary step to transform the inputs to the desired output; it will not be enough to use an existing elementary logic gate once. We must do something more. But what?

Personally, I can most easily visualize this by writing imaginary "truth tables" that describe the values of the bits at each stage in a pipeline. For instance, given inputs `a` and `b`, I will add a new column to the "truth table" in between the columns for `b` and `out` with a heading describing the operation I have performed. For instance, if I `AND` the bits:

|   a   |   b   | aANDb |  OR out (desired result) |
|-------|-------|-------|--------------------------|
|   0   |   0   |   0   |   0                      |
|   0   |   1   |   0   |   1                      |
|   1   |   0   |   0   |   1                      |
|   1   |   1   |   1   |   1                      |

This is not a "real" truth table for `OR`, of course, because there are not three distinct inputs to the `OR` interface. But I am actually exploring the implementation, not the interface. In other words, `aANDb` is an "internal wire."

From here, I next imagine what combination of columns to the left of `out` I can use in combination with what elementary operation I have available to achieve the desired output shown in the `out` column. The constraint is that I the resulting operation must be applied to *all* of the rows (bits) and must *exactly* match the resulting `out` column.

There are three elementary operations available at the moment: `NAND` (the one we were given), `AND`, and finally `NOT`. As is obvious from the table above, given `aANDb`, a simple `NOT` would flip the bits, but not would produce the desired result, which we can see because the final two columns do not match exactly:

|   a   |   b   | aANDb | NOTaANDb |  OR out (desired result) |
|-------|-------|-------|----------|--------------------------|
|   0   |   0   |   0   |     1    |   0                      |
|   0   |   1   |   0   |     1    |   1                      |
|   1   |   0   |   0   |     1    |   1                      |
|   1   |   1   |   1   |     0    |   1                      |

The above table is a way for me to describe an implementation such as this in the nand2tetris HDL:

```
And(a=a, b=b, out=aANDb);
Not(in=aANDb, out=out);
```

Using the same scheme, inverting the inputs would result in a table like this:

|   a   |   b   | NOTa | NOTb |  OR out (desired result) |
|-------|-------|------|------|--------------------------|
|   0   |   0   |  1   |  1   |   0                      |
|   0   |   1   |  1   |  0   |   1                      |
|   1   |   0   |  0   |  1   |   1                      |
|   1   |   1   |  0   |  0   |   1                      |

What's noteworthy about this table is that the last three columns (`NOTa`, `NOTb` and `out`) is identical to an exactly-inverted `NAND` truth table. That means, if we next do a `NAND` with `NOTa` and `NOTb`, we get the desired result:

|   a   |   b   | NOTa | NOTb | NOTa NAND NOTb | OR out (desired result) |
|-------|-------|------|------|----------------|-------------------------|
|   0   |   0   |  1   |  1   |       0        |   0                     |
|   0   |   1   |  1   |  0   |       1        |   1                     |
|   1   |   0   |  0   |  1   |       1        |   1                     |
|   1   |   1   |  0   |  0   |       1        |   1                     |

In the nand2tetris HDL, this could be written as follows:

```
Not(in=a, out=NOTa);
Not(in=b, out=NOTb);
Nand(a=NOTa, b=NOTb, out=out);
```

This is not a mechanism of figuring out specific chip implementations I've seen before, and it may not be "the best way" but it is a way that makes enough sense to me, for now, that it has become useful.
