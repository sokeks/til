# TIL: First ask a question

## What issue did I encounter?

I must admit, I had huge issues with [3871. Count Commas in Range II](https://leetcode.com/problems/count-commas-in-range-ii), that is to return the **total** number of commas used when writing all integers from [1, n] (inclusive) in **standard** number formatting. E.g. if n=1002, we have 3 commas for 1,000; 1,001; and 1,002. I got stuck by trying to solve the task without asking myself a question that would shape a model which, when implemented, would give me the right solution.

## My previous mental model

My flawed thought process was something like:

*ok, we need to know how many thousands are in the number, because each single thousand brings a comma (as in the example above) and if I **somehow** count them, I will get to the solution and it's a matter of adjusting the code to fit it.*

I hadn't formulated a question that would lead me to the proper solution, and consequently, I failed.

## What I learned

So what kind of a question could it be? For example, this one is quite good:

*How many numbers (from range [1, n]) have a thousand-related comma (so first comma), how many million-related comma (so second comma), and so on?*

Let's break it into pieces:

**Q1a:** How many numbers (from range [1, n]) have a thousand-related comma (so first comma)?<br>
**A1a:** All from 1,000 inclusive upwards &rarr; in mathematical terms: $$\sum_{x=1}^{n - 1000 + 1} 1 = n - 1000 + 1$$
<br><br>
**Q2a:** How many numbers (from range [1, n]) have a million-related comma (so second comma)?<br>
**A2a:** All from 1,000,000 inclusive upwards  &rarr; in mathematical terms: $$\sum_{x=1}^{n - 1000000 + 1} 1 = n - 1000000 + 1$$
<br><br>
**Q3a:** ...<br>
**A3a:** ...<br>
(You get it, what goes further.)

And if we want to represent it in the code, we get:

```Python
commas_count = 0
threshold = 1000

while n >= threshold:
  commas_count += n - threshold + 1
  threshold *= 1000
```
---
The above question is not the only one; another good approach is:

*How many numbers (from range [1, n]) have one comma, how many two commas, and so on?*

Breaking it apart:

**Q1b:** How many numbers (from range [1, n]) have exactly one comma?<br>
**A1b:** All from 1,000 inclusive to 1,000,000 exclusive &rarr; in mathematical terms: $$\sum_{x=1,000}^{min(n, 1,000,000 - 1)} 1 = min(n, (1,000,000 - 1)) - 1,000 + 1 = 999,000$$
<br><br>
**Q2b:** How many numbers (from range [1, n]) have exactly two commas?<br>
**A2b:** All from 1,000,000 inclusive upwards  &rarr; in mathematical terms: terms: $$\sum_{x=1,000,000}^{min(n, 1,000,000,000 - 1)} 1 = min(n, (1,000,000,000 - 1)) - 1,000,000 + 1 = 999,000,000$$
<br><br>

And in code:

```Python
commas_count = 0
lower = 1000
commas_in_number = 1

while lower <= n:
  commas_count += (min(n, lower * 1000 - 1) - lower + 1) * commas_in_number
  lower *= 1000
  commas_in_number += 1 
```

**Conclusion:**

Without a good question, it's really hard to find a good answer, not speaking of building a good model, or writing a good code. 

---
