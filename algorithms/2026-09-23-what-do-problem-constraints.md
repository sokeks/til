# TIL: What Do Problem Constraints Tell You?

## What issue did I encounter?

While doing [1995. Count Special Quadruplets](https://leetcode.com/problems/count-special-quadruplets/) I spotted that task's Constraints can bring you quite useful information, beyond what I was usually used to.

## My previous mental model

My usual approach was that task's Constraints tell you basically what are the expected range of values, therefore if you can safely add/multiply those. That's why you can see usually something like and know

```
Constraints:

* 3 <= nums.length <= 3000
* -10e5 <= nums[i] <= 10e5
```

And I thought that's it!

## What I learned

However, in the aforementioned task, I spotted these unusual constraints:

```
Constraints:

* 4 <= nums.length <= 50
* 1 <= nums[i] <= 100
```

And what does it tell us? If the input arrays is so small, that means that potentially highly time-demanding algorithms are suitable for a solution. For example, in this particular task, thanks to that I could go up with much higher time complexities, up to $$O(n^4)$$. As for an array of length equal to 50, and this time complexity we get at maximum 6,250,000 operations (however in this case, thanks to condition of a < b < c < d, we end up having $$\binom{n}{4} \approx \frac{n^4}{24}$$ giving us ~230 000 operations).


and write something as below:

``` Python
    def countQuadruplets(self, nums: list[int]) -> int:
        distinct_quadruplets_count = 0
        for a in range(len(nums)):
            for b in range(a + 1, len(nums)):
                for c in range(b + 1, len(nums)):
                    for d in range (c + 1, len(nums)):
                        if nums[a] + nums[b] + nums[c] == nums[d]:
                            distinct_quadruplets_count += 1

        
        return distinct_quadruplets_count
```

So a constraint outlook is yet another input in our algorithm pick up and I recommend you listening to it.

## Notes

In general, we can optimize the time complexity to $$O(n^2)$$:

``` Python
    from collections import defaultdict

    def countQuadruplets(self, nums: list[int]) -> int:
    # version for O(n^2)
        distinct_quadruplets_count = 0
        sums_count = defaultdict(int)
        for c in range(2, len(nums)):
            b = c - 1
            for a in range(b):
                sums_count[nums[a] + nums[b]] += 1
            
            for d in range(c + 1, len(nums)):
                fitting_sum = nums[d] - nums[c]
                distinct_quadruplets_count += sums_count[fitting_sum]

        return distinct_quadruplets_count
```

Since $1 \le nums[i] \le 100$, the maximum possible sum of two elements is $200$. This means that our hash map keeps max ~200 entries, regardless of how large $N$ grows. So thanks to BOTH constraints, we achieve $O(n^2)$ time complexity and strictly $O(1)$ extra space!

---
