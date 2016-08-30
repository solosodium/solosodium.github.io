---
layout: post
title: What Does Buy One Get One Half Off Means
published: true
---

So what does **Buy One Get One Half Off** really mean? This actually can be shown in very simple math. If you are not interested in the math, here is the short version: `IT MEANS AT LEAST 25% IN PROFIT`.

Before we start, let me put down a few logical **assumptions**:

>
* The selling price of any item $$i$$ constitutes of cost and profit, $$S_i = C_i + P_i$$
* Seller is selling any item $$i$$ with positive cost and for profit, so $$C_i > 0$$ and $$P_i > 0$$
* Seller is selling all items at the same profit margin (ratio of profit and cost) $$\alpha$$, $$\forall$$ item $$i$$, $$\alpha C_i = P_i$$, and $$\alpha > 0$$
* If a buyer buys two items with different prices, the buyer pays for the full price of the higher priced item
* With buy one get one half off, seller is still making money

With these assumptions, we can begin:

Let's take two items: item $$i$$ and item $$j$$, without loss of generality, let's assume item $$i$$ is at least as expensive as item $$j$$, $$S_i \geq S_j$$.

The price we are paying for these two items is $$S_i + \frac{1}{2}S_j$$, which is $$C_i + P_i + \frac{1}{2}(C_j + P_j)$$.

In order for the seller to make money, this amount should at least cover the cost of the two items, $$C_i + C_j$$.

So we have an inequality, $$S_i + \frac{1}{2}S_j \geq C_i + C_j$$, which is $$C_i + P_i + \frac{1}{2}(C_j + P_j) \geq C_i + C_j$$.

By eliminating and rearranging terms, we have $$P_i + \frac{1}{2}P_j \geq \frac{1}{2}C_j$$.

Remember that we made an assumption earlier that $$\forall$$ item $$i$$, $$\alpha C_i = P_i$$, so we can substitute $$C_j$$ with $$\frac{P_j}{\alpha}$$, and have $$P_i + \frac{1}{2}P_j \geq \frac{1}{2\alpha}P_j$$

Rearranging the terms once more, we have $$\alpha \geq \frac{1}{2\frac{P_i}{P_j} + 1}$$.

Because we assumed $$S_i \geq S_j$$, we have $$P_i \geq P_j$$, so $$\frac{P_i}{P_j} \geq 1$$, and the right hand side of the above inequality follows $$\frac{1}{2\frac{P_i}{P_j} + 1} \leq \frac{1}{3}$$.

As you can see that the maximum is achieved when $$P_i = P_j$$, which is $$S_i = S_j$$, which basically means we bought two identical items.

To make money in any given scenario, the seller should set the profit margin $$\alpha$$ to cover the worst case. So we reached the final conclusion:

>
$$\alpha \geq \frac{1}{3}$$. If we reconsider the concept of profit margin as the ratio of profit $$P_i$$ versus selling price $$S_i$$, we are looking at the minimum profit of `25%` out of the selling price.
