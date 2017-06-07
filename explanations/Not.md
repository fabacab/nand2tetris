# `NOT` logic gate

The Hardware Description Language (HDL) description of the `NOT` functionality begins like this:

```
CHIP Not {
    IN in;
    OUT out;

    PARTS:
    // Put your code here:
}
```

I chose to implement `NOT` immediately after I chose to [implement `AND`](And.md) because the insight of the "double-negative" I acquired from implementing `AND` also made clear why and how `NAND` operations could be used to simulate the `NOT` functionality.

The meaning of "not" is pretty clear: it is the inverse of whatever input is provided. The challenge in implementing `NOT` from `NAND`, however, is that our pre-provided `NAND` logic gate requires two inputs, whereas the `NOT` logic gate requires one.

Thankfully, we are able to connect (or "wire") any input to any other input; that is the point of designing our own hardware. So, we can just fan the incoming signal (named `in`) to both the `a` and `b` inputs of the `NAND` chip:

```
// Take `Not`'s single input and fan it to both of `Nand`'s inputs.
Nand(a=in, b=in, out=out);
```

This works because, remember, a `NAND` is a "not-and." Assuming the input to the `NOT` is a 0, then (logically speaking) the `NAND` first `AND`s that same 0 with itself, returning 0. The `NAND` then `NOT`s the 0 (i.e., it says "give me not 0"), which is of course its opposite, which is a 1. Likewise, if the input to the `NOT` is a 1, then (logically speaking), the `NAND` first `AND`s that same 1 with itself, returning a 1. The `NAND` then `NOT`s the 1 (i.e., it says "give me not 1"), which is of course its opposite, which is a 0.

In this way, a single `NAND`, fed only one input, is the logical equivalent to a `NOT`.
