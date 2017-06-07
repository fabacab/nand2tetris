# 16-bit `AND` logic gate

The Hardware Description Language (HDL) description of the 16-bit `AND` functionality begins like this:

```
CHIP And16 {
    IN a[16], b[16];
    OUT out[16];

    PARTS:
    // Put your code here:
}
```

This is simply a 16-bit variant of the exact same `AND` logic gate functionality created with our earlier, single-bit logic gate. We need merely apply the earlier `AND` gate to each individual set of input bits. The trickiest thing about this is the HDL syntax itself, which I described in [the `Not16` chip explanation](Not16.md).
