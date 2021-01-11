
# Remote to Local (git pull = git fetch + git merge)
git fetch origin                                  # download changes of current branch from remote repository called origin
git merge origin/develop                          # commit commits in branch origin/develop to my current branch (which is usually main)

git pull develop                                  # Does both steps above (fetch develop branch from remote, and merge it with my current branch)

# Remote to Local (rebase instead of merge)
git fetch origin                                  # download changes from remote repository called origin
git rebase origin/develop develop                 # commit history becomes origin/develop > develop (i.e. develop on top of origin/develop)

git push origin -f                                # Note: when pushing a rebased branch, must use -f to force remote to update history with a rebased one.

e.g. 2 branches main and develop

m1 >    m2  >  m3 > m4
   \>d1   > d2

Merged  Master: m1 > d1 > m2 > d2 > m3 > m4   (chronological history of commits, commit timestamps are in order)
Rebased Master: m1 > m2 > m3 > m4 > d1 > d2   (APPEND topic changes to the HEAD of master changes)

Another way to look at this:

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

To rebase:  `git checkout topic` followed by `git rebase master`. When rebase exits topic will remain the checked-out branch.

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

My Test:

[BEFORE]

m1(1)----------------m2(4)-----------------m3(7)     #main  (with m1, m2, m3. global change: 1, 4, 7)
  \----w1(2)--w2(3)----------w3(5)--w4(6)            #wua   (with m1, w1, w2, w3, w4. global change: 1, 2,3,5,6)

main: m1, m2, m3
wua:  m1, w1, w2, w3, w4

[AFTER]

Result: m1-w1-w2-w3-w4-m2-m3  #main (git checkout main; git rebase wua) (change order: wua changes followed by main changes)

Result: m1-m2-m3-w1-w2-w3-w4  #wua (git checkout wua; git rebase main)  (change order: main changes followed by wua changes)

Result: m1-w1-w2-m2-w3-w4-m3  #main (git checkout main; git merge wua)  (chronological change order: 1,2,3,4,5,6,7)
Result: m1-w1-w2-m2-w3-w4-m3  #wua (git checkout wua; git merge main)   (chronological change order: 1,2,3,4,5,6,7)


CONCLUSION:

To fetch changes from Google's latest sample code, while customizing it, do this:

Create a bare remote repository
git clone $GOOGLE_SAMPLE_CODE
git branch wua


