---
title: "Escaping Restricted Shell"
description: "How to escape restricted shell. But first what is Restricted Shell ?"
date: 2025-09-09T22:53:56+08:00
image: restricted-shell.png
math: 
license: 
comments: true
draft: false
---


# Escaping Restricted Shell

Good day everyone, been quite some times since I last post anything in this blog. Gonna try be more consistent after this. Wish me luck.

Today I want to share about How to escape restricted shell. But first what is Restricted Shell ?

### Restricted Shell

Basically a way to restrict user from executing command other than what is being allowed. Take example below is using `rbash`. We try to use `clear` and `ls` and even `cat` command but the shell does not even found the command.

<figure><img src="restricted-shell-1.png" alt=""><figcaption></figcaption></figure>

Before we continue I need to explain what is `PATH` in linux.

### What is PATH in Linux ?

`PATH` is an environment variable that specifies a list of directories where the system looks for executable files. In easy words, if we were to execute command, for example `ls`, the system will check through all the PATH to see if there's `ls`. If there is, then the command can be executed, but if none, it will prompt `command not found` like images before.

We continue.

To check what command is allowed we can check the `PATH` by using `export -p` to show the environment.

<figure><img src="https://3341280199-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2Fr7WBydXq9jwgJjNXxPCj%2Fuploads%2F2vKknJn8U0djl1P9Dg4W%2Fimage.png?alt=media&#x26;token=b1a8e049-ac37-41b0-b914-76ecd66a33a5" alt=""><figcaption></figcaption></figure>

Then we `-ls $PATH`. This show what command that we are allow to execute.

<figure><img src="https://3341280199-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2Fr7WBydXq9jwgJjNXxPCj%2Fuploads%2FwMWK8kvLH9NyryYqJNG5%2Fimage.png?alt=media&#x26;token=c06c7378-7ecb-4c5c-9acb-24cec9361f5e" alt=""><figcaption></figcaption></figure>

To escape this restriction shell, we can use any of the above command and check on GTFOBins and check if there's function `shell` on the allowed command. Here we can see `vi` does have Shell function.

<figure><img src="https://3341280199-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2Fr7WBydXq9jwgJjNXxPCj%2Fuploads%2FwL6VU06yyYDehMnVUg6M%2Fimage.png?alt=media&#x26;token=ce24d2e9-2414-4dae-aa49-1d81340f9031" alt=""><figcaption></figcaption></figure>

We can try any of these 2. I tried both but the second one works. We only need to copy all the command and paste it on the restricted shell that we have.

<figure><img src="https://3341280199-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2Fr7WBydXq9jwgJjNXxPCj%2Fuploads%2FumWixUTyba7P2HIRWaqU%2Fimage.png?alt=media&#x26;token=e4cf011c-b31a-4dc7-b050-635944445bfd" alt=""><figcaption></figcaption></figure>

When prompt below, just press enter.

<figure><img src="https://3341280199-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2Fr7WBydXq9jwgJjNXxPCj%2Fuploads%2FDGBrISPR6lP70ZnFii2O%2Fimage.png?alt=media&#x26;token=24cf2d8d-d876-4217-8cc7-ab45fb509b85" alt=""><figcaption></figcaption></figure>

Then we can export the default path for linux, since this is linux box. Then we now have the ability to execute command like normal again since the PATH for the linux is not restricted to only `/home/tom/usr/bin` anymore.

<figure><img src="https://3341280199-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2Fr7WBydXq9jwgJjNXxPCj%2Fuploads%2FbNIBgDQM1MR9bA2su7Ng%2Fimage.png?alt=media&#x26;token=6ae76196-814e-40ad-959b-18cb8e5c3a36" alt=""><figcaption></figcaption></figure>

There's other way you can experiment on how to escape restricted shell. You play around this box on [https://portal.offsec.com/labs/play](https://portal.offsec.com/labs/play), Room: DC-2.

Thanks for reading !
