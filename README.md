#0x0007 - Rvalue References Move Semantics

## What is a traditional Rvalue
A rvalue refers to some temporary value that is lost after this value is assigned to some container. 

```
int i = 5; // 0 is a rvalue
int j = i; // i is not a rlvalue, but a traditional lvalue because its value will continue even after this assignment
int j = i * j; // i * j is a rvalue
```

In the last example, although *i * j* (let's call it _comp_val_) was assigned to the variable *j* (a traditional lvalue), a temporary block is actually created and then the value of _comp_val_ is copied into this temporary block and then assigned to *j*.  After this assignment, this temporary block is lost.  And therefore, you are not allowed to get/use the address of a rvalue as in  
  ```
  int foo();
  int *int_ptr = &foo(); // error!
  ```

With primitive data, this temporary block copying doesn't seem so harmful.  What if, however, we created a function that returned an object of a more complex nature, such as an array, or vector.

```
vector<int> retAppdData (const vector<int>& v, int dataToAppnd)
{
  vector<int> new_vec;
  for (const auto& itr : v) {
    new_vec.push_back(itr);
  }
  new_vec.push_back(dataToAppnd);
  
  return new_vec;
}
...
...
int main () {
  ...
  vector<int> v = {1,2,3}; // initialize
  int append = 4;// data to append to newly created vector
  
  v = retAppdData(v, append);
  
  return 0;
}
```

Have to return a different vector than the _v_ input parameter because this function is returning a Rvalue, which will not exist after the ``` retAppdData() ``` call.  And the input parameter _v_ has a placeholder or handler somewhere in memory already, therefore it is not modifiable.

So in the pre modern C++, the copy assignment operator (e.g. when we call ``` v = retAppdData() ```) looks like this basically:
  -1. Make a clone of the value returned (Rval) from the ***retAppdData()*** // clone the vector that is now _new_vec_ 
      ***_will call this cloned_new_vec_***  
  -2. Destruct the resource that was held by v // {1,2,3} that was passed into the func() 
  -3. Now we are going to attach the clone temp resource is destroyed (after func call, because the return value of func is a Rvalue -       does't persist).
      
      v was passed in as a lvalue (a reference), so value of where v pointed is destroyed and replaced with new data.
     
Note: The copy constructor would look similiar.

It would seem more logical, that instead of copying all the data from the temp block into where v is pointing, instead we could swap where v pointed originally (e.g. {1,2,3}) and where clone_new_vec points (e.g. {1,2,3,4}) such that now v points to ({1,2,3,4}) and clone_new_vec points to ({1,2,3}).  Now, again, because clone_new_vec is a Rvalue, the Destructor from clone_new_vec will delete the original reference of v and we no longer have to copy all the data.

## This what Rvalue References is trying to solve and this concept is referred to as ***Move Semantics***.
So why Rvalue **Reference**, since reference usually means we can track the memory location and rvalue is opposite.

Essentially when the assignment operator is overloaded such that the pointers are swapped, the returned value (or the rhs of the
  v = retVector call) is being passed as a reference because we need this memory location to swap.
  
 The syntax for calling the overloaded assignment and/or copy constructor is ```X&&```.  
  
Traditional pass by reference, which were called lvalues, were not modifiable.  So in the case above, we would not be able to swap the temp space

What's important to note here is that when you create code and you want to take advantage of the compiler optimizations with Rvalue 
References, have to make sure that the call uses a Rvalue that is a non-const.

For example:
```
vector<int> V;
vector<int> Vfunc ();

func (V) // V is a lvalue and the normal assignment and copy constructor will be called
func (Vfunc()) // this will call the overloaded function because Vfunc() is a Rvalue
```
