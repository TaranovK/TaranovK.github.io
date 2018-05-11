---
layout: post
comments: true
title: Understanding Gergonne's Card Trick - Python implementation and underlying mathematics
---

I have recently watched an amazing video from [Numberphile](http://www.numberphile.com/) about a card trick. The card trick in the video was a nice extension of [21 card trick](https://en.wikipedia.org/wiki/Twenty_One_Card_Trick), which I knew since I was a kid. 
To be honest, I was fascinated by how math was used in the trick to move a selected card to any chosen position in the deck of 27 cards. However, the explanation in the video of underlying mathematics was cursory and unconvincing. And because of that, I have decided to write this post and explain how the trick works and how it can be generalized to other deck sizes.


## 27 card trick a.k.a Gergonne's card trick

The original trick uses a selection of 27 cards out of a standard deck. The volunteer chooses one card at random and pick a number between 1 and 27. The cards are then dealt out face up in three columns of 9 cards each. The volunteer points to the pile containing their card. The cards are picked up and the process is repeated three times. Afterwards, the deck is dealt again and the selected cards appears at the position chosen by the volunteer. You can see the trick in action on the video below. 

<p align="center">
<iframe width="560" height="315" src="https://www.youtube.com/embed/l7lP9y7Bb5g?rel=0" frameborder="0" allow="autoplay; encrypted-media" allowfullscreen></iframe>
</p>

The secret of the trick is based on the base 3 number system.
The chosen number by the volunteer is centralized and converted from decimal (base 10) system, which we use in real life, to the base-three one. To centralize the number we just subtract 1 to have numbers between 0 and 26 instead of between 1 and 27. 
For instance, if the number 12 is picked then its centralized counterpart in base 3 would be \\(11_{10} = 102_3\\), where subscript stands for the numerical base.

After that, the magician should read the digits of \\(102_3\\) from right to left: 2, 0, and then 1. This numbers have special meanings:  2 = bottom, 1 = middle, and 0 = top, and they tell us in which order to place piles after each deal. In our example, after the first round the pile with the selected card goes to the bottom of a new deck, then to the top, and after the last round the pile goes to the middle. If you correctly do all this steps the card will end up at the chosen position.

At this point, you are probably able to reproduce the trick, but I bet you don't know why we used base 3 numerical system, why we had 27 cards, and why we need to read digits from right to left. In the next section I will try to explain the trick from mathematical point of view and introduce the generalization of it.


## Underlying mathematics

The trick can be generalized to  \\(t\\) rounds with \\(p\\) piles each round. For that we would need \\(p^t\\) cards in our deck, which makes the trick infeasible in real life for large number of rounds since the deck size grows exponentially. 


At the beginning, the volunteer can pick any card from the deck and it can be at any position. Let us denote the initial position of the selected card as \\(x_0\\). What do we already know about it? Clearly, the position of the card is limited by the deck size, thus \\(x_0 \in [0, p^t - 1]\\). Note that \\(x_0\\) is already centralized. 

Now I would like to understand the position of the card after the first round \\(x_1\\). Namely, I am going to express \\(x_1\\) as a function of the previous position \\(x_0\\) and the position of the selected pile \\(s_0\\) in a new deck. Since there are  \\(p\\)  piles, \\(s_0 \in [0, p -1] \\).


\begin{align\*}
   x_1 = f(x_0 , s_0 )
\end{align\*}

When we deal cards to \\(p\\) piles, the position of the  card in the selected pile will be equal to \\(x_0 \text{ div } p \\), where \\(\text{ div }\\) stands for [integer division](https://en.wikipedia.org/wiki/Division_(mathematics)#Of_integers). In Python the integer division is expressed by the operator \\( // \\). For instance, if the card's initial position is 10 and there are 3 piles, then the card will be in some pile (actually it will be the second pile, but we don't need to know it, since the volunteer is going to tell this information to us) at position \\(10 \text{ div } 3 = 3 \\). 

When the volunteer says us which pile contains the selected card we are going to put it after  \\(s_0\\) piles in the new deck. Since the size of each pile is \\(p^t / p = p^{t-1}\\), the selected card will be shifted by \\( s_0 p^{t-1} \\) cards. Overall, we can now write the position of the card after the first round as


\begin{align}
   x_1 = f(x_0 , s_0 ) = x_0 \text{ div } p + s_0 p^{t-1},
   \label{eq:x1}
\end{align}
where the first addend represents the position of card in the selected pile, and the second stands for the shift after picking up piles in the requested order. 


Similarly, we can express the position of the card after the second round as 

\begin{align}
   x_2 = f(x_1 , s_1 ) = x_1 \text{ div } p + s_1 p^{t-1}.
   \label{eq:x2}
\end{align}

Now by combining equations \\((\ref{eq:x1})\\) and  \\( (\ref{eq:x2}) \\), we get

\begin{align\*}
   x_2 &=   (x_0 \text{ div } p + s_0 p^{t-1}) \text{ div } p + s_1 p^{t-1} \\\\\\
   &= x_0 \text{ div } p^2 + s_0 p^{t-2} + s_1 p^{t-1}
\end{align\*}

We can already see the pattern in our equations and write the position of the card after repeating this procedure k times.

\begin{align\*}
   x_k = x_0 \text{ div } p^k + \sum_{i=0}^{k-1} s_i p^{t-k+i} 
\end{align\*}

Finally, the position of the card after all \\(t\\) rounds is 

\begin{align\*}
   x_t = x_0 \text{ div } p^t + \sum_{i=0}^{t-1} s_i p^{i}.
\end{align\*}

We can recall that \\(x_0 \in [0, p^t - 1]\\), thus \\(x_0 \text{ div } p^t = 0 \\). Therefore, the final position of the position of the card is

\begin{align}
   x_t = \sum_{i=0}^{t-1} s_i p^{i}.
  \label{eq:final}
\end{align}

As we can see from equation \\((\ref{eq:final})\\), the position of the card is independent from its initial position and is completely determined by the placement order of piles \\(s_i\\) at each step. What is more, the equation is the actual formula used to convert a number \\(\hat{s}\\) of base \\(p\\) to the decimal system. Namely, 
\begin{align\*}
   (x_t) _ { 10 } = \hat{s} _ {p},
\end{align\*}
where \\(\hat{s}\\) is built by concatenating the digits \\(s_i\\) from right to left.


## Generalized Gergonne's card trick

In this section I'd like to focus on how the trick can be extended given the results of the previous section. 
The number of piles  \\(p\\) represents the base of numerical system we need to use in our trick. 
In 27 card trick this number is 3, and that is why the selected number is converted to base 3 numerical system. You can easily try base 2, or 4, or even 7. The digits in the  base \\(p\\) representation should be read from right to left, and each digit means where in a new deck the pile with the selected card must go to. The digit stands for the  position of the selected pile starting from the top of the deck. The digit 0 means that the pile goes to the top, the digit 1 that it will the second one, and etc. The number of rounds \\(t\\)  says us how many digits we should have in our new numerical representation. Finally, the deck size must be equal to \\(p^t\\).



## Python implementation

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/TaranovK/card-trick-assets/master?filepath=card_trick.ipynb)

I have implemented the generalized version of the trick in Python. The Jupyter notebook can be found on [github](https://github.com/TaranovK/card-trick-assets). It is also compatible with [Binder](https://mybinder.org/) and can be immediately executed in your browser. To try it out you can use [this link](https://mybinder.org/v2/gh/TaranovK/card-trick-assets/master?filepath=card_trick.ipynb)

My implementation uses Unicode characters instead of cards, and can be configured for any number of rounds and piles. 

The code can be also used for practicing the conversion of numbers to another bases for the trick by using    <code class="python"> get_secret_order</code> function.
 
