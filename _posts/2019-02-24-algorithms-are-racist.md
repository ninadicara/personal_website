---
layout: post
description: Explaining why maths isn't neutral
title: Yes, algorithms are racist (because we are)
date: 2019-02-24 15:34:30
---

A journalist tweeted this week that Alexandria Ocasio-Cortez (AOC) said that algorithms, driven by math, are racist. Something about the way the tweet was written struck me as sceptical, and many people in tech seemed to feel the same way. A lot of tech-experienced people weighed in to defend AOC, because if you have an understanding of how algorithms are made, and the existence of systemic racism, then you’ll understand that she’s right.

<blockquote class="twitter-tweet"><p lang="en" dir="ltr">Socialist Rep. Alexandria Ocasio-Cortez (D-NY) claims that algorithms, which are driven by math, are racist <a href="https://t.co/X2veVvAU1H">pic.twitter.com/X2veVvAU1H</a></p>&mdash; Ryan Saavedra (@RealSaavedra) <a href="https://twitter.com/RealSaavedra/status/1087627739861897216?ref_src=twsrc%5Etfw">January 22, 2019</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

Now, before we go into the algorithm part lets talk a little about institutional racism. We know that racism is very present in the criminal justice system, with people who are Black, or people of colour (POC), far more likely to be arrested than White people (over 3 times more likely). If you find that hard to swallow please note that it’s information displayed openly on the [.gov.uk website][1]. 

This is the result of racism in our society that plays out in a million microscopic ways every day. Wages, funding decisions, educational opportunities, access to pro-social activities, proximity to crime, who gets the best medical treatment and more, which disadvantages people who are black or from an ethic minority on an institutional scale.

Imagine…
if we were to create an algorithm that created a ‘risk score’ for people who were likely to get into crime, and use it to target interventions towards those who might need it. We need to give the computer a lot of information for it to decide what risk level to give each new person it will classify. The data might include criminal justice information (criminal history, arrests), age, location, ethnicity and more.

(I’m sure you can see where this is going.)

If we fed the algorithm the arrest data we have the algorithm will learn from it. What the algorithms will learn is that Black people are three times more likely to be arrested than White people. As a result, the algorithm will be more likely to classify Black people with a higher risk score than White people, just based on their ethnicity.

This doesn’t just work with race. If you happen to live in an area where there is a lot of violence, you may be assigned a higher risk score because all the algorithm can do is see a pattern and fit you into it. You live in a neighbourhood with a lot of crime… you are more likely to be involved in a crime.

By the way does this all sound like a completely unethical, terrifyingly dystopian experiment?

[Because it actually happened.][2]



You can see how everything about social inequality is amplified and reinforced by giving the algorithm data that teaches it bias, racism and prejudice. 

While the example above is about over-representation of Black people in the criminal justice system, there is also a huge problem with under-representation elsewhere. [An article in Nature in July 2018][3] discusses this issue very well, and gives the example that an algorithm recognises a white woman in a white wedding dress as a ‘Bride’ but, not a Indian woman dressed in a traditional colourful wedding sari.

This is because the ‘training data’ (the information the algorithm builds the patterns from) did not contain enough (or any) images of Indian brides to learn that they were brides too.

We get into more concerning territory when it comes to medical training data – another example from the Nature article shows that an algorithm was much less accurate at detecting skin cancer in dark-skinned people that it was in White people, because less than 5% of the original data was from POC.

I would wager that if you are Black or a person of colour you will not be surprised by this post. This information is unfortunately another extension of the same inequality that exists in day-to-day life. The people who are responsible for creating algorithms are usually White, male and highly-educated and largely unaware of their own societal privilege, working with lots of other people who look just like them, and who do not always notice the lack of diversity in their data either.

So there are three solutions that I can see. We need more diversity in computer science/maths, both ethnicity and gender-wise. There’s a huge movement for it which is great, but it’s never going to happen as fast as we want it to. Secondly, we need those who are currently responsible for creating these algorithms to take representation in training data seriously, and consider the real life applications of these algorithms in society. Lastly, companies who produce training data sets need to take responsibility for this too and acknowledge that they need to be sourcing more diverse information.

The thing is, if we got it right then maths could be helping us create the world we want to live in, rather than maintaining systems of white supremacy. 


[1]:  https://www.ethnicity-facts-figures.service.gov.uk/crime-justice-and-the-law/policing/number-of-arrests/latest
[2]: https://link.springer.com/article/10.1007%2Fs11292-016-9272-0
[3]: https://www.nature.com/articles/d41586-018-05707-8#ref-CR6
