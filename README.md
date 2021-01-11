Let's use this example from `git rebase --help`.
This is what happens when you run `git checkout topic` followed by `git rebase master`

       Assume the following history exists and the current branch is "topic":

                     A---B---C topic
                    /
               D---E---F---G master

       From this point, the result of either of the following commands:

           git rebase master
           git rebase master topic

       would be:

                             A'--B'--C' topic
                            /
               D---E---F---G master


NOTE:
       If the upstream branch already contains a change you have made
       (e.g., because you mailed a patch which was applied upstream),
       then that commit will be skipped. For example, running git rebase
       master on the following history (in which A' and A introduce the
       same set of changes, but have different committer information):

                     A---B---C topic
                    /
               D---E---A'---F master

       will result in:

                              B'---C' topic
                             /
               D---E---A'---F master

As a test, I created this repository and added these commits and branches to see the effects of rebase and merge.

```
m1(1)----------------m2(4)-----------------m3(7)     #main  (with m1, m2, m3. global change: 1, 4, 7)
  \----w1(2)--w2(3)----------w3(5)--w4(6)            #wua   (with m1, w1, w2, w3, w4. global change: 1, 2, 3, 5, 6)

main branch: m1, m2, m3
wua  branch: m1, w1, w2, w3, w4
```

Here's the results of various merge and rebase test:

```
# Rebase
Result 1: m1-w1-w2-w3-w4-m2-m3  #main (git checkout main; git rebase wua)  (change order: wua changes followed by main changes)
Result 2: m1-m2-m3-w1-w2-w3-w4  #wua  (git checkout wua;  git rebase main) (change order: main changes followed by wua changes)

# Merge
Result 3: m1-w1-w2-m2-w3-w4-m3  #main (git checkout main; git merge wua)   (chronological change order: 1,2,3,4,5,6,7)
Result 4: m1-w1-w2-m2-w3-w4-m3  #wua  (git checkout wua;  git merge main)  (chronological change order: 1,2,3,4,5,6,7)
```

Result 2 looks useful for customizing someone's repository with my own changes, while allowing myself to download the latest changes from the original repository.
I like the fact that changes are arranged in this order:  main commits + my commits.

In other words, my changes are always at the top of the change history.
For example, my branch may evolve like this, with main changes (m1, m2, ..., mM) listed first, followed by my own changes (w1, w2, ..., wN)

    Day1: m1, m2, w1, w2
    Day2: m1, m2, m3, w1, w2, w3, w4
    Day3: m1, m2, m3, m4, m5, m6, w1, w2, w3, w4
