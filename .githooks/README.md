# Git hooks

These are not active until you point git at them. It's a one time step per
clone:

```
git config core.hooksPath .githooks
```

Setting `core.hooksPath` replaces the usual `.git/hooks` lookup entirely, so
anything you had sitting in there stops running. There's nothing else in this
project that installs hooks, so that is normally fine.

## pre-commit

Stops you from committing directly onto `main`, since everything lands there
through a squashed pull request. Switch to a branch and commit again:

```
git switch -c some-branch-name
```

Staged changes follow you across the switch, so nothing is lost.

The hook keeps quiet during rebases, merges, cherry-picks and reverts, because
those legitimately create commits while `main` is checked out. It also ignores
a detached HEAD.

If you genuinely mean to commit on `main`:

```
ALLOW_MAIN_COMMIT=1 git commit ...
```

That's narrower than `git commit --no-verify`, which would skip every hook
rather than just this check.

## A note if you work on Windows

Git on Windows usually runs with `core.fileMode = false`, which means it does
not track the executable bit. A hook added or replaced from there gets recorded
as mode `100644`, and git refuses to run a hook it can't execute. It does say
so, but only as a hint that is easy to scroll past:

```
hint: The '.githooks/pre-commit' hook was ignored because it's not set as executable.
```

The commit itself still goes through, so the hook looks like it is working on
Windows and quietly does nothing everywhere else. Set the bit in the index when
you stage:

```
git add .githooks/pre-commit
git update-index --chmod=+x .githooks/pre-commit
```
