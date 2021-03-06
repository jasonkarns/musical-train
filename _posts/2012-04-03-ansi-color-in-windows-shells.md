---
title: ANSI color in Windows shells
date: 2012-04-03 21:36:00 -04:00
tags:
- ansicolor
- bash
- mingw
- shell
meta:
permalink: "/2012/04/03/ansi-color-in-windows-shells/"
---
<p>Having used git on windows for over three years now, I've fallen back in love with the command line. Bash, of course, not the windows command prompt. Beautiful, ubiquitous, warty bash. Git depends heavily on GNU utilities so on Windows it requires either cygwin or msys. Having been burned by cygwin in the past, I prefer the minimalism and simplicity of msys + mingw. Along with git, the entire ruby ecosystem lives in the shell. However, the numerous tools, gems and utilities that assume standard ANSI color support in the shell began to wear on me. Lo and behold, there is a lovely solution to provide ansi color support for bash (and cmd) on Windows: <a href="https://github.com/adoxa/ansicon">ansicon</a>.</p>
<p><a href="https://github.com/adoxa/ansicon/downloads">Download the zip</a> and extract. There are a few ways to install:</p>
<ol>
<li>Extract to a permanent location (I use <var>C:/bin/ansicon</var>). Execute <kbd>ansicon.exe -i</kbd> from within the appropriate directory for your system (x86/x64), and you're all set. Any new shells (bash and windows cmd included) will autorun the ansicon utility for displaying color output. Be sure not to move the executable prior to running <kbd>ansicon.exe –u</kbd>. This removes the registry entry and prevents an ugly error message for every command shell.</li>
<li>Alternatively, place the ansicon executable in your <kbd>PATH</kbd>, or add its location to your <kbd>PATH</kbd>. Then you can launch ansicon for a session with <kbd>ansicon.exe –p</kbd>.</li>
</ol>
<p>This utility has been working great for my on Windows XP. I've been having trouble getting it to work on Windows 7, but I hear it should be supported. I'll post an update when the Windows 7 issue is resolved.</p>
<h4 id="update_20120410T0245">Update</h4>
<p>Root cause, <a href="https://github.com/adoxa/ansicon/issues/28#issuecomment-4968769">uncovered</a>! If you use JRuby with a 64-bit JVM on Windows x64, ansicon won't work. The issue is that ansicon (64-bit) is capable of injecting into 32-bit processes, but not vice versa. Currently, the JRuby launcher is a 32-bit executable. Thus, if you're running a 64-bit shell (cmd, bash, or otherwise), ansicon will inject correctly into that process. It will then inject successfully into the 32-bit JRuby launcher process. At this point, for all intents and purposes, you're running the 32-bit version of ansicon. Thus, if you're running JRuby on a 64-bit JVM, then 32-bit ansicon is not able to inject into 64-bit JVM. There is an <a href="https://jira.codehaus.org/browse/JRUBY-6586">open feature request for JRuby</a> to ship its 64-bit version with a 64-bit launcher. You should vote for this feature. I also hear that <a href="https://github.com/adoxa">adoxa</a> (Jason Hood) has a potential fix for this issue in the works. Stay posted.</p>
<p>Of course, the easiest solution at the moment is to ensure that JRuby uses a 32-bit JVM. Just change (or set) your <kbd>JAVA_HOME</kbd> environment variable to point to a 32-bit JVM and you're golden.</p>
<h4 id="update_20120416T2314">Update 2: Issue Resolved</h4>
<p>The <a href="https://github.com/adoxa/ansicon/downloads">latest 64-bit binaries</a> (<a href="//github.com/downloads/adoxa/ansicon/ansi6432.zip">ansi6432.zip</a>) have fixed the issue. Just download and extract them over-top the 1.51 version.<br />
<img src="{{ site.baseurl }}/assets/2012/04/e0905-color.png?w=300&amp;h=159" alt="" title="Windows x64 MINGW Bash with ANSI color with ansicon" width="300" height="159" class="alignnone size-medium wp-image-315" /></p>
