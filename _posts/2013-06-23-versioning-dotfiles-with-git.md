---
title: Versioning Dotfiles with Git
date: 2013-06-23 11:14:03 -04:00
tags:
- git
meta:
  _wp_old_slug: using-git-for-your-home-directory
permalink: "/2013/06/23/versioning-dotfiles-with-git/"
---
<p>It's become quite common to see 'dotfiles' repositories across GitHub, and for good reason. Versioning your home directory is a great way to maintain backups of your dotfiles, share with others, learn from others, and makes configuring a new machine a bit easier. Many users, however, don't actually version their home directory. A common approach is to have a dotfiles repo somewhere (let's say <code>~/dotfiles</code>) and use a number of different ways to get the actual dot-files into the home directory itself: manually copy, manually symlink, or scripting the copy/symlink.</p>
<p>I'm not a fan of the copy approach. Any changes made to the actual dotfiles must to be manually replicated in the repo. I'm also not a fan of the symlink approach. It solves the problem of <em>modifications</em> but doesn't account for <em>new files</em>. So why don't we version the home directory itself?</p>
<p>I'm sure there are some good reasons not to version the home directory, and I'd love to hear them. I can't think of concrete examples where having a ~/.git directory is a bad idea, but I'm sure there are a few. Regardless, here's my current setup.</p>
<h2>My Setup</h2>
<p>My <a href="https://github.com/jasonkarns/dotfiles">dotfiles repo</a> is cloned under <code>~/dotfiles</code>. Git has a <a href="https://www.kernel.org/pub/software/scm/git/docs/git-config.html">setting</a> (<code>core.worktree</code>) that configures where the working tree should be checked out. It expects a path that can be either absolute or relative to the '.git' directory. To accomplish this, from my home directory:</p>
<pre>
$ git clone --no-checkout https://github.com/jasonkarns/dotfiles.git
$ git config core.worktree="../../"
$ git checkout master
</pre>
<p>This will do a normal git checkout, but instead of checking out the <code>master</code> branch to '~/dotfiles', it checks out to the home directory itself. The repo itself ('~/dotfiles') is completely empty, except for the '.git' directory.</p>
<h2>Benefits</h2>
<ol>
<li>Any changes to my dotfiles are known to git</li>
<li>New files are known to git</li>
<li>The home directory itself is not a git repo, yet it's fully versioned.
<ul>
<li>git commands cannot be run under ~</li>
<li>git status is not displayed in my command prompt ($PS1) under ~</li>
</ul>
</li>
<li>To manage the dotfiles repo and run git commands, I must be in '~/dotfiles'. (I like the forced context switch.)</li>
</ol>
<p>This setup has worked well for me. I have the benefits of a versioned home directory, without the annoyance of it being an actual repo (like seeing git status info in my command prompt).</p>
<h2>Complications</h2>
<p>There is one major complication: submodules. I use <a href="https://github.com/gmarik/vundle">Vundle</a> to manage my Vim plugins. Vundle itself is a git submodule under 'dotfiles/.vim/bundle'. In order to run <code>git submodule update</code>,  git requires that I be in the repo (duh) but <em>also</em> that I be in the working tree root. Since my working tree root is not <em>in</em> the repo, I get an error:</p>
<pre>
$ git submodule update
fatal: Not a git repository (or any of the parent directories): .git
</pre>
<p>To get around this, git has a feature wherein a plaintext file named '.git' is placed in the root of the working tree. It contains just a single line: <code>gitdir: /path/to/actual/repo/.git</code>. While this file exists, the home directory itself becomes, for all intents and purposes, a proper git repo. I can run git commands directly from the home directory and even my command prompt picks up the git status info.</p>
<p>So, with '~/.git' containing <code>gitdir: /Users//dotfiles.git</code>, I am able to properly run <code>git submodule update</code> and everything works! Of course, as long as this '.git' file exists, my home directory is essentially a proper git repo, so once I've run any necessary commands, I simply delete the '.git' file, and now I'm back to a plain, non-repo home directory!</p>
