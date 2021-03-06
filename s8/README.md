# EVA S8 notes:



## Momentum:

Momentum is a concept in analog physics. Generally speaking, the momentum of an object refers to the tendency of the object to keep moving in the direction of its motion, which is the product of the mass and speed of the object.

**Here’s a popular story about momentum**: gradient descent is a man walking down a hill. He follows the steepest path downwards; his progress is slow, but steady. Momentum is a heavy ball rolling down the same hill. The added inertia acts both as a smoother and an accelerator, dampening oscillations and causing us to barrel through narrow valleys, small humps and local minima.

<u>This standard story isn’t wrong</u>, *but* it fails to explain many important behaviors of momentum. In fact, momentum can be understood far more precisely if we study it on the right model. [[distill](https://distill.pub/2017/momentum/)]

One nice model is the convex quadratic. This model is rich enough to reproduce momentum’s local dynamics in real problems, and yet simple enough to be understood in closed form. This balance gives us powerful traction for understanding this algorithm.



We begin with **gradient descent**. The algorithm has many virtues, but speed is not one of them. It is simple — when optimizing a smooth function ***f***, we make a small step in the gradient w (*k*+1) = *w*( *k*) − *α* * *∇* *f* (*w* (*k*) )

For a step-size *small enough*, gradient descent makes a monotonic improvement at every iteration. It always converges, albeit to a local minimum. And under a few weak curvature conditions it can even get there at an exponential rate.



But the exponential decrease, though appealing in theory, can often be infuriatingly small. Things often begin quite well — with an impressive, almost immediate decrease in the loss. But as the iterations progress, things start to slow down. You start to get a nagging feeling you’re not making as much progress as you should be (so true 😑). What has gone wrong?



The problem could be the optimizer’s old nemesis, pathological curvature. Pathological curvature is, simply put, regions of ***f*** which aren’t scaled properly. The landscapes are often described as <u>valleys, trenches, canals and ravines</u>. The iterates either jump between valleys, or approach the optimum in small, timid steps. Progress along certain directions grind to a halt. In these unfortunate regions, gradient descent fumbles.

**Momentum proposes the following tweak to gradient descent. We give gradient descent a short-term memory:**

​																						*z* (*k*+1) = *β* * *z*(*k*) + ∇ *f* (*w*(*k*)) 

​																						w (*k*+1) = *w*( *k*) − *α* * *z*(*k*+1)

The change is innocent, and costs almost nothing. When *β*=0, we recover gradient descent. But forβ*=0.99 this appears to be the boost we need. Our iterations regain that speed and boldness it lost, speeding to the optimum with a renewed energy.

Optimizers call this minor miracle **“acceleration”**.



### Simple Moving Average:

For a simple moving average, the formula is the sum of the data points over a given period divided by the number of periods.

MA=(P1+ P2 +P3 + P4 + P5)/5 --> An example of simple moving average for 5 points. When next point comes in, simply move the window (still taking in 5 point) so that (P2 + P3 + P4 + P5 + P6)/5 becomes new value for moving average.



Using moving averages is an effective method for eliminating strong fluctuations. **The key limitation** is that data points from older data are not weighted any differently than new data points. This is where weighted moving averages come into play.



### Weighted Moving Average:

This approach assign a heavier weighting to more current data points since they are more relevant than data points in the distant past. The sum of the weighting should add up to 1 (or 100 percent). In the case of the simple moving average, the weightings are equally distributed.

Example:

WMA = (Price1×*n* + Price2×(*n*−1) + ⋯ Price *n*) / (*n/2*×(*n*+1))

where: n=Time period



### Exponentially Weighted Moving Average:

Exponentially weighed averages deal with sequences of numbers.



![](https://miro.medium.com/max/1098/1*KQC1UiYUxdzA5IsSEg4Gow.png)





![](https://miro.medium.com/max/1162/1*U5g-MNIKrZjVnI12ePtbLw.png)



![](https://miro.medium.com/max/1164/1*fhHakQ1nWN7HK1KBNdarqw.png)



That sequence V is the one plotted yellow above.

We’re approximately averaging over last *1 / (1- beta)* points of sequence.

![](https://miro.medium.com/max/1164/1*buj-RJg3wW6RSclnpczkzA.png)





As you can see, with smaller numbers of beta, the new sequence turns out to be fluctuating a lot, because we’re averaging over smaller number of examples and therefore are ‘closer’ to the noisy data. With bigger values of beta, like *beta=0.98*, we get much smother curve, but it’s a little bit shifted to the right, because we average over larger number of example(around 50 for *beta=0.98*). *Beta = 0.9* provides a good balance between these two extremes.



## SGD with Momentum:



![](https://miro.medium.com/max/1400/1*V5fNciao4YpMl0Of_8v2yw.png)



Where *L* — is loss function, triangular thing — gradient w.r.t weight and *alpha* — learning rate



## Nesterov accelerated gradient:



![](https://cs231n.github.io/assets/nn3/nesterov.jpeg)



## Why momentum really works:

**One major reason is**: when we perform SGD, (mini-batch in reality as we choose batch size to speed up process instead of one image at a time), we are **not computing the exact derivative of our actual loss function** as we are estimating it on a small batch at a time and not the entire dataset. Thus the derivatives are kind of noisy to say which makes it difficult for network to learn.

Adding exponential weighted average kind of smoothes out the average and thus helps to provide better estimate as it includes average over previous calculated average.



**The other reason** lies in ravines. Ravine is an area, where the surface curves much more steeply in one dimension than in another. Ravines are common near local minimas in deep learning and SGD has troubles navigating them. SGD will tend to oscillate across the narrow ravine since the negative gradient will point down one of the steep sides rather than along the ravine towards the optimum. Momentum helps accelerate gradients in the right direction. below image 1 vs image 2(with momentum)



   ![](https://miro.medium.com/max/820/1*JHYIDkzf1ImuZK487q_kiw.gif)                            ![](https://miro.medium.com/max/820/1*uTiP1uRl2CaHaA-dFu3NKw.gif)







----





![](https://cs231n.github.io/assets/nn3/opt2.gif)



![](https://cs231n.github.io/assets/nn3/opt1.gif)







# The 1cycle learning rate scheduler



## Cyclic learning rates

**Cyclic learning rates** (and cyclic momentum, which usually goes hand-in-hand) is a learning rate scheduling technique for (1) faster training of a network and (2) a finer understanding of the optimal learning rate. Cyclic learning rates have an effect on the model training process known somewhat fancifully as "superconvergence".

To apply cyclic learning rate and cyclic momentum to a run, begin by specifying a minimum and maximum learning rate and a minimum and maximum momentum. Over the course of a training run, the learning rate will be inversely scaled from its minimum to its maximum value and then back again, while the inverse will occur with the momentum. At the very end of training the learning rate will be reduced even further, an order of magnitude or two below the minimum learning rate, in order to squeeze out the last bit of convergence.

This was graphed in [the paper that introduced this idea](https://arxiv.org/abs/1803.09820) as follows:

![img](https://i.imgur.com/cs84ifb.png)

The maximum should be the value picked with a learning rate finder procedure, and the minimum value can be ten times lower.

## One-cycle training

This cyclic learning rate policy is meant to be applied over one entire learning cycle: e.g. one epoch. Fast.AI calls this the **one cycle training**. After each cycle, you are supposed to re-apply the learning rate finder to find new good values, and then fit another cycle, until no more training occurs; hence the name.

## Why it works

Optimally, learning rate and momentum should be set to a value which just causes the network to begin to diverge at its peak. The remainder of the training regimen performs learning rate annealing: the learning rate is gradually reduced, allowing the model to settle into the point of minimality in the cost surface. Momentum is counterproductive when the learning rate is very high, which is why momentum is annealed in the opposite of the way in which the learning rate is annealed in the optimizer.

Cyclic learning rates work well in practice because it combines the fastest possible learning rate for the macro discovery of the true minimum region on the cost surface with the micro discovery of the optimal point within that region as the learning rate is annealed.

Cyclic learning rate scheduling thus combines discovery of the maximum practical learning rate (given a certain batch size) with learning rate annealing. You can use cyclic learning to discover the maximum practical learning rate (too high and it will diverge, too low and it will not stabilize at the top), and then switch to adaptive learning rate scheduling (which is what most `keras` users use) with that learning rate, if you so desire.

## Practical outcome

Using the cyclic learning as presented in the paper doesn't result in a state-of-the-art model. However, with proper tuning, it achieves a near-state-of-the-art result in a fraction of the number of compute cycles. This makes it much more practical to train, and is what the author has termed "superconvergence".
