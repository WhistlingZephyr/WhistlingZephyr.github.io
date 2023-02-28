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

[Cheat.sh](https://cheat.sh/) <small>(or [cht.sh](https://cht.sh/)</small> if you prefer) claims to be "the only cheat sheet you need". How does it work? The website should give us some hint, but let's dive in ourselves.

You usually use this one in a CLI. So, open a terminal emulator and type the following code:

```sh
curl https://cht.sh/tar
```

It fetches examples of the `tar` command from multiple sources. Let's try the following to narrow the results down further:

```sh
curl https://cht.sh/tar~extract
```

You can also get cheat sheets on language syntax using:

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

#### Installation

There's also a shell client that you can install from [here](https://github.com/chubin/cheat.sh#installation) and add its tab-completion from [here](https://github.com/chubin/cheat.sh#tab-completion). This is how you'll perform those commands in the shell client:

```sh
cht.sh tar
cht.sh tar~extract
cht.sh python lambda
cht.sh python reverse list
cht.sh python reverse list \?Q
```

The client has other useful features like a handy shell where you can write your queries without having to prefix them with cht.sh each time. Visit [here](https://github.com/chubin/cheat.sh#client-usage) for more on how to use it.

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

### 2. Devhints

[Devhints](https://devhints.io/) is a beautiful website containing many useful cheat sheets.

Visit any page, for example <https://devhints.io/regexp>, and you'll have a good amount of useful information right in front of you.

### 3. explainshell.com

[explainshell.com](https://explainshell.com/#) is not really a "cheat sheet" per se; this website allows you to get elegant explanations for various shell commands. Visit <https://explainshell.com/explain?cmd=tar%20xzvf%20archive.tar.gz> for an example.

### 4. Awesome Cheatsheet

[Awesome Cheatsheet](https://github.com/detailyang/awesome-cheatsheet) is an [awesome](https://github.com/sindresorhus/awesome) lists of various cheat sheets from all over the web.

Notable mentions:

- <https://bigocheatsheet.com/> - Big-O Cheat Sheet
- <https://easings.net/> - Easing Functions Cheat Sheet
- <https://cheats.rs/> - Rust Language Cheat Sheet
- <https://clojure.org/api/cheatsheet> - Clojure Cheat Sheet
- <https://cljs.info/cheatsheet/> - ClojureScript Cheat Sheet
- <https://perldoc.perl.org/perlcheat> - Perl 5 Cheat Sheet
- <https://docs.scala-lang.org/cheatsheets/> - Scala Cheat Sheet

### 5. Learn X in Y Minutes

[Learn X in Y Minutes](https://learnxinyminutes.com/) Is a website containing cheat sheet-like references of various programming languages among other things, something we've already seen in Cheat.sh. Visit <https://learnxinyminutes.com/docs/julia/> for an example.

### 6. 100 Seconds of Code

[100 Seconds of Code](https://www.youtube.com/playlist?list=PL0vfts4VzfNiI1BsIK5u7LpPaIDKMJIDN) is a YouTube series by Fireship. It's an awesome series covering a vast amount of concepts within 100 seconds each.

### 7. Navi

[Navi](https://github.com/denisidoro/navi) is another popular command-line cheat sheet, except it's interactive.

Install it from your preferred [repository](https://github.com/denisidoro/navi#installation) or [download the binary](https://github.com/denisidoro/navi/releases/) for your OS, then type

```sh
navi
```

in a terminal emulator to get started. There are other ways to use it, which you can find in their [README](https://github.com/denisidoro/navi#usage).

### 8. Cheatography

[Cheatography](https://cheatography.com/) is another cheat sheet website, hosting thousands of programming cheat sheets. Visit <https://cheatography.com/davidsouther/cheat-sheets/bash-zsh-shourtcuts/> for an example. You can also download the cheat sheets as PDFs.

## Wrapping Up

So that's it, we've covered several useful cheat sheets for programmers. I hope this post was helpful, and maybe the next time you need to remember the syntax for a regex lookaround, you'll have an easier time.

## Going Ahead

If you enjoy reading through lists of things, you may want to check out the [sindresorhus/awesome](https://github.com/sindresorhus/awesome) repository or the [awesome-list](https://github.com/topics/awesome-list) topic on GitHub. Cheat.sh's maintainer has [awesome-console-services](https://github.com/chubin/awesome-console-services) that you might find fun to tinker with.

## Acknowledgements

Thanks to [@Dylan-DPC](https://github.com/Dylan-DPC) and [@Monadic-Cat](https://github.com/Monadic-Cat) for reviewing this post.
