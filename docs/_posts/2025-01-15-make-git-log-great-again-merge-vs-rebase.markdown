---
layout: post
title:  "Make Git Log Great (Again): Merge vs Rebase"
date:   2025-01-15
tags: [softwaredevelopment,versioncontrol,git]
---
A well-maintained Git log is more than just a record of commits—it’s a story of your project’s growth, collaboration, and problem-solving. But as projects scale and teams grow, Git logs can become cluttered, making it harder to trace changes, understand context, or troubleshoot issues.

In this post, we’ll explore strategies to make your Git log more readable, structured, and useful—because a great Git log isn’t just good practice; it’s a competitive advantage.

## Simple Merging

Let's kick off with a simple merging strategy, the one we are all probably most familiar with. For simplification purposes, let's assume we have one main branch that represents the current development branch. Every team member creates their own branch when they want to work on a specific feature and continues working on that feature branch until it is finished. Once it's done, they merge their feature branch into the main branch, and all their work is integrated into the main branch. Then, they can move on to the next feature. Below is a small drawing that represents the described behavior.

## [![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgrX_j-akpPnBA2j0oiTVPdLjpsu0noa551ZJC1QM39zubuE2-aDHulfGYJMcEYd8w8eI3QtG8rUsdm7X69TV4BHtUp73cz-wZ-xbytqtwXZA0Q4Twq07_cg5ZZmvbVgfRzS58sfpq0DDTdG3_O8H3AO8cz01K12YxY9Bjkb98SRvOxv_QaOrliFGVhDByQ/w331-h400/Picture4.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgrX_j-akpPnBA2j0oiTVPdLjpsu0noa551ZJC1QM39zubuE2-aDHulfGYJMcEYd8w8eI3QtG8rUsdm7X69TV4BHtUp73cz-wZ-xbytqtwXZA0Q4Twq07_cg5ZZmvbVgfRzS58sfpq0DDTdG3_O8H3AO8cz01K12YxY9Bjkb98SRvOxv_QaOrliFGVhDByQ/s987/Picture4.png)

  

### What Happens When a Feature Branch Takes Longer to Implement?

Here comes the first problem: what if our feature branches are not short and need to be implemented over a longer period of time? In this case, we risk our feature branch becoming outdated compared to the main branch.

We can solve this problem by more frequently merging changes from the main branch into our feature branch. This keeps our branch updated with the latest changes from the main branch and helps avoid the risk of encountering a lot of conflicts when we eventually merge the feature branch back into the main branch. A small illustration of this concept can be found in the image below.

  

[![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhU78ltICQhKo9ocY5Orjpz2_N6ngRpirIXC5XaRut9rqewomM8R07jJrH68rM2Dns7Br8Z_hS40mS45qhH6qd2M7L8bbf8x30q2YK5l-FT3qftJa7AnxKqNJfRCRuJt0rG7JyxPz-d-WvUgZHETzYemEyArrIi-Ut0gaKizkcc2D87bd2ocFJ-gYjwjeTy/w315-h400/Picture1.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhU78ltICQhKo9ocY5Orjpz2_N6ngRpirIXC5XaRut9rqewomM8R07jJrH68rM2Dns7Br8Z_hS40mS45qhH6qd2M7L8bbf8x30q2YK5l-FT3qftJa7AnxKqNJfRCRuJt0rG7JyxPz-d-WvUgZHETzYemEyArrIi-Ut0gaKizkcc2D87bd2ocFJ-gYjwjeTy/s1077/Picture1.png)

  

On one hand, we have reduced the likelihood of larger conflicts when merging our feature branch into the main branch. However, on the other hand, we are making our Git log harder to read and introducing tightly coupled commits, which makes reverting changes more challenging—and in real-world scenarios, almost impossible. The problem becomes even worse as the team grows and feature branches take longer to complete.

## Another Approach: Simple Rebase

Now, let us take a look at the same example, but this time using rebasing instead of merging. This means that when we need to update the feature branch with the current main branch, we rebase the feature branch onto the current main. Similarly, when we want to update the current main branch with our feature branch, we rebase the main branch onto the rebased feature branch. Below is an image illustrating this example in contrast to the merging approach.

  

[![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgVNoAuM4DoyvsAnq_aB5rixiSztHwh_TjOy1gAItjvbEF18xMeyjIZmSswSYe5sDAyNw_zbACo1mCNExslbYNIU-WYYet-pMQWiEMv7ec3Ilh9rIp4sz4wxopbGnO-4n8ckTmjXwNVGBV6wCZpEVqq1HT_zWrLbPRYtjTvZE7r0xqWI-xYTZsFulmmFe81/w640-h328/Picture2.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgVNoAuM4DoyvsAnq_aB5rixiSztHwh_TjOy1gAItjvbEF18xMeyjIZmSswSYe5sDAyNw_zbACo1mCNExslbYNIU-WYYet-pMQWiEMv7ec3Ilh9rIp4sz4wxopbGnO-4n8ckTmjXwNVGBV6wCZpEVqq1HT_zWrLbPRYtjTvZE7r0xqWI-xYTZsFulmmFe81/s1971/Picture2.png)
  

Here, we can see that the Git log becomes much more readable and easier to track when features are integrated into the main branch. In this example, we have displayed just one feature branch and a simplified main branch for illustration purposes, but this approach can also be applied to much more complex scenarios.

  

### Can it be improved?

Yes, we can further improve the example above by simply squashing the feature branch into a single commit before including its changes in the main branch. For illustration purposes, we will display only the last two steps.

  

  

[![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgq-BjkaRpXTfVZsRUobjtn_dW-wepQqJKNoTGl2sBrJqHidzhyNWeU1PUyKcJbYO5G5wshyphenhyphenxLu1rKlmxOCEM45hEBq5gSXKSzNyfpQ2vYsEI1RtdtSVeYdokKBUilSkVAsyb_Wvim_rx-2qItI8DyLxzF74niTglNWayv4Uq-1agG9tEL6KwMMZod_G0r3/w640-h362/Picture3.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgq-BjkaRpXTfVZsRUobjtn_dW-wepQqJKNoTGl2sBrJqHidzhyNWeU1PUyKcJbYO5G5wshyphenhyphenxLu1rKlmxOCEM45hEBq5gSXKSzNyfpQ2vYsEI1RtdtSVeYdokKBUilSkVAsyb_Wvim_rx-2qItI8DyLxzF74niTglNWayv4Uq-1agG9tEL6KwMMZod_G0r3/s1714/Picture3.png)

  

  

With this final step, we’ve streamlined our feature branches into a single commit in the Git log. This approach not only condenses the feature development into a neat, single entry, but also transforms the Git log into a clean, chronological timeline. By simplifying the development history, the log becomes much easier to follow, maintain, and understand—especially when navigating through complex projects. Moreover, with fewer commits to manage, it’s much simpler to identify changes, isolate specific updates, and, if needed, revert them with less risk of unintended consequences. This results in a more organized and manageable Git history, which ultimately benefits both individuals and teams working on the project.