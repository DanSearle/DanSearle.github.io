---
layout: post
title: "Debian Packages and Continuous Integration"
description: ""
category: 
tags: []
---
{% include JB/setup %}

Overview of Continous Integration
=================================

Software in use
---------------

 * Git - SCM
 * Jenkins - Continous integration server
 * git-buildpackage

Automating the Build
--------------------

Ensure the build can actually be fully built with no user intervention.
For this purpose I use bash scripts in the script/ directory.


Branching Model & Versioning
----------------------------

Picking a branching model and a versioning system to match is actually quite
an important step as it will define how many build configurations
are required in Jenkins. I initially decided to use the
[Successful Git Branching Model (nvie.com)](http://nvie.com/posts/a-successful-git-branching-model/)
however this does create a large amount of build configurations as I found 
myself configuring a job for:

 * Development branch
 * Current release branch
 * Master branch

I also included promotion processes for automatically merging and versioning
builds. Whilst this approach does work it does create a very large 
administration overhead were if something changes in your build you
have to update 3 different jobs in order to ensure that everything is kept in 
step, although this should not happen often.

Another problem I encountered is I was deploying the release branch build to a 
test server for testing in order to perform fixes and then merging to the master 
branch deploying to a staging server ready for testing again before final 
release.  With this approach there is not a lot of value gained in testing on 
two systems independently as only one release will be merged to the master so 
you may as well merge to master and perform fixes on the master branch. This
approach would not be good if there was an automated build process
which immediately deployed from master. I believe that there should be some 
final human intervention before the build gets published, especially if 
the build is a website which is live to customers, a package released to NuGet 
may not require such a stringent requirement to be tested on the target 
platform before release.

In light of this I have switched to the [GitHub flow (Scott Chacon)](http://scottchacon.com/2011/08/31/github-flow.html)
which focuses more on your master being the current release and will be 
continually deployed on feature and patch changes. Rather than creating an 
explicit release branch and waiting for it to be merged into master 
before release. Comparing this to the "Successful Git Branching Model" I agree
with Scott Chacon's article that it is too complex for most developments:

> It's complicated enough that a big [helper script](https://github.com/nvie/gitflow) 
> was developed to help enforce the flow.

I wont delve more into why I think this approach is better as I think it is 
summed up nicely in Scott Chacon's article.


[Symantic versioning with continous deployment (Ploeh Blog)](http://blog.ploeh.dk/2013/12/10/semantic-versioning-with-continuous-deployment/)

bumpversion

Building the Debian package
===========================

git-dch
git-buildpackage

Uploading to package server
---------------------------

Staging
-------

Deployment using Puppet
=======================
