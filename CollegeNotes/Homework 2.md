```c
int a[100][100]; // we will not be using this for the homework
```

The above code will have a chance to fail because there might not be enough continuous memory to allocate the whole grid
We are going to use dynamic memory to ensure we get the amount of memory we need
**We will need the double pointers to make it work**
**We got to make sure the elephant does not seek outside of the grid we have declared**

## logic of the elephant
1. stay in place if there is food
2. see if the adjacent fields has food
3. back off

Only one action has to take place every hour. It has to first move in hour one, then it has to eat.