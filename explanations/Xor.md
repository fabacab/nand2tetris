# `XOR` logic gate

The Hardware Description Language (HDL) description of the `XOR` functionality begins like this:

```
CHIP Xor {
    IN a, b;
    OUT out;

    PARTS:
    // Put your code here:
}
```

```
a b
0 0
NOT{ NOT[ A AND NOT( A AND B ) ] AND NOT[ B AND NOT( A AND B ) ] }
NOT{ NOT[ 0 AND NOT( 0 AND 0 ) ] AND NOT[ 0 AND NOT( 0 AND 0 ) ] }
NOT{ NOT[ 0 AND NOT( 0 ) ] AND NOT[ 0 AND NOT( 0 ) ] }
NOT{ NOT[ 0 AND 1 ] AND NOT[ 0 AND 1 ] }
NOT{ NOT[ 0 ] AND NOT[ 0 ] }
NOT{ 1 AND 1 }
NOT{ 1 }
0

a b
0 1
NOT{ NOT[ A AND NOT( A AND B ) ] AND NOT[ B AND NOT( A AND B ) ] }
NOT{ NOT[ 0 AND NOT( 0 AND 1 ) ] AND NOT[ 1 AND NOT( 0 AND 1 ) ] }
NOT{ NOT[ 0 AND NOT( 0 ) ] AND NOT[ 1 AND NOT( 0 ) ] }
NOT{ NOT[ 0 AND 1 ] AND NOT[ 1 AND 1 ] }
NOT{ NOT[ 1 ] AND NOT[ 1 ] }
NOT{ 0 AND 0 }
NOT{ 0 }
1

a b
1 0
NOT{ NOT[ A AND NOT( A AND B ) ] AND NOT[ B AND NOT( A AND B ) ] }
NOT{ NOT[ 1 AND NOT( 1 AND 0 ) ] AND NOT[ 0 AND NOT( 1 AND 0 ) ] }
NOT{ NOT[ 1 AND NOT( 0 ) ] AND NOT[ 0 AND NOT( 0 ) ] }
NOT{ NOT[ 1 AND 1 ] AND NOT[ 0 AND 1 ] }
NOT{ NOT[ 1 ] AND NOT[ 0 ] }
NOT{ 0 AND 1 }
NOT{ 0 }
1

a b
1 1
NOT{ NOT[ A AND NOT( A AND B ) ] AND NOT[ B AND NOT( A AND B ) ] }
NOT{ NOT[ 1 AND NOT( 1 AND 1 ) ] AND NOT[ 1 AND NOT( 1 AND 1 ) ] }
NOT{ NOT[ 1 AND NOT( 1 ) ] AND NOT[ 1 AND NOT( 1 ) ] }
NOT{ NOT[ 1 AND 0 ] AND NOT[ 1 AND 0 ] }
NOT{ NOT[ 0 ] AND NOT[ 0 ] }
NOT{ 1 AND 1 }
NOT{ 1 }
0
```
