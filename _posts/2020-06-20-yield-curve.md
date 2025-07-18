---
title: "Visualizing changes to the Yield Curve in 3D"
date: 2020-06-20
permalink: /posts/2020/06/yield-curve/
excerpt: A Shiny web application to visualize Treasury Yield Curve rates as a 3D surface.<br/><img src='/images/yield-curve.PNG'>
tags:
  - data visualization
  - economics
---

For better or worse, covering changes to the shape of the US Treasury *yield curve*
has become a persistent feature of economics journalism. The [Shiny](https://shiny.posit.co/) 
app below three dimensional visualizations of the yield curve over time.

<iframe src="https://davis-berlind.shinyapps.io/treasury-yield/" width="100%" height="725"></iframe><br/>

Before diving into the concept of [yield](https://www.investopedia.com/terms/y/yield.asp),
we need to understand a few things about [bonds](https://www.investopedia.com/terms/b/bond.asp). 
When the US government wants to borrow money, the [Treasury](https://en.wikipedia.org/wiki/United_States_Department_of_the_Treasury)
issues debt. This debt may take the form of a Treasury Bill, Note, or Bond depending on its 
[term to maturity](https://www.investopedia.com/terms/t/termtomaturity.asp), i.e. the length of
time the government is asking to borrow money for, and collectively these kind of debt instruments
are called *Treasuries*.

When purchasing Treasuries, one has to keep track of the [par value](https://www.investopedia.com/terms/p/parvalue.asp),
the [coupon rate](https://www.investopedia.com/terms/c/coupon-rate.asp),
and the price. The par value is the underlying value of the bond, i.e. what the government owes
you. If you buy a ten year Treasury Note with a \\$1,000 par value, then at the end of those ten
years the government will pay you back \\$1,000. The coupon rate is what we might commonly call
the interest rate. Every year you hold the bond, the government will pay you an additional sum
equal to the coupon rate times the par value. So continuing the previous example, if you buy a 
ten year Treasury Note with a \\$1,000 par value and a five percent annual coupon, then once
every year (until the bond matures) the government will pay you \\$50 for lending them your
money.

However, when most Treasuries are purchased, whether through direct auctions by the government
or a secondary market, the price paid by the buyer is rarely equal to the par value. Instead,
Treasuries are bought and sold at at a *discount* or *premium* on the par value. For instance,
if the stock market is slumping and investors are searching for someplace safe to keep their money, 
they may turn to Treasuries, thereby driving up demand and increasing the price above par. 
For example, You might be willing to pay \\$1,100 for a \\$1,000 par value bond based on the 
security it gives you, i.e. you are paying a \\$100 premium. This is where the concept of yield 
comes in. The yield is simply the annual coupon payment divided by the market price of the bond. 
So if you purchased a ten year Treasury Note with a \\$1,000 par value and a five percent annual 
coupon for \\$1,100, then your yield would be $\frac{0.05 \times 1000}{1100} = 0.0455$; in other 
words, the yield would be 4.55%.

The yield curve simply comes from plotting the bond yields of Treasuries at each maturity term
length. Typically, the yield curve is upward sloping, indicating that the longer you
are willing to lend out your money, i.e. the more [interest rate risk](https://www.investopedia.com/terms/i/interestraterisk.asp)
you are willing to take on, the more you will be paid.[^1] In rare cases, short-term yields 
can actually rise above long-term yields, in which case the curve is said to be *inverted*. 
[Campbell Harvey](https://en.wikipedia.org/wiki/Campbell_Harvey)'s 1986 dissertation linked 
inversions of the curve to near-term economic downturns, and in the intervening years the 
inverted yield curve has demonstrated remarkable accuracy for predicting recessions.

> The ten-year/two-year Treasury spread is one of the most reliable leading indicators of 
> recession within the following year. For as long as the Fed has published this data back to 1976, 
> it has accurately predicted every declared recession in the U.S., and not given a single false
> positive signal.[^2]

Even the current 2020 COVID-19 recession was preceded by a yield curve inversion in August 2019
(most will point to this as a technicality, but it's a perfect record nonetheless). Talking about a
curves inverting is an inherently visual prospect. I got tired of grabbing yield data from the
Treasury's [website](https://www.treasury.gov/resource-center/data-chart-center/interest-rates)
and manually making plots every time someone in the news rang the yield curve emergency bell,
so I built a shiny app to scrape the data and plot it for me in a way that is interactive and 
easily lets you see the trends in the curve over time. Here's the [link](https://davis-berlind.shinyapps.io/treasury-yield/)
for direct access to the Shiny app. The [source code](https://github.com/davis-berlind/treasury-yield)
is also available on my [GitHub](https://github.com/davis-berlind).

[^1]: In addition to being upward sloping, under normal assumptions the yield curve is concave, indicating decreasing marginal returns to assuming more credit risk.
[^2]: [Jime Chappelow, Investopedia](https://www.investopedia.com/terms/i/invertedyieldcurve.asp)
