---
title: "Into the Rabbit Hole: Cheat Sheets for Programmers"
date: 2023-02-28T23:00:00+05:30
tags: 
    - programming
    - Into the Rabbit Hole
description: >
    Exploring various useful cheat sheets for programmers.
---

## Introduction

In this post, we'll explore various cheat sheets related to programming and places where you can find more of them.

## Digging Up

### 1. Cheat.sh

[Cheat.sh](https://cheat.sh/) <small>(or [cht.sh](https://cht.sh/)</small> if you prefer) claims to be "the only cheat sheet you need". It's used in a CLI. How does it work? The website should give us some hint, but let's dive in ourselves.

![cheat.sh image](/images/chtsh.png)

So, open a terminal emulator and type the following code:

```sh
curl https://cht.sh/tar
```

It fetches examples of the `tar` command from multiple sources. Let's try the following to narrow the results down further:

```sh
curl https://cht.sh/tar~extract
```

You can also get cheat sheets on a specific language

![cheat.sh example](/images/chtsh-example.gif)


Try the following for example:

```sh
curl https://cht.sh/python/lambda
```

For cases when the answer is from StackOverflow, add a `\?Q` suffix to hide comments <small>(or just `?Q` if you're using quotes around the URL)</small>; try and see the difference between these 2 commands:

```sh
curl https://cht.sh/python/reverse+list
```

```sh
curl https://cht.sh/python/reverse+list\?Q
```

Similarly, you can turn off ANSI coloring with `\?T`, and you can combine both like `\?QT`.

![cheat.sh output diagram](/images/chtsh-diagram.png)

#### Installation

There's also a shell client that you can install by running:

```sh
PATH_DIR="$HOME/bin"  # or another directory on your $PATH
mkdir -p "$PATH_DIR"
curl https://cht.sh/:cht.sh > "$PATH_DIR/cht.sh"
chmod +x "$PATH_DIR/cht.sh"
```

Or to globally install it for all users, instead run:

```sh
curl -s https://cht.sh/:cht.sh | sudo tee /usr/local/bin/cht.sh && sudo chmod +x /usr/local/bin/cht.sh
```

And you can add its tab completion like for bash:

```sh
curl https://cheat.sh/:bash_completion > ~/.bash.d/cht.sh
. ~/.bash.d/cht.sh
# and add . ~/.bash.d/cht.sh to ~/.bashrc
```

And for zsh:

```sh
curl https://cheat.sh/:zsh > ~/.zsh.d/_cht
echo 'fpath=(~/.zsh.d/ $fpath)' >> ~/.zshrc
# Open a new shell to load the plugin
```

This is how you'll perform those commands in the shell client:

```sh
cht.sh tar
cht.sh tar~extract
cht.sh python lambda
cht.sh python reverse list
cht.sh python reverse list \?Q
```

The client has other useful features like a handy shell where you can write your queries without having to prefix them with cht.sh each time. Visit [here](https://github.com/chubin/cheat.sh#client-usage) for more on how to use it. Here's a couple examples:

```sh
$ cht.sh --shell
cht.sh> go reverse a list
...
cht.sh> cd go
cht.sh/go> reverse a list
...
$ cht.sh --shell go
cht.sh/go> reverse a list
...
cht.sh/go> join a list
...
# You can make a single query for another language by prepending /
cht.sh/go> /python dictionary comprehension
...
cht.sh/go> cd ..
cht.sh> help
...
```

This is just the tip of the iceberg, I recommend looking through <https://cheat.sh/:intro> and if you wish to use advanced parameters, <https://cheat.sh/:help>. The intro page should tell you how to install it so that you can use it without having to type an URL each time.

Now, let's explore some other lesser known features of cheat.sh that the documentation doesn't mention.

#### I. `chmod` Calculator

Cheat.sh has a handy `chmod` calculator. Try the following for example which gives you a nice colored output describing the permissions:

```sh
curl https://cht.sh/chmod/755
```

If you're using the CLI client, you can also type:

```sh
cht.sh chmod 755
```

#### II. Learn X in Y Minutes Integration

You can run the following to view the [Learn X in Y Minutes](https://learnxinyminutes.com/) page of Python:

```sh
curl https://cht.sh/python/:learn
```

Replace `python` with any other language and have its Learn X in Y Minutes page instead. Likewise, with the CLI client, you can type:

```sh
cht.sh python :learn
```

#### III. IETF RFC Viewer

You can type the following to quickly view any IETF RFC in your terminal:

```sh
curl https://cht.sh/rfc/8259
```

And replace `8259` with any IETF RFC you wish to view. Likewise, with the CLI client, you can type:

```sh
cht.sh rfc 8259
```

You can also write the name of the RFC instead for searching through available RFCs. For example:

```sh
curl https://cht.sh/rfc/json
```

gives

```txt
4627 The application/json Media Type for JavaScript Object Notation (JSON). D. Crockford. 
6901 JavaScript Object Notation (JSON) Pointer. P. Bryan, Ed., K. Zyp, M. Nottingham, Ed..
...
```

And then you can make a new query with the RFC number.

#### IV. Latency Numbers

You can type the following to view a cheat sheet of latency numbers:

```sh
curl https://cht.sh/latencies
```

Likewise, with the CLI client, you can type:

```sh
cht.sh latencies
```

#### V. OEIS Integration

An [OEIS](https://oeis.org) integration currently exists but is broken. I've submitted an [issue](https://github.com/chubin/cheat.sh/issues/376) (and thankfully the maintainers responded very quickly), and a [fix](https://github.com/chubin/cheat.sh/issues/187#issuecomment-1446306305) should be along the way. I'll update this post after it's fixed and document its usage.

### 2. Navi

[Navi](https://github.com/denisidoro/navi) is another popular command-line cheat sheet, except it's interactive.

[![navi ascii cast](https://asciinema.org/a/406461.svg)](https://asciinema.org/a/406461)

Install it from your preferred [repository](https://github.com/denisidoro/navi#installation) or [download the binary](https://github.com/denisidoro/navi/releases/) for your OS. There are other ways to use it, which you can find in their [README](https://github.com/denisidoro/navi#usage). Let's explore them ourselves.

#### I. Typing `navi` In the Terminal

It's the most simple way to use navi, Try it:

```sh
navi
```

The ASCII Cinema video above showcases how the output looks.

#### II. As a Shell Widget

You can add the following to your shell's rc file to be able to use navi as a shell widget:

```sh
# bash
eval "$(navi widget bash)"

# zsh
eval "$(navi widget zsh)"

# fish
navi widget fish | source

# elvish
eval (navi widget elvish | slurp)
```

After, you can access navi by pressing `Ctrl+G`. You can learn more about it [here](https://github.com/denisidoro/navi/blob/master/docs/installation.md#installing-the-shell-widget). It's what was used to "fix" commands in the video above. Try it yourself by pressing `CTRL + G` after typing the following after you've added the mentioned line to your shell rc file and reloaded it:

```sh
ls | remove first line
```

It should change to the follow:

```sh
ls | tail -n +2
```

#### III. As a Tmux Widget

You can add the following to your tmux config to use navi as a tmux widget:

```sh
bind-key -T prefix C-g split-window \
  "$SHELL --login -i -c 'navi --print | head -c -1 | tmux load-buffer -b tmp - ; tmux paste-buffer -p -t {last} -b tmp -d'"
```

The widget can be accessed by pressing `prefix + C-g`. It's useful when you want to use navi in any command-line apps or SSH sessions.

#### IV. Others

You can also use navi as [aliases](https://github.com/denisidoro/navi/blob/master/docs/aliases.md), a [shell scripting tool](https://github.com/denisidoro/navi/blob/master/docs/shell_scripting.md), or an [Alfred workflow](https://github.com/denisidoro/navi/blob/master/docs/shell_scripting.md).

#### Cheatsheet Repositories

Running `navi` for the first time will help you download and manage cheatsheets.

You can also:

- [browse through featured cheatsheets](https://github.com/denisidoro/navi/blob/master/docs/cheatsheet_repositories.md#browsing-through-cheatsheet-repositories)
- [import cheatsheets from git repositories](https://github.com/denisidoro/navi/blob/master/docs/cheatsheet_repositories.md#importing-cheatsheets)
- [write your own cheatsheets](#cheatsheet-syntax) (and [share them](https://github.com/denisidoro/navi/blob/master/docs/cheatsheet_repositories.md#submitting-cheatsheets), if you want)
- [use cheatsheets from other tools](https://github.com/denisidoro/navi/blob/master/docs/cheatsheet_repositories.md#using-cheatsheets-from-other-tools), such as [tldr](https://github.com/tldr-pages/tldr) and [cheat.sh](https://github.com/chubin/cheat.sh)
- [auto-update repositories](https://github.com/denisidoro/navi/blob/master/docs/cheatsheet_repositories.md#auto-updating-repositories)
- auto-export cheatsheets from your [TiddlyWiki](https://tiddlywiki.com/) notes using a [TiddlyWiki plugin](https://bimlas.gitlab.io/tw5-navi-cheatsheet/)

#### Cheatsheet Syntax

Cheatsheets are described in `.cheat` files that look like this:

```sh
% git, code
# Change branch
git checkout <branch>
$ branch: git branch | awk '{print $NF}'
```

The full syntax and examples can be found [here](https://github.com/denisidoro/navi/blob/master/docs/cheatsheet_syntax.md).

#### More Info

Run the following to learn more about possible options:

```sh
navi --help
```

### 3. Devhints

[Devhints](https://devhints.io/) is a beautiful website containing many useful cheat sheets.

Visit any page, for example <https://devhints.io/regexp>, and you'll have a good amount of useful information right in front of you.

### 4. explainshell.com

[explainshell.com](https://explainshell.com/#) is not really a "cheat sheet" per se; this website allows you to get elegant explanations for various shell commands. Visit <https://explainshell.com/explain?cmd=tar%20xzvf%20archive.tar.gz> for an example.

### 5. Awesome Cheatsheet

[Awesome Cheatsheet](https://github.com/detailyang/awesome-cheatsheet) is an [awesome](https://github.com/sindresorhus/awesome) lists of various cheat sheets from all over the web.

Notable mentions:

- <https://bigocheatsheet.com/> - Big-O Cheat Sheet
- <https://easings.net/> - Easing Functions Cheat Sheet
- <https://cheats.rs/> - Rust Language Cheat Sheet
- <https://clojure.org/api/cheatsheet> - Clojure Cheat Sheet
- <https://cljs.info/cheatsheet/> - ClojureScript Cheat Sheet
- <https://perldoc.perl.org/perlcheat> - Perl 5 Cheat Sheet
- <https://docs.scala-lang.org/cheatsheets/> - Scala Cheat Sheet

### 6. Learn X in Y Minutes

[Learn X in Y Minutes](https://learnxinyminutes.com/) Is a website containing cheat sheet-like references of various programming languages among other things, something we've already seen in Cheat.sh. Visit <https://learnxinyminutes.com/docs/julia/> for an example.

### 7. 100 Seconds of Code

[100 Seconds of Code](https://www.youtube.com/playlist?list=PL0vfts4VzfNiI1BsIK5u7LpPaIDKMJIDN) is a YouTube series by Fireship. It's an awesome series covering a vast amount of concepts within 100 seconds each.

### 8. Cheatography

[Cheatography](https://cheatography.com/) is another cheat sheet website, hosting thousands of programming cheat sheets. Visit <https://cheatography.com/davidsouther/cheat-sheets/bash-zsh-shourtcuts/> for an example. You can also download the cheat sheets as PDFs.

## Wrapping Up

So that's it, we've covered several useful cheat sheets for programmers. I hope this post was helpful, and maybe the next time you need to remember the syntax for a regex lookaround, you'll have an easier time.

## Going Ahead

If you enjoy reading through lists of things, you may want to check out the [sindresorhus/awesome](https://github.com/sindresorhus/awesome) repository or the [awesome-list](https://github.com/topics/awesome-list) topic on GitHub. Cheat.sh's maintainer has [awesome-console-services](https://github.com/chubin/awesome-console-services) that you might find fun to tinker with.

## Acknowledgements

Thanks to [@Dylan-DPC](https://github.com/Dylan-DPC) and [@Monadic-Cat](https://github.com/Monadic-Cat) for reviewing this post.
