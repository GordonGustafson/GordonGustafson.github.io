---
layout: post
title: "Never use ~ in your $PATH (/bin/sh might not expand it)"
date: 2016-06-01
tags: [shell]
---

[`dash`][almquist-shell-wikipedia] doesn't exand `~` when it occurs in `$PATH`, so if you want your scripts or environment to be portable across shells, you should never use `~` in your `$PATH`.
`/bin/sh` is `dash` [in Debian][dash-as-bin-sh-debian] and [in Ubuntu][dash-as-bin-sh-ubuntu], so you're most likely to encounter this problem when using the `#!/bin/sh` shebang on those operating systems.


To demonstrate this behavior on by Debian Stretch machine, let's create a `helloworld` script in `~/` and take a look at our `$PATH`:

{% highlight sh %}
$ cd ~
$ echo 'echo "hello world!"' > ~/helloworld
$ chmod +x ~/helloworld
$ echo $PATH
/usr/local/bin:/usr/bin:/bin:/usr/local/games:/usr/games
{% endhighlight %}


`bash` expands `~` when it occurs in `$PATH`:

{% highlight sh %}
$ PATH="$PATH:~" bash  # run bash with ~ on path
$ echo $PATH
/usr/local/bin:/usr/bin:/bin:/usr/local/games:/usr/games:~
$ echo ~
/home/ggustafson
$ helloworld
hello world!
{% endhighlight %}


But `dash` doesn't:

{% highlight sh %}
$ exit                 # leave bash with ~ on path
exit
$ PATH="$PATH:~" dash  # run dash with ~ on path
$ echo $PATH
/usr/local/bin:/usr/bin:/bin:/usr/local/games:/usr/games:~
$ echo ~
/home/ggustafson
$ helloworld
dash: 3: helloworld: not found
{% endhighlight %}


[almquist-shell-wikipedia]: https://en.wikipedia.org/wiki/Almquist_shell
[dash-as-bin-sh-debian]: https://wiki.debian.org/Shell#Default_.2Fbin.2Fsh_shell
[dash-as-bin-sh-ubuntu]: https://wiki.ubuntu.com/DashAsBinSh
