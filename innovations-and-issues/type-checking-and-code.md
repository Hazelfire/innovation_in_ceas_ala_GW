# Type-checking and code

This post summarises the theory that **Sam Nolan** has been thinking about in "type checking" economic models. By "type-checking", I mean the careful checking of assumptions done in some semi-automated fashion. Some of [these ideas have been implemented in my program pedant](https://forum.effectivealtruism.org/posts/xue4yQ5rn6iDsHdmM/pedant-a-type-checker-for-cost-effectiveness-analysis).

I'm excited about this approach as it allows for a deeper understanding and verification of the use of models. I've done this with GiveDirectly's models and found two missing assumptions, and I think it could possibly lead to finding bigger errors when using larger models. I also think this would help people learn and think about statistics and economic models better.

### Introduction (Dimensional Analysis)

Dimensional analysis, a mathematical technique, is a tool in physics and engineering that involves the study of physical quantities through their dimensions. The primary role of dimensional analysis is to validate the consistency of mathematical expressions by ensuring that dimensions are used validly. It allows us to ascertain if an equation is potentially physically meaningful by verifying that it respects the principles of homogeneity and scale invariance. The approach is equally valid for economics.

Scale invariance is the characteristic of physical equations remaining unchanged under transformations of scale or size. This property is deeply intertwined with dimensional analysis because a physically meaningful expression should remain valid regardless of the units in which measurements are taken. Essentially, if a model describes something real, it should hold true whether we are using meters or kilometers, years or months, USD or AUD etc.

To give an example, if you want to calcuate the speed of a runner, and the runner travels 4 meters in 2 seconds. Then a valid calculation for their speed is meters / seconds = speed, or 4 meters / 2 seconds = 2 meters seconds^-1.

You can check whether this is scale invariant by changing the units. For instance changing meters to centimeters. In this case, the runner has travelled 400 cm in 2 seconds. This results in a speed of 200 cm/s. This has the same meaning and can be converted to 2 meters/s. So this means that the formula is scale invariant.

What if we, in error, assumed that the formula to calculate speed was meters - seconds = speed? Well, we're not sure what the resulting units would be, but they would be a combination of meters and seconds. So lets call the units f(meters, seconds). Now, with a valid set of units, I would expect some sort of process to be able to convert from one set of units to another to exist. For instance, to convert meters / second to cm / second I simply multiply the number by 100, and this always works. We can prove that in this case, no conversion function for f(meters, seconds) to f(cm, seconds) exists.

In our first formula, we find that if we were to calculate speed using this incorrect formula, we would have 4 meters - 2 seconds = 2 f(meters, second).\
Now lets imagine that we do this again with the cms case. In this case, 400 cm - 2 seconds = 398 f(cm, seconds). So in this case, 2 f(meters, second) converts to 398 f(cm, seconds).

The problem shows up when we now try to find another set of meters and seconds formula that has the same outcome. Say, 5 meters - 3 seconds = 2  f(meters, second). However, if we were to convert this formula to cm, we would result in 500 meters - 3 seconds = 497 f(meters, seconds). So in this case 2 f(meters, second) converts to 497 f(meters, seconds).

Because a conversion function for a number shouldn't really know exactly how the number was constructed, this shows that the function doesn't exist. Because in the above two cases, the results are identical.

In other words, the function g(c, x, y, x', y') defines a conversion function that takes the output of a function, x and y as old units, and x' and y' as new units. In this case, we have found that g(2, meters, seconds, cm, seconds) = 398 = 497. Therefore ariving at a contradiction.

This is the basis that one can prove that a formula is dimensional. And I use this definition to extend dimensional analysis.

### **Why was this useful?**

You can use this for any formula, particularly ones that are long and complicated. I [applied it to GiveDirectly's Cost-Effectiveness Analysis](https://forum.effectivealtruism.org/posts/WmQwtYEajNDuPdyZx/type-checking-givewell-s-givedirectly-cost-effective) and identified two missing assumptions:

* The duration that the initial consumption is consumed in (years)
* The duration of the final year of consumption is (years)

The effect of a missing assumption is that it is assumed to be 1. For instance, it is assumed that for 1 year the person recieving a grant consumes a portion of it. But it could be that the grant is recieved in a different way, longer or shorter, changing the cost-effectiveness of the intervention.

### Extensions to Dimensional Analysis

Dimensional Analysis how it is first formulated didn't suite my needs because it didn't give a dimension to discount/interest rates.

Lets consider an interest rate. An interest rate intuitively has some form of time variable associated with it. For instance, to calculate compound interest, you can calculate P = I \* (1 + r) ^ t. Where P is the final amount of money, I is the initial, r is the interest rate and t is the amount of time. For now, I will consider the number 1 + r. For instance, if the interest is 4% per year, this numbers is 1.04.

This number has a time component to it, because we usually say it like "per year". Not only that, but when you take it to a power, such as (1 + r) ^ t, where t is in units time, the units seem to "cancel out" (become dimensionless).&#x20;

Also, this 1.04 can be converted to other units of time. For instance, you can convert it to months by taking it to the power of 1/12th (1.04 ^ (1/12)).\
Because it seems like a valid unit, I created a new unit for these. They are called **power units** to me. I represent discount rates as ^(1/T). The have some interesting properties:

* You can multiply power units together, and when you do, they do not change the unit.
* You cannot add power units together, even if they are the same.
* Taking a power unit to the power of another unit results in a power unit the same

The power units are something that I'm fairly sure exist and have no problems. This encouraged to be think about other extensions.

## Usages

### Model reusability & Comparability

One problem that GiveWell finds is that they need to compare very different interventions, and they may not be comparing exactly the same things every time (especially after making many adjustments on each charity). We need to make sure the adjustments made are the same, and all the numbers used in one model are included in others to keep consistency. One way that this could be done is to declare value in some complicated unit, and you can then verify if everything has been properly accounted for be checking that the units are correct in both calculations.

### Assumption tracking

Units are often very similar concepts to assumptions, and it could be possible to use dimensional analysis to track a list of assumptions used in the model to generate it's outputs.

## The Future

### Probability

Currently, one thing I'd be excited about is applying dimensional analysis to probability distributions. Particularly, I think it may give clues on what distributions to assign to different properties, and invalid operations that could be applied to distributions.

Although I haven't gotten that far, one example of a result is that you can't find the mean of a probability distribution of a power unit, because that would require summing up the individual components, and you can't add power units.&#x20;

Further, in poisson processes, elements in the distributions are given dimensions, which seem to indicate that a normal dimension has some similarities to an exponential distribution, and makes me think gamma distributions have a lot of properties I care about, but I'm not super sure.

### Decision Theory

Relating to probability theory, there are correct and incorrect ways to think about making decisions based on data that you have. For instance, [this conversation I wrote](https://observablehq.com/d/65750cabd92419a7) details the difference between bayesian and frequentist processes, and how bayesian math is more relevant to decision making.

I'd be curious about how I could use decision theory to prevent numbers from combining in ways you don't want them to, for instance, you can only take the expectation over states that are in the same decision. I'm also not sure how to represent this.

This becomes more of a problem in probabilistic models, where you may not be consistent about your judgement about how uncertain each paramater is. Having units would help with that.

### Sam's pet theory: Universally identifiable units

I have a pet theory that as you take units and make them more and more specific, they basically represent the same thing as the name of the parameter, and every parameter has it's own units. This would mean that for every unit there is only one value that correctly inhabits those units.

Obviously, this would require some strong extensions to dimensional analysis. One way of doing this would be to add vector like attributes to the dimensions. For instance, the baseline consumption + grant money would represent the addition of two vectors. They can be added because they have the same dimension (Money) AND the "grant money" variable is represents a transformation from a baseline state (how much money they already have) to the new state. The result is a money vector from 0 to how much money they have after the grant.



