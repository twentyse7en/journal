# Issue with floating point, be careful!

## Issue
I was trying to convert [Rupee](https://en.wikipedia.org/wiki/Indian_rupee) to [Paisa](https://en.wikipedia.org/wiki/Indian_paisa).
Here is the issue, `146862.98 * 100 = 14686298.000000002`. From where did that `.000000002` came from?
But interestingly `146862.98 * 10 = 1468629.8`.   


I first thought it might be a issue with `javascript`, but later found out it's a issue with representing floating point in binary.
Found the same issue in `python` as well.

## Solution
For me I don't need the fractional part after converting to Rupee, all the calculation will be done without fractional part.

## Reference
- [Short Explanation](https://floating-point-gui.de/)
- [Detailed Explanation](https://docs.oracle.com/cd/E19957-01/806-3568/ncg_goldberg.html)
- [StackOverflow](https://stackoverflow.com/questions/588004/is-floating-point-math-broken)
