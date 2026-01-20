# My awesome Git config!

## Install

```sh
$ git clone git@github.com:Bastes/git-config.git ~/.config/git
```

Add `~/.config/git/scripts` to your `$PATH` for custom scripts to be
automatically discovered:

```fish
$ fish_add_path ~/.config/git/scripts
```

## Gerrit review enhancements

Working with gerrit in an environment that forbids pushing branches to the
remote this workflow allows to persist "work in progress" draft reviews
under a topic matching your branch name (prefixed by a unique identifier
to avoid overlapping) and then clean up redundant reviews once your branch is
groomed and ready for review.

First, saved in your config a `gerrit.nickname` that will serve as a prefix
identifying your branches / topics:
```sh
$ git config gerrit.nickname <your-identifier>
```

Working on a feature, create a local branch based on the central branch:
```sh
$ git new-wip-branch my-super-branch
```

This creates a branch `<your-identifier>/my-super-branch` (using your
`gerrit.nickname`), the name of which will serve as a topic when you create
 the review drafts.

I commit as usual:
```sh
$ git commit ...
```

To persist your changes on the central repository:
```sh
$ git wip-review
```

This creates the reviews under the topic corresponding to the branch name (and
fails and warns you if you're not currently on one of your topic branches).

Your branch lives its life, you commit to it, rebase it, re-save with the same
command:
```sh
$ git wip-review
```

The time comes when it's ready. You may start by "grooming" it with:
```sh
$ git rebase -i --autosquash origin/master
```

Do whatever surgery you need, squash or split commits, doesn't matter. Once
you're done you may persist your changes again:
```sh
$ git wip-review
```

Now once you've done that, you may have abandonned commits that have a review
id and :warning: THOSE MAY STILL BE ACTIVE ON THE CENTRAL REPOSITORY :warning:.
But fear not, that's why the last script is here:
```sh
$ git clean-reviews
```

This will list the reviews from your local branch and your topic on the central
repository, compare the two and propose to abandon on the central repository
the reviews that are no longer current (i.e. no longer in your local branch).

This way, you can simply un-clutter your gerrit repository from all those
half-baked reviews that now amount to nothing, their code already either
persisted in new and better reviews or abandoned altogether.
