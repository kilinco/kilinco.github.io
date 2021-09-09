---
layout: post
title: Summed Area Table
categories: [blog]
tags: [algorithms, matrix sum query, technical questions]
excerpt_separator: <!--more-->
---

Suppose you have a very large matrix _A_ and you would like to calculate the sum of a submatrix that resides within _A_. How would you do that? 

It would be relatively straightforward, if you had do it just once. You would sum up all the elements in that submatrix which would be of _O(m*n)_ time complexity, where m is the number of rows and n is the number of columns in the submatrix. The question becomes a bit more complex if you have to query the sum of different submatrices multiple times. Unsurprisingly, such use-cases do come up in Computer Vision and Computer Graphics, and _O(m*n)_ time complexity can get incredibly disturbing.
<!--more-->
Perhaps on such a disturbed day in 1984, Frank Painter rose to the challenge and wondered if he could somehow preprocess matrix _A_ so that it would take constant time to retrieve the sum of any submatrix. The answer was yes. Not only the sum, but you can preprocess your matrix to efficiently retrieve any other statistics such as mean or standard deviation for its submatrices. When I first came across this problem, I got completely befuddled and my first thought was to calculate all of the sums for all the submatrices and store it by some index. Needless to say that would have been an astonishingly inefficient solution and Frank did not go that way. Now let's follow Frank's footsteps and ponder together...

If we take a step-back here, it is clear that we have to scan the matrix, make some computations and then store the results. What do we compute? Let's think of our input. In order to query the sum of a submatrix, we first need to define it. The submatrix can be defined by 4 arguments. Let's call them *x_low*, *x_high*, *y_low* and *y_high*. If we were to draw a straight line for each of them, the submatrix would be the rectangular area constrained by these 4 lines. The number of elements in this submatrix would be *(x_high - x_low + 1)* \* *(y_high - y_low + 1)*. Now suppose we can only query this interface for *(x,y)* which then would return the sum of the submatrix defined by *(0,0)* and *(x,y)*. How can we use this method to calculate the submatrix defined by *(x_low,y_low)* and *(x_high,y_high)*? The answer becomes apparent when you draw and visualize the problem, so I would highly recommend doing that.. :)

```cpp
// returns the sum of all elements in submatrix defined by x_low = 0, y_low = 0, x_high = x & y_high = y
int sum_submatrix(int x, int y); 

int sum_submatrix(int x_low, int y_low, int x_high, int y_high) {
    return sum_submatrix(x_high, y_high) - sum_submatrix(x_high, y_low-1) - sum_submatrix(x_low-1, y_high) + sum_submatrix(x_low-1, y_low-1); 
}
```
From our previous calculation, it looks like we only need to calculate the submatrix sums for all submatrices from *(0,0)* to *(x,y)*. Compared to storing the results for each and every submatrix, this would obviously be much more efficient. So how can we calculate the submatrix sum? 

At this point it is best to visualize the solution with a 1D array. Think of this 1D array as the first row of _A_ and we need to calculate all the submatrix sums from *(0,0)* to *(0,4)*.

```text
[1,5,1,6,8] ---> [1,6,7,13,21]
```

Notice the pattern in the 1D case? It all comes down to calculating the submatrix sum cumulatively. See the snippet below. Note that we subtract `dp[i-1][j-1]` to prevent it to be counted twice. 

```cpp
vector<vector<int>> dp;
void calculate_submatrix_sums(const vector<vector<int>> & A) {
    int M = A.size(), N = A[0].size();
    dp = vector<vector<int>> (M+1, vector<int>(N+1,0));
    for(int i = 1; i <= M; ++i)
        for(int j = 1; j <= N; ++j)
            dp[i][j] = dp[i-1][j] + dp[i][j-1] - dp[i-1][j-1] + A[i-1][j-1];
}
```

Thus, using this algorithm the sum of all elements in the submatrix can be calculated with the following snippet.

```cpp
int sum_submatrix(int x_low, int y_low, int x_high, int y_high) {
    return dp[x_high+1][y_high+1] - dp[x_high+1][y_low] - dp[x_low][y_high+1] + dp[x_low][y_low];
}
```

You can find the much better thought out and illustrated solution [here](https://nayan.co/blog/AI/Integral-Image/).

Lastly, it is incredibly important to remember that this algorithm can also be used to retrieve stastics of the submatrices.
