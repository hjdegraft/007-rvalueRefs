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

We are now using a reference and are not copying.  So when the compiler sees _&j_ it knows to only look at the reference or address of _j_, which is set to i.  And then when j is assigned to 5 (e.g. ``j = 5 ``), then it changes its value as well as what it is pointing to (e.g. _i_).  So essentially, _j's_ pointer was moved to _i_.

#### How is this related to Rvalue References
In the first example, whereby we are copying the contents of the variable into some temporary memory and then releasing, imagine we were using a more complex resource, such as a vector, as opposed to a primitive like **int**.

So for example, we have some func named getDoubleVal(), which doubles the value of the data.
```
vector<int> getDoubleVal (const vector<int>& v)
{
  vector<int> new_val;
  for (const auto& itr : v)
    new_val.push_back(2 * *itr)
    
  return new_val;
}

```

If we later added 
```
int main()
{
    vector<int> v;
    for ( const auto& i : v )
    {
        v.push_back( i );
    }
    v = doubleValues( v );
}
```

In this example, not only are we doubling the value, but when ***getDoubleVal()*** is called this object returned (e.g. _v_), once it is copied, will just be thrown away.  So we are having to reallocate some chunk of memory with doubled value data.   *And again, highlighting that although the _new_val_v_ was assigned to the _input_v_, after _new_val_v_ had its data doubled, _input_v_ data remained as originally initialized.*

So in this case
```
input_v => i // from previous example
new_val_v => j 
ret val from getDoubleVal() => 5
```

#### What is the problem Rvalue References are trying to solve
When you have a temporary object, as in the case of a vector, and after contents are copied, when the function ends, this object is destroyed.  Why not take the temporary object's resources and safely allow another object to use them.  So it would seem logical to instead of copying the data into the temporary object, just point the data in _input_val_) to the temporary object. That way you do not have to copy.  And pre modern C++, there was only one copy constructor and one assignment operator.  However, now with #####***Rvalue References*** you overload these functions.  You are now able to determine if the object being passed is just some temporary value (e.g. ***Rvale***) and then call the correct the constructor/assignment.  

Now, as we described in the ``` int &j = i ``` sample code earlier, when passing a reference, you are not creating/copying a new resource, but instead directing its address.  Pre modern C++, this concept referenced const or non modifiable (mutable) objects or ***lvalue***.  When using Rvalue References, you know the object used will be temporary, however the difference is that this object needs to be modifiable.  

So again, let's look at a generic copy constructor...

```
 SomeObj (const SomeObj& origObj)
            : _p_vals( new int[ origObj._size  ] )
            , _size( origObj._size )
        {
            for ( int i = 0; i < _size; ++i )
            {
                _p_vals[ i ] = origObj._p_vals[ i ];
            }
        }
 ```
 
 The overloaded copy constructor that's used for Rvalue References, looks more like...

```
SomeObj (SomeObj&& origObj)
        : _p_vals( origObj._p_vals  )
        , _size( origObj._size )
    {
        origObj._p_vals = NULL;
        origObj._size = 0;
    }
```

We see that the parameter is a non-const rvalue (temporary object) reference.  Temporary object and therefore an instance is still created (not reallocating new memory) and a reference because we did not copy any data.  But why is it a non-const?  If it were still const as in the original copy constructor, you would not be able to modify its members and set them to NULL/0.  So why are...  Remember if you have a function that returns a const object, the original copy constructor will be called.  Therefore make sure 


To demonstrate this overloaded constructor, I wrote the following simple code...

```









