# AB_Testing
A/B Testing, Thompson Sampling, A/B-Thompson hybrid

## A/B Test
In the A/B testing approach, every alternative (e.g., ad type) is sent to the same number of users.
For instance, we have four variants of an ad with the probabilities of user clicks of 0.09, 0.11, 0.16, and 0.18.
If our "budget" is 10000 displays, then each variant is displayed to 2,500 users and the total number of clicks will be close to
$$2500 \cdot (0.09+0.11+0.16+0.18) = 1350$$
The problem with A/B tests is that:
* if we allocate too few users, the test is inconclusive;
* if we allocate too many users, we lose opportunities on the less-performing variants.

This approach uses only the final probability values (click rates) after the end of the test.

## Thompson Sampling
Ideally, we would need a smart system that is able to learn dynamically as it gets more data. This system should:
* explore the different alternatives when the sample size is too small and the results are not reliable;
* exploit the results when they start becoming reliable enough, by sending more and more traffic to the best-performing alternative.
Good news: such a system exists and is called Thompson Sampling.

To solve the problem that a single number doesn’t express the uncertainty associated with the estimate, Thompson Sampling proposes to use a full probability distribution rather than a single number. Once we have our distributions — one for each alternative — Thompson Sampling works by drawing a random number from each distribution. Then, the alternative associated with the highest number is shown to the user. The idea is that if the distributions express a high uncertainty, the outcome depends much on the chance. In other words, the less confidence in our belief, the more the system will explore different alternatives. On the contrary, as confidence increases, the system increasingly exploits the best-performing alternative.

<b>Algorithm:</b> For each new user, we must do the following.
1) Based on the current count of clicks and misses for each variant, get the corresponding Beta distribution.
2) Draw a number from each variant’s distribution obtained at step 1.
3) Show the user the variant associated with the highest number.
4) Update the counter with the outcome obtained on the current user (click or miss).

Thompson sampling has a considerably higher click rate that is closer to that of the most popular ad.
![trials_clicks_AB_Thompson](https://github.com/ysereda/AB_Testing/assets/4238839/226245c3-e94a-4576-94ce-6bd6af76a990)

## Hybrid A/B - Thompson
Now let us test a hybrid approach consisting of an equal number of $d$ displays of each variant (A/B testing stage) followed by Thompson Sampling. The hope is that there is some optimal $d$ value at which the click rate is maximized due to a more accurate estimate of click rates prior to Thompson Sampling.

To obtain statistically significant results, 100 tests were performed for each $d$ from 0 to 58. A 2.08% improvement in the mean click rate was achieved, from $1763.5$ at $d = 0$ (pure Thompson Sampling) to a maximum of $1800.2$ at $d = 20$. The minimal click rate across 100 tests has increased by 2.10% from 1760 to 1797, and the maximal click rate has grown by 2.04%, from 1767 to 1803.
![displays_clicks hybrid](https://github.com/ysereda/AB_Testing/assets/4238839/c6ef9569-3856-487f-be0b-ccd86314fd24)

Thus, the hybrid approach allows one to achieve an average click rate at its level for the most clickable variant.
