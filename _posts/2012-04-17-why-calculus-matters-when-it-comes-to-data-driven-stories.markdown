---
title: Why calculus matters when it comes to data-driven stories
date: '2012-04-17T21:30:50.000+00:00'
categories:
- Blog
- Data Visualization Thoughts
tags:
- data visualization
- standard deviation
- z-value
---

<p>A quick refresher from my data visualization <a href="http://susanemcgregor.com/">professor</a> here at Columbia a couple of weeks ago reminded me why I was forced to spend all those grueling hours calculating standard deviation back in high school.</p>
<p>See, when you're using a data set to tell a story, the first step is to understand what that data says. And to do that, you've got to have a good idea of the range and variation of the values at hand. Not only can figuring that information out help you determine whether there's any statistical significance to your data set, but it can also pinpoint outliers and possible errors that may exist within the data before you begin the work of visualizing it.</p>
<p>Thanks to powerful processing programs like Excel, we can figure out the variability of data sets pretty easily using the program's built-in standard deviation function (remember <a href="http://standard-deviation.appspot.com/">this</a> intimidating-looking equation from calculus class?). Still, it always helps to know how to calculate the information out by hand, if only to get a conceptual idea of why numbers such as the standard deviation (the variability of a data-set) and the z-value (the number of standard deviations a given value is away from the mean) even matter in the first place when it comes to data visualization.</p>
<p>So, to brush up on my formulas and also better understand the numbers behind an <a href="http://carlvlewis.net/?p=2024">actual story assignment</a> for one of my classes, I recently hand-calculated the standard deviation and z-values for a set of data on state-by-state obesity rates. From my calculations, I was able to use the standard deviation (3.24) to determine that, on average, most states fell within the middle of the bell-curve for the average national obesity rate (27.1 percent) . In addition, the z-values helped me understand which states stood out from the pack as possible outliers (Mississippi is by far the most obese with a 2.13 z-value, Colorado the least obese with a -1.9 z-value). To get an idea of how those formulas look hand-calculated in Excel, check out my spreadsheet <a href="http://carlvlewis.net/Lewis_stateobesityrates_standarddev.xlsx">here</a>. And keep these formulas in mind while working on your next data story. They can potentially save you time and effort by helping you figure out what your data set says <em>before</em> you have to go through the often-lengthy process of visualizing it.</p>
