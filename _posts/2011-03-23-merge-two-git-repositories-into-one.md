---
title: Merge Two Git Repositories Into One
date: 2011-03-23 20:14:27 -04:00
tags:
- git
meta:
permalink: "/2011/03/23/merge-two-git-repositories-into-one/"
---
<p>A few weeks ago I <a href="https://twitter.com/jasonkarns/status/45888163079663616">tweeted</a>: "Just did 2 subtree merges in order to combine 2 partially-related git repos into a single repo and still maintain history. #gitrocks" Wanna learn how to do it? Here we go...</p>
<h2>TL;DR</h2>
<pre class="sh"><code># create new project as the parent
$ mkdir new_parent_project
$ cd new_parent_project
$ git init
$ touch .gitignore
$ git ci -am "initial commit"

# merge project A into subdirectory A
$ git remote add -f projA /path/to/projA
$ git merge -s ours --no-commit projA/master
$ git read-tree --prefix=subdirA/ -u projA/master
$ git ci -m "merging projA into subdirA"

# merge project B into subdirectory B
$ git remote add -f projB /path/to/projB
$ git merge -s ours --no-commit projB/master
$ git read-tree --prefix=subdirB/ -u projB/master
$ git ci -m "merging projB into subdirB"
</code></pre>
<p>The most common use case for sub-tree merges, that I'm aware of, is to merge another git repository into a subdirectory in an existing repository. There are quite a few tutorials which cover these steps. In fact, the second two sets of commands above do exactly that. However, I discovered that in order to merge two repositories into a new repository, the new repository must already have a prior commit in it. Otherwise, the sub-tree merges will not work as planned. So, as in the first set of commands above, be sure to create at least one initial commit prior to doing the sub-tree merges. It can be as trivial as committing an empty file. But without it, the merges will not work correctly. Armed with this knowledge, you can follow the <a href="http://help.github.com/subtree-merge">tutorial</a> on GitHub on merging sub-trees. Or you can follow along with me here.</p>
<h3>Create Parent Repo</h3>
<p>First, create a new, empty project to act as the parent project for our two existing repositories.</p>
<pre class="sh"><code>Jason@BRUTUS ~/dev
$ mkdir parent

Jason@BRUTUS ~/dev
$ cd parent/

Jason@BRUTUS ~/dev/parent
$ git init
Initialized empty Git repository in ~/dev/parent/.git/
</code></pre>
<p>Now we need to create the initial commit. This is essential.</p>
<pre class="sh"><code>Jason@BRUTUS ~/dev/parent (master #)
$ touch .gitignore

Jason@BRUTUS ~/dev/parent (master #)
$ git ci -am "initial commit"
[master (root-commit) fc6f5ad] initial commit
 0 files changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 .gitignore
</code></pre>
<h3>Merge Project A Into Subdirectory</h3>
<p>Next, we add a remote to the first project we'd like to import. We'll give the remote a name (<var>projectA</var>) and pass the <kbd>-f</kbd> option so that it will fetch the contents of this remote immediately.</p>
<pre class="sh"><code>Jason@BRUTUS ~/dev/parent (master)
$ git remote add -f projectA /path/to/projectA
Updating projectA
warning: no common commits
remote: Counting objects: 16, done.
remote: Compressing objects: 100% (16/16), done.
remote: Total 16 (delta 7), reused 0 (delta 0)
Unpacking objects: 100% (16/16), done.
From /path/to/projectA
 * [new branch]      master     -&gt; projectA/master
</code></pre>
<p>Now, let's run a merge but not commit the result (<kbd>--no-commit</kbd> flag). We also need to specify the merge strategy <var>ours</var> with the <kbd>-s</kbd> switch.</p>
<pre class="sh"><code>Jason@BRUTUS ~/dev/parent (master)
$ git merge -s ours --no-commit projectA/master
Automatic merge went well; stopped before committing as requested
</code></pre>
<p>Now that we are in merging mode, we'll read in the tree from the remote, taking care to provide a subdirectory into which the subproject will go. This is specified with with <kbd>--prefix</kbd> switch. Also, add the <kbd>-u</kbd> flag to update the working tree with our changes.</p>
<pre class="sh"><code>Jason@BRUTUS ~/dev/parent (master|MERGING)
$ git read-tree --prefix=projA/ -u projectA/master
</code></pre>
<p>The remote has been merged into its own subdirectory and the changes have been staged. Now we can simply commit them.</p>
<pre class="sh"><code>Jason@BRUTUS ~/dev/parent (master +|MERGING)
$ git ci -m "merging project A into subdirectory"
[master 4d2d50d] merging project A into subdirectory
</code></pre>
<h3>Merge Project B Into Subdirectory</h3>
<p>At this point, we have Project A merged into its own subdirectory within our new parent project. Merging in Project B uses the same simple steps as above.</p>
<pre class="sh"><code>Jason@BRUTUS ~/dev/parent (master)
$ git remote add -f projectB /path/to/projectB
Updating projectB
warning: no common commits
remote: Counting objects: 47, done.
remote: Compressing objects: 100% (47/47), done.
remote: Total 47 (delta 23), reused 0 (delta 0)
Unpacking objects: 100% (47/47), done.
From /path/to/projectB
 * [new branch]      master     -&gt; projectB/master

Jason@BRUTUS ~/dev/parent (master)
$ git merge -s ours --no-commit projectB/master
Automatic merge went well; stopped before committing as requested

Jason@BRUTUS ~/dev/parent (master|MERGING)
$ git read-tree --prefix=projB/ -u projectB/master

Jason@BRUTUS ~/dev/parent (master +|MERGING)
$ git ci -m "merging project B into subdirectory"
[master 8f41792] merging project B into subdirectory
</code></pre>
<h3>Pulling In Updates</h3>
<p>If the original repositories (Projects A and B in this example) continue to live on elsewhere as separate projects, you can easily pull in updates to your new parent repo. Using the sub-tree merge strategy, the updates will be applied properly to the applicable subdirectory.</p>
<pre class="sh"><code>Jason@BRUTUS ~/dev/parent (master)
$ git pull -s subtree projectA master
</code></pre>
<p>However, if you no longer have any need for the original repositories, they can be deleted and the remotes in your new parent project can safely be removed.</p>
<p>Below is a screenshot of a repository after two sub-tree merges. The repositories that I merged were two separate userstyles: one for using tab color for notifications (gtab) and another for adding S/MIME icons to gmail's inbox (gmail). Each of these two projects have their own history that was maintained after the merge. Now they are each in their own subdirectory in a common <em>userstyles</em> git repository. As you can see, the two projects each have their own lines of development that do not include any common ancestry until the merge point.</p>
<p><a href="https://jasonkarns.files.wordpress.com/2020/02/cd4c2-merging.png"><img src="{{ site.baseurl }}/assets/2011/03/cd4c2-merging.png" alt="" title="GitK After Subtree Merge" width="545" height="331" class="alignnone size-full wp-image-187" /></a></p>
