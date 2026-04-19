---
title: "Troubleshooting with Git Bisect"
date: 2024-07-02
tags:
    - Git
    - Bisect
    - Debugging
author: Irodotos Apostolou
---

## Setting the scene

Have you ever faced a bug in your codebase and thought:

> “This was working before… what changed?”

When working on any software project, encountering bugs is inevitable. Recently, while enhancing Android support for the Mill(https://github.com/com-lihaoyi/mill) build tool, our team faced an issue: our screenshot tests suddenly stopped working after a series of commits. Identifying the piece of code that causes an issue often translates to hours of debugging, running the code in various states, and observing its behaviour.

Luckily, if you know that a particular piece of code was working at some point, you can avoid diving into the code internals and identify the moment in time when the issue was introduced. Git offers a powerful tool for exactly this kind of problem: **git bisect**.


## The Challenge: A Needle in a Haystack

After several commits were merged into our development branch for Mill's Android support, we noticed that our screenshot tests were consistently failing. Screenshot tests are helpful in detecting regressions associated with the User Interface. We knew when we first introduced them that they were working. At some point they started failing consistently. Due to various reasons (including flaky tests) we haven't noticed for some time!


## Enter git bisect: Your Commit History Detective

We needed an approach to find the exact commit that started the failures! This is where git, with its bisect tool, saves the day!. It’s a Git command that performs an automated binary search through your project's commit history. You tell it a "bad" commit where the bug is present and a "good" commit where the bug is absent. git bisect then repeatedly checks out a commit halfway between the known good and bad points, and you simply test that version and tell Git whether it's "good" or "bad." This process halves the search space with each step, quickly isolating the first commit that introduced the issue.


## Our git bisect Journey with Mill

To find the commit that broke our Android screenshot tests in the Mill project, we followed these steps:


1) We initiated the bisecting process using git bisect start.

```bash
git bisect start
```


2) We identified the current commit (HEAD) as "bad" since the screenshot tests were failing. In our case, we determined whether a commit was "good" or "bad" based on whether the screenshot tests were running properly.

```bash
git bisect bad HEAD
```


3) We then found an older commit hash where we were certain the screenshot tests were passing and marked it as "good."

```bash
git bisect good 916628b
```

Git then automatically checked out a commit roughly in the middle of this range.


At each step, we rebuilt the relevant part of the Mill project and ran the Android screenshot tests.


If the tests passed, we informed Git with 

```bash
git bisect good
```

If they failed, we used 

```bash
git bisect bad
```

We continued narrowing the range, checking out new commits for us to test.


This iterative process continued, with git bisect guiding us through the commit history.


## Let's go through an example with numbers to understand how git bisect works

In a scenario where we have commit_1 to commit_50, and the problem is on commit_23 we start with 

```bash
git bisect good commit_1
```

```bash
git bisect bad commit_50
```

![alt text](/images/GitBisect1.png)

Now Git will checkout at the commit_25 and it will ask if this is a "good" or "bad" commit. Why commit_25? Beacuse (50+1)/2 = 25.5. We always rounding to the lower number, so the final answer is 25.  In this scenario, commit_25 is bad, because the screenshot tests are failing, so we need to run

```bash
git bisect bad
```

![alt text](/images/GitBisect2.png)

Now git will go to commit_13. Why? (25+1)/2 = 13. In this scenario, commit_13 is good, because the screenshot tests are running properly, so we run

```bash
git bisect good
```

![alt text](/images/GitBisect3.png)

Git now will go to commit_22 because (19+25)/2 = 22. Again, 22 is a  good commit 

```bash
git bisect good
```

![alt text](/images/GitBisect4.png)

Now git goes to commit_23 because (22+25)/2 = 23.5, but we always get the lower rounding, so it's 23.

commit_23 is a bad commit because now the screenshot tests are failing

```bash
git bisect bad
```

![alt text](/images/GitBisect5.png)

There are no other commits to check because commit_23 is "bad" and we know that commit_22 is "good", so the git bisect is done, and Git will tell us that commit_23 was the last "bad" commit we had


## The Revelation and the Fix

After a few iterations with git bisect, we pinpointed the exact commit that introduced the failure in our screenshot tests. Upon inspecting the changes in this specific commit, we quickly identified the problematic code.


def testEnableWorkStealing: T[Boolean] = T(true)

Screenshot tests cannot run in parallel like the other tests


Once the problematic change was reverted, we re-ran the screenshot tests, and they passed successfully. The fix was then incorporated into a pull request, which you can see here: https://github.com/com-lihaoyi/mill/pull/4979


## Back on Track Thanks to git bisect

The git bisect command proved invaluable in this scenario, saving us significant time and effort. Instead of a manual and potentially lengthy search, we were able to efficiently and systematically identify the root cause of the regression in Mill's Android screenshot testing. Using binary search we found the problem is log(N) steps, easily and fast


If you ever find yourself wondering which commit

introduced a bug into your codebase, remember git bisect. It’s a powerful ally in your debugging toolkit. For more detailed information on its usage, refer to the official documentation