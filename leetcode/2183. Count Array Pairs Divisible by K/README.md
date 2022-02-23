# [2183. Count Array Pairs Divisible by K (Hard)](https://leetcode.com/problems/count-array-pairs-divisible-by-k/)

<p>Given a <strong>0-indexed</strong> integer array <code>nums</code> of length <code>n</code> and an integer <code>k</code>, return <em>the <strong>number of pairs</strong></em> <code>(i, j)</code> <em>such that:</em></p>

<ul>
	<li><code>0 &lt;= i &lt; j &lt;= n - 1</code> <em>and</em></li>
	<li><code>nums[i] * nums[j]</code> <em>is divisible by</em> <code>k</code>.</li>
</ul>

<p>&nbsp;</p>
<p><strong>Example 1:</strong></p>

<pre><strong>Input:</strong> nums = [1,2,3,4,5], k = 2
<strong>Output:</strong> 7
<strong>Explanation:</strong> 
The 7 pairs of indices whose corresponding products are divisible by 2 are
(0, 1), (0, 3), (1, 2), (1, 3), (1, 4), (2, 3), and (3, 4).
Their products are 2, 4, 6, 8, 10, 12, and 20 respectively.
Other pairs such as (0, 2) and (2, 4) have products 3 and 15 respectively, which are not divisible by 2.    
</pre>

<p><strong>Example 2:</strong></p>

<pre><strong>Input:</strong> nums = [1,2,3,4], k = 5
<strong>Output:</strong> 0
<strong>Explanation:</strong> There does not exist any pair of indices whose corresponding product is divisible by 5.
</pre>

<p>&nbsp;</p>
<p><strong>Constraints:</strong></p>

<ul>
	<li><code>1 &lt;= nums.length &lt;= 10<sup>5</sup></code></li>
	<li><code>1 &lt;= nums[i], k &lt;= 10<sup>5</sup></code></li>
</ul>


**Similar Questions**:
* [Check If Array Pairs Are Divisible by k (Medium)](https://leetcode.com/problems/check-if-array-pairs-are-divisible-by-k/)

## Solution 1.

```cpp
// OJ: https://leetcode.com/problems/count-array-pairs-divisible-by-k/
// Author: github.com/lzl124631x
// Time: O(N * log(M, K) + K * sqrt(K)) where `M` is the max number in `A`.
// Space: O(K)
// Ref: https://www.hackerearth.com/zh/problem/algorithm/pair-count-5/editorial/
const int maxN = 1e5+1;
int cnt[maxN] = {}, sum[maxN] = {};
class Solution {
public:
    long long countPairs(vector<int>& A, int k) {
        memset(cnt, 0, (k + 1) * sizeof(int));
        int N = A.size();
        for (int &n : A) {
            n = gcd(n, k); // remove factors that `k` doesn't have. Now `A[i] <= k` 
            cnt[n]++;
        }
        vector<int> div; // all the divisors of `k`
        for (int i = 1; i <= k; ++i) {
            if (k % i == 0) div.push_back(i);
        }
        for (int d : div) {
            sum[d] = 0;
            for (int i = d; i <= k; i += d) {
                sum[d] += cnt[i]; // `sum[d]` is the sum of `cnt[i]` where `cnt[i]` is multiple of `d`.
            }
        }
        long ans = 0;
        for (int n : A) {
            int r = k / n;
            ans += sum[r];
            if (n % r == 0) --ans; // If `A[i]` is divisible by `r`, `cnt[r]` counts `A[i]` in so we need to minus one.
        }
        return ans / 2;
    }
};
```

## Solution 2.

```cpp
// OJ: https://leetcode.com/problems/count-array-pairs-divisible-by-k/
// Author: github.com/lzl124631x
// Time: O(K + N * (log(min(M, K)) + sqrt(K))) where `M` is the max number in `A`.
//          It can be reduced to `O(log(min(M, K)) + sqrt(K))` if we take `O(sqrt(K))` time to compute all the divisors.
// Space: O(K)
class Solution {
public:
    long long countPairs(vector<int>& A, int k) {
        long long ans = 0;
        int cnt[100001] = {};
        vector<int> divs; // `divs` are all the divisors of `k`. E.g. `k = 12`, `divs = [1,12,2,6,3,4]`
        for (int i = 1; i <= k; ++i) {
            if (k % i == 0) divs.push_back(i);
        }
        for (int n : A) {
            n = gcd(n, k); // `n` covers `gcd(n, k)`, the other number must cover `k / gcd(n, k)`.
            for (int d : divs) {
                if (d % (k / n) == 0) ans += cnt[d]; // If `d` is multiple of `k / gcd(n, k)`, add `cnt[d]` to answer
            }
            cnt[n]++;
        }
        return ans;
    }
};
```