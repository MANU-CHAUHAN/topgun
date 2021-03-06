# minigrad
## A small autograd repo implementing backpropagation with ability to develop small Neural Networks.



##### Greatly inspired by Andrej Karpathy's  [micrograd](https://github.com/karpathy/micrograd)




### Reverse Mode Auto DIfferentiation

Let's say we have expression ð§=ð¥1ð¥2+sin(ð¥1) and want to find derivatives ðð§ðð¥1 and ðð§ðð¥2. Reverse-mode AD splits this task into 2 parts, namely, forward and reverse passes.

#### Forward pass:

First step is to decompose the complex expression into a set of primitive ones, i.e. expressions consisting of at most single step or single function call.


    ð¤1 = ð¥1
    
    ð¤2 = ð¥2
    
    ð¤3 = ð¤1 * ð¤2
    
    ð¤4 = sin(ð¤1)
    
    ð¤5 = ð¤3 + ð¤4
    
    ð§ = ð¤5



The advantage of this representation is that differentiation rules for each separate expression are already known.

For example, we know that derivative of `sin` is `cos`, and so `dw4/dw1=cosâ¡(w1)`.

We will use this fact in reverse pass below. Essentially, forward pass consists of evaluating each of these expressions and saving the results. 

Say, our inputs are: `ð¥1=2`  and `ð¥2=3`. Then we have:


    ð¤1 = ð¥1 = 2
    
    ð¤2 = ð¥2 = 3
    
    ð¤3 = ð¤1 * ð¤2 = 6
    
    ð¤4 = sin(ð¤1) = 0.9
    
    ð¤5 = ð¤3 + ð¤4 = 6.9
    
    ð§ = ð¤5 = 6.9




#### Reverse pass:

This is the main part and it uses the **chain rule**.

In its basic form, chain rule states that if you have variable `ð¡(ð¢(ð£))` which depends on `ð¢` which, in its turn, depends on `ð£`, then:

    ðð¡/ðð£ = ðð¡/ðð¢ * ðð¢/ðð£


or, if `ð¡` depends on `ð£` via several paths / variables `ð¢ð`, e.g.:


    ð¢1 = ð(ð£)
    
    ð¢2 = ð(ð£)
    
    ð¡ = â(ð¢1,ð¢2)

then:

    ðð¡/ðð£ = â ðð¡/ðð¢ð *ðð¢ð/ðð£

In terms of expression graph, if we have a final node `ð§` and input nodes `ð¤ð`, and path from `ð§` to `ð¤ð` goes through intermediate nodes `ð¤ð` (i.e. ð§=ð(ð¤ð) where ð¤ð=ð(ð¤ð)), we can find derivative `ðð§/ðð¤ð` as



    ðð§/ðð¤ð = â{ðâPððððð¡ð (ð)} ðð§/ðð¤ð * ðð¤ð/ðð¤ð


In other words, to calculate the derivative of output variable ð§ w.r.t. any intermediate or input variable ð¤ð, we only need to know the derivatives of its parents and the formula to calculate derivative of primitive expression ð¤ð=ð(ð¤ð).


Reverse pass starts at the end (i.e. ðð§/ðð§) and propagates backward to all dependencies. 

    ðð§ / ðð§ = 1

Then we know that ð§=ð¤5 and so:
    
    ðð§ / ðð¤5 = 1

ð¤5 linearly depends on ð¤3 and ð¤4, so ðð¤5/ðð¤3=1 and ðð¤5/ðð¤4=1. Using the chain rule we find:

    ðð§/ðð¤3 = ðð§/ðð¤5 Ã ðð¤5/ðð¤3 = 1Ã1 = 1
     
    ðð§/ðð¤4 = ðð§/ðð¤5 Ã ðð¤5/ðð¤4 = 1Ã1 = 1



From definition ð¤3=ð¤1ð¤2 and rules of partial derivatives, we find that ðð¤3 / ðð¤2=ð¤1. Thus:

    ðð§/ðð¤2 = ðð§/ðð¤3 Ã ðð¤3/ðð¤2 = 1 Ã ð¤1 = ð¤1

Which, as we already know from forward pass, is:

    ðð§/ðð¤2 = ð¤1 = 2



Finally, `ð¤1` contributes to `ð§` via `ð¤3` and `ð¤4`. Once again, from the rules of partial derivatives we know that `ðð¤3/ðð¤1 = ð¤2` and `ðð¤4/ðð¤1 = cos(ð¤1)`. Thus:

    ðð§/ðð¤1 = ðð§/ðð¤3 * ðð¤3/ðð¤1 + ðð§/ðð¤4 * ðð¤4/ðð¤1 = ð¤2 + cos(ð¤1)


And again, given known inputs, we can calculate it:


    ðð§/ðð¤1 = ð¤2 + cos(ð¤1) = 3 + cos(2) = 2.58


Since ð¤1 and ð¤2 are just aliases for ð¥1 and ð¥2, we get our answer:

    ðð§ / ðð¥1 = 2.58

 

    ðð§ / ðð¥2 = 2

And all is done for the given expression!



##### TODO:

1) utils and visualizations

2) Implement all operations:

```
Relu, Log, Exp                          # unary ops
Sum, Max                                # reduce ops (with axis argument)
Add, Sub, Mul, Pow.                     # binary ops (with broadcasting)
Reshape, Transpose, Slice               # movement ops
Matmul, Conv2D                          # processing ops
```

