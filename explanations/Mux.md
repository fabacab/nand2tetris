# `MUX` logic gate

The Hardware Description Language (HDL) description of the `MUX` functionality begins like this:

```
CHIP Mux {
    IN a, b, sel;
    OUT out;

    PARTS:
    // Put your code here:
}
```

The selector ("multiplexor") functionality seen here is the first three-input chip. Again, it sounds simple enough: output the value of the given input depending on the selection bit input. If the *sel*ector is 0, output whatever the value of `a` is. If the selector is 1, output whatever the value of `b` is. Another way to say it is "output the value of A or the value of B, depending on the value of S."

The first thing we know we are going to need is an `OR` because of the way the requirement is described ("output A something something OR output B"). The challenge is that a simple `OR` is not enough, for two reasons. First, our `OR` gate only takes two inputs, and the `mux` interface requires three. Second, our `OR` gate does not output the *value* of its input, it outputs its own value *based* on its input.

The second insight to draw here is that the value of the two data inputs (`a` and `b`) do not actually matter. They will be whatever they are set to, and we merely need to retain them as-is. The important bit for us is the value of the selection bit.

Let us therefore explore this bit first.

Recall that the API of the muxer demands that we output the value of `a` if (and only if) the selection bit is 0. The keyword here is "if," which we can model in electronic circuits using the `AND` gate. This is because the `AND` gate effectively checks one of its arguments (inputs) against a second, and conditionally outputs a value, i.e., a `1`, if (and only if) the two arguments (inputs) are also a `1`.

| a | sel | a AND sel |
|---|-----|-----------|
| 0 |  0  |    0      |
| 0 |  1  |    0      |
| 1 |  0  |    0      |
| 1 |  1  |    1      |

Since this will be the same for both `a` and `b`, however, `AND`'ing the selection with `a` and then also with `b` won't work. Let's see why.

| b | sel | b AND sel |
|---|-----|-----------|
| 0 |  0  |    0      |
| 0 |  1  |    0      |
| 1 |  0  |    0      |
| 1 |  1  |    1      |

In HDL:

```
And(a=a, b=sel, out=aANDsel);
And(a=b, b=sel, out=bANDsel);
```

Now that we have both an `a AND sel` and a `b AND sel`, let's do the intuitive thing and `OR` them:

| a AND sel | b AND sel | (a AND sel) OR (b AND sel) |
|-----------|-----------|----------------------------|
|    0      |    0      |             0              |
|    0      |    0      |             0              |
|    0      |    0      |             0              |
|    1      |    1      |             1              |

```
Or(a=aANDsel, b=bANDsel, out=aANDselORbANDsel);
```

That was rather uninteresting; it fails because there is no conversion, no "else" in the chip design. This is because there is only ever the selection bit, and it is always being compared with itself. For an "either/or" situation, we need to compare the selection bit with its opposite. To get the opposite value from a given input, we use the `NOT` chip, of course:

| sel | NOT sel |
|-----|---------|
|  0  |    1    |
|  1  |    0    |

In HDL:

```
Not(in=sel, out=NOTsel);
```

Okay, now we have both the value of the `sel`ector bit *and* its opposite. As before, we can use the `AND` chip to create an "if" condition ("if the value of `sel` is 0, output `a`"):

| a | sel | a AND sel |
|---|-----|-----------|
| 0 |  0  |     0     |
| 0 |  1  |     0     |
| 1 |  0  |     0     |
| 1 |  1  |     1     |

Similarly, we can ready the else condition by `AND`ing the value of an input with the inverted value of the selector bit (i.e., with `NOT(sel)`):

| a | NOT(sel) | a AND NOT(sel) |
|---|----------|----------------|
| 0 |    1     |       0        |
| 0 |    0     |       0        |
| 1 |    1     |       1        |
| 1 |    0     |       0        |

Notice the combination of these two tables produces a 1 in both cases where `a` is set to 1 and a 0 in both cases where `a` is set to 0, which is precisely what we want. We can call this "select A" and write it in HDL like so:

```
Not(in=sel, out=NOTsel);
And(a=a, b=NOTsel, out=SELa);
```

However, once gain, if we merely now `OR`'ed this with our original input of `b`, we would not get the desired result, because `OR` is still outputting its own value, not a value based on the selector's bit:

| SELa | b | SELa OR b |
|------|---|-----------|
|  0   | 0 |     0     |
|  0   | 1 |     1     |
|  1   | 0 |     1     |
|  1   | 1 |     1     |

The issue with the above truth table is that there are more cases that will emit a 1 than there are that will emit a 0. This does not match the truth table for the mux functionality, which has a total of 8 possible outputs (because with three inputs, of two possible values per input, we get 2^3=8), and has an equal number of 0's and 1's possible (four possible 0's and four possible 1's):

| sel | a | b | out |
|-----|---|---|-----|
|  0  | 0 | 0 |  0  |
|  0  | 0 | 1 |  0  |
|  0  | 1 | 0 |  1  |
|  0  | 1 | 1 |  1  |
|  1  | 0 | 0 |  0  |
|  1  | 0 | 1 |  1  |
|  1  | 1 | 0 |  0  |
|  1  | 1 | 1 |  1  |

What is still missing from our design is an equivalent of `SELa`, but for `b`. We can create that the same way we created it for `a`: by using `AND`. We do not want to invert the signal, because then we will have the same situation we had in the beginning where the selection bit was the same for both conditions (which yields the same results).

| b | sel | bANDsel |
|---|-----|---------|
| 0 |  0  |    0    |
| 0 |  1  |    0    |
| 1 |  0  |    0    |
| 1 |  1  |    1    |

We can now finally `OR` these results in such a way as to produce the desired result.

| bANDsel | SELa | bANDsel OR SELa |
|---------|------|-----------------|
|    0    |  0   |        0        |
|    0    |  0   |        0        |
|    0    |  1   |        1        |
|    1    |  1   |        1        |

In HDL:

```
Not(in=sel, out=NOTsel);
And(a=a, b=NOTsel, out=SELa);
And(a=b, b=sel, out=bANDsel);
Or(a=SELa, b=bANDsel, out=out);
```

The name `bANDsel` is confusing, and since it functions as the reciprocal to our `SELa` wire, we can rename it to `SELb` for clarity.
