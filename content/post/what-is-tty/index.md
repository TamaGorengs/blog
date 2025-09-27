---
categories:
- Pentesting
- Notes
date: 2025-09-09
draft: false
description: "Why? So you can use Ctrl-C, arrow keys, tab completion, and command history."
slug: what-is-tty
image: what-is-tty.png
tags:
- linux
- technical
title: What is TTY?
---

# What is TTY ?

## Understanding TTY and PTY in Reverse Shells

If you've done CTFs or penetration testing, you've probably upgraded a
shell using something like:

``` bash
python3 -c 'import pty; pty.spawn("/bin/bash")'
```

Why? So you can use **Ctrl-C**, arrow keys, tab completion, and command
history. Basically, make it feel like a real terminal.

But when a friend asked me *"What actually is TTY?"*, I realised I
couldn't explain it well without digging deeper. Here's what I found.

------------------------------------------------------------------------

### What Happens When You Get a Netcat Reverse Shell

When you catch a reverse shell with netcat, it's just a raw TCP stream.
Your commands go in, and output comes back. The shell process is
connected to a network socket, **not** a terminal device.

That's why:

-   Arrow keys don't work
-   Ctrl-C doesn't send an interrupt signal
-   No job control (`fg`, `bg`, `Ctrl-Z`)
-   No nice terminal features like auto-suggestion or proper formatting

Upgrading the shell to use a **pseudo-terminal (PTY)** fixes all of
this.

------------------------------------------------------------------------

### What is TTY?

**TTY** stands for **Teletypewriter**. The old machines used to send
typed messages to computers.

On modern Unix/Linux systems, "TTY" refers to a terminal device file
(e.g., `/dev/tty1`, `/dev/pts/0`) that lets you interact with the system
via text commands.

If you run:

``` bash
tty
```

in a shell, it'll tell you which terminal device you're connected to.

------------------------------------------------------------------------

### What is a Pseudo-Terminal (PTY)?

A **pseudo-terminal** is a pair of virtual character devices that
emulate a real terminal.

-   **Master side**: controlled by a program (like SSH, `script`, or
    `socat`)
-   **Slave side**: looks and behaves like a real terminal to the shell

When you run:

``` bash
python3 -c 'import pty; pty.spawn("/bin/bash")'
```

Python creates a PTY and attaches your shell to the slave side. Now the
shell thinks it's talking to a real terminal and unlocks all the
interactive features.

------------------------------------------------------------------------

### TTY vs PTY - The Correlation

Think of it like this:

-   A **TTY** is the real terminal (hardware or virtual) that you
    normally type into.
-   A **PTY** is a fake TTY created in software to act like the real
    thing.

When you "upgrade" your reverse shell, you're essentially creating a PTY
so the remote shell behaves **as if** it were connected to a TTY. That's
why you suddenly get arrow keys, tab completion, and proper signal
handling.

------------------------------------------------------------------------

### Why Upgrade to TTY in a Reverse Shell?

Without a TTY:

-   Ctrl-C won't stop processes (no signal handling)
-   Arrow keys may print weird characters instead of moving the cursor
-   Tab completion won't work
-   Some programs (like `nano`, `top`, `less`) won't run properly

With a TTY:

-   Full interactive shell experience
-   Proper line editing & history
-   Works like a local terminal

------------------------------------------------------------------------

### Pro Tip: Other Ways to Upgrade Shells

``` bash
# Socat
socat file:`tty`,raw,echo=0 tcp-listen:4444  

# Python pty
python3 -c 'import pty; pty.spawn("/bin/bash")'  

# Script command
script /dev/null -c bash
```

For more TTY payloads, you can visit my
[notes](https://0xyur3i.gitbook.io/notes/linux/upgrading-to-fully-interactive-tty-shells)
blog.

------------------------------------------------------------------------

**Bottom line:**\
A raw reverse shell is like shouting commands through a tunnel.
Functional but clumsy.\
A TTY upgrade is like walking into the server room and sitting at the
console.
