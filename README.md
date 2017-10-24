#0x0007 - Rvalue References

## What is a traditional Rvalue
If we look at the following simple example:

```
int i = 0;
int j = i;
j = 5;
```
In this example, we see that **i** on the second line of code is a rvalue or temporary value.  After the assignment to _j_, the value in **i** is copied to a newly created block and its contents.  So that is why as we saw, when we assigned j to i and then changed j's value to 5, i remained as 0.  What changed was the temporary (rvalue) block that was copied to j and then after the `int j = i;` returned, this temporary block of memory is released and its changes are lost.

However, if we were to observe this example:

```
int i = 0;
int &j = i; 
j = 5;
```

We are now using a reference and are not copying.  So when the compiler sees _&j_ it knows to only look at the reference or address of _j_, which is set to i.  And then when j is set to 5 (e.g. ``j = 5 ``), then it changes its value as well as what it is pointing to - i.

#### How is this related to Rvalue References
In the first example, whereby we are copying the contents of the variable and then releasing this copied block, imagine we were using a more complex resource, such as a vector, as opposed to a primitive like **int**.

