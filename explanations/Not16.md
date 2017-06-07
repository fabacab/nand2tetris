# 16-bit `NOT` logic gate

The Hardware Description Language (HDL) description of the 16-bit `NOT` functionality begins like this:

```
CHIP Not16 {
    IN in[16];
    OUT out[16];

    PARTS:
    // Put your code here:
}
```

This is simply the exact same functionality as [the single-bit `NOT` functionality](Not.md), so the challenge is purely in understanding the syntax of the nand2tetris HDL itself. The thing to know is that you need not specify the index of the bit on the bus to the left of the equals sign (`=`) in your code. Instead, do so on the right.

Specifically, this will fail with a syntax error:

```
Not(in=in[0], out[0]=out);
```

Instead, write it like so:

```
Not(in=in[0], out=out[0]);
```

This is not totally clear, because even the HDL Survival Guide discusses sub-busing with an example that creates an *internal* sub-bus. That makes sense, as the symbols to the left of the equals sign are the *inputs* to the chip being fed. In that case, the you can feed specific bits to specific wires on a bus. However, no example is shown for these most simple of cases.

The way to understand this is that in the first, broken statement, the signature for the chip called `Not` only ever takes one bit; hence its `out` parameter should not be treated like an array of bits (a bus). But the chip called `Not16` does have an array for both its `in` and its `out`, and since the right-hand side of the equal sign is the part of the syntax that maps to the chip we are constructing, that is where the array syntax (the bit indexes) should go.
