#Issue Workflow

Take an issue from Jira, assign it to yourself, set it to 'In Progress'
Make a branch named after the Jira Issue (e.g. MXDAFITI-1 )
Work on it!
When you think it is finished, notify one of the deployers
Then It will be deployed to testing and will be tested
When the 'definition of done' is fullfilled and the QA has approved the branch, merge it to the trunk
Then a tag will be created and deployed to staging. A testorder will be placed to verify the build.
If everything went fine, the tag will be deployed to live and the branch should be deleted.


####The SVN tutorial can be found here:
  * [Basic merging](http://svnbook.red-bean.com/nightly/en/svn.branchmerge.basicmerging.html)
  * [Branch - Merge](http://svnbook.red-bean.com/nightly/en/svn.branchmerge.html)
  * [Complete SVN book](http://svnbook.red-bean.com/)

#Legend

[JIRA ISSUE] – name of JIRA issue, e.g. MXDAFITI-1  

#Create a new branch

You have to change the actual URL according to the your needs.

4.2 Version
```sh
$ svn copy http://svn.dafiti.com.mx/upgrade/4.2.0/trunk/  http://svn.dafiti.com.mx/upgrade/4.2.0/branches/MXDAFITI-# -m 'MXDAFITI-#: Created branch from trunk'
```

#Checkout the new branch

4.2 Version
```sh
$ svn co  http://svn.dafiti.com.mx/upgrade/4.2.0/branches/MXDAFITI-#
```

#Synchronize the local branch with the trunk

4.2 Version
```sh
$ svn merge -x -w -x -b -x --ignore-eol-style http://svn.dafiti.com.mx/upgrade/4.2.0/trunk/
$ svn ci -m 'MXDAFITI-#: merged from trunk'
```
**It is important to use -x -w -x -b -x --ignore-eol-style (ignore whitespace, ignore binary, ignore line-endings) in order to prevent merge conflicts.**


#Reintegrate the project branch into the trunk

First of all we have to switch the local working copy to the trunk

4.2 Version
```sh
$ svn switch http://svn.dafiti.com.mx/upgrade/4.2.0/trunk/
```
**It is VERY important, that the local working copy is up-to-date!**

Now reintegrate the branch into the trunk to which you've just switched

4.2 Version
```sh
$ svn merge --reintegrate -x -w -x -b -x --ignore-eol-style http://svn.dafiti.com.mx/upgrade/4.2.0/branches/MXDAFITI-#
$ svn revert bob/application/vendor/configs/_crontab
$ svn ci -m 'MXDAFITI-#: reintegrated branch BRANCH-DESCRIPTION'
# Committed revision 1234.
```


###Google Analytics Notes:'
>If your changes were important modifications (new mobile web launch, IT issues, marketing events), please make notes @ >Google Analytics regardignt this big/medium changes. Or ask to someone with access to do it.


Usually you do not use this branch again. E.g. after the end of a sprint the branch gets reintegrated into the trunk and then deleted or marked as finished. Bugfixes should be done in a bugfix branch named after the bug ticket - e.g. MXDAFITI-2. If the bug got fixed you must reintegrate this bugfix branch back into the trunk.

4.2 Version
```sh
$ svn rm http://svn.dafiti.com.mx/upgrade/4.2.0/branches/MXDAFITI-# -m 'MXDAFITI-#: removed branch after reintegration'
```
[Deleted branches are keept in SVN as part of the project, so even if they are deleted they can be brought back to life.](http://stackoverflow.com/questions/102472/subversion-branch-reintegration)

>**Warning:	Do not use the branch again!**
>It is not recommended to merge from a branch to the trunk after reintegration and the branch should be deleted after >reintegration.

>[Stackoverflow discussion regarding dead branches](http://stackoverflow.com/questions/102472/subversion-branch-reintegration)

>If you need to make changes to the branch after reintegration, you will have to create a new branch from trunk. Name it like the first branch with a postfix like e.g. '_bugfix'.

#Undoing a faulty commit

If you ever want to undo a commit you have to reverse merge from the target you commited to, to your working copy. From your working directory do the following:
```sh
$ svn merge -c -[FAULTY_REVISION_NUMBER] http://svn.dafiti.com.mx/dafiti/MXDAFITI/branches/MXDAFITI-#
$ svn ci -m '[JIRA ISSUE]: reverse merged to undo revision [FAULTY_REVISION_NUMBER]'
```

If you want to undo a range of revisions you can do the following:
```sh
$ svn merge -r [FAULTY_REVISION_NUMBER_START]:[FAULTY_REVISION_NUMBER_END]
$ svn ci -m '[JIRA ISSUE]: reverse merged to undo revision [FAULTY_REVISION_NUMBER]' http://svn.dafiti.com.mx/dafiti/MXDAFITI/branches/MXDAFITI-#
$ svn ci -m 'MXDAFITI-#: reverse merged to undo revisions [FAULTY_REVISION_NUMBER_START] to [FAULTY_REVISION_NUMBER_END]'
```

#Adding files to SVN in command line

If you want to add all files recursively from your current dir do the following:
```sh
$ svn add --force ./*
```

This will schedule all files that are not tracked by svn to be added to the repo in the next commit.
