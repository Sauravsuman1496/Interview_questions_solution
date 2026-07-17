This is one of the most famous pieces of code in computer graphics — the **Fast Inverse Square Root** algorithm, popularized by the game *Quake III Arena*. It calculates the multiplicative inverse of `sqrt(x)`, much faster than a standard computation.

## Code

```cpp
float InvSqrt(float x){
    float xhalf = 0.5f * x; // used in the Newton-Raphson formula; 1 iteration is enough

    int i = *(int*)&x; // store the floating-point bits in an integer via type casting,
                        // to reinterpret the float as an int for bit manipulation
                        // (this does not convert the value, only reinterprets the bits)

    i = 0x5f3759df - (i >> 1); // initial guess for Newton's method;
                                // 0x5f3759df is a magic constant found through trial and error

    x = *(float*)&i; // convert the new bits back into a float

    x = x*(1.5f - xhalf*x*x); // one round of Newton's method

    return x; // result: 1 / sqrt(x)
}
```

**Time Complexity:** O(1)
