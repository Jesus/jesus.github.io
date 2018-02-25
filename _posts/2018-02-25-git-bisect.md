---
layout: post
title:  Git bisect
date:   2018-02-25 10:19:00 +0000
categories: programming
---

Bugs are sometimes difficult to fix and it's always good to know the tools
available to hunt them. Today I'm presenting `git bisect`.

You'll find it useful in scenarios like these:

  * *"I know we didn't have this bug 2 weeks ago."*
  * *"This page in my web application used to load quick. When did it get so
    slow?"*

We can use it to find the commit that introduced a bug. Looking
at the commit we may get more insight on what was being updated when the thing
broke. Probably we'll also understand much more easily what's going on and
what's causing it.

To use this tool, we need to identify:

  * A *good* commit, a point in history that we know for sure that didn't have
    the bug.
  * A *bad* commit, a later point in history when the thing has already broken.

We know that the guilty commit it somewhere between the good commit and the
bad one.

<img src="/assets/images/commits.png" class="inline-text large-image" />

And we also need to write a test that we'll use to identify whether a commit
is good or bad.

## How to use it?

Let's see this in action with an example anyone can repeat. Instead of looking
for a bug, we'll find a commit when certain file was created. I've used
the [Dropbox API][dropbox-api] git repository to try this out.

We need to write a test that'll indicate if the current commmit is good or bad,
in our case the test will pass if the file doesn't exist, and it'll fail if
the file is found.

```sh
#!/bin/bash
if [ -f lib/dropbox_api/endpoints/files/get_thumbnail_batch.rb ]; then
  exit 1 # Bad commit
else
  exit 0 # Good commit
fi
```

Notice that we've used a shell script so this is language independent. You can
use this tool no matter what test suite or framework you're working on, you
just need to make it uses the usual POSIX exit codes.

Now we're ready to start the quest:

```sh
git bisect start «bad» «good» # Start bisect session
git bisect run sh test.sh     # Traverse tree looking for the oldest bad commit
git bisect reset              # End bisect session
```

There're several [ways to refer to the good and bad commit][revision-selection],
some examples:

```sh
git bisect start 629be78 0d0fcc
git bisect start master 0d0fcc
git bisect start HEAD HEAD~100 # My favourite, from head to last 100 commits
```

When I ran `git bisect run sh test.sh`, git would show me the commit where the
file was created, which in my example was: [`83981c`](https://github.com/Jesus/dropbox_api/commit/83981c2).

So that was the basics, as always with git, there're many options and
fine tuninng available. If you want to find out
more I'll recommend the [official documentation][git-bisect].

[dropbox-api]: https://github.com/Jesus/dropbox_api
[revision-selection]: https://git-scm.com/book/tr/v2/Git-Tools-Revision-Selection
[git-bisect]: https://git-scm.com/docs/git-bisect
