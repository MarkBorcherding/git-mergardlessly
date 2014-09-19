# Git Mergardlessly

Merge _irregardless_ of if you're up to date.

## Reasoning

We have a workflow where nothing gets merged to `master` that isn't in production. This results in an undesired effect of this history. Consider the following graph.

```
A---B---C  master
         \
          D---E---F my-feature
```

If we stage `my-feature` through our QA environments and then merge master we end up with the following graph.

```
A---B---C---D---E---F master
                      my-feature
```

This is fine. It's correct, but what we loose is the history of what commits were part of `my-feature` and which prexisted it. We would like to presever the hitory of what happened as part of that feature.

## First attempt

I know what you're thinking. Just merge `my-feature` into `master` with `--no-ff`.

```
A---B---C-----------G master
         \         /
          D---E---F my-feature

```

This preserves the history, but we end up running a different SHA in production than that which we staged through our QA environments. This isn't ideal.

## Manual workaround

The following set of commands work, but feel like we're dancing around he thing we really want to do.

```
git checkout -b something-temporary master
git merge my-feature --no-ff
git checkout my-feature
git merge something-temporary --ff-only
git branch --delete something-temporary
# Stage my-feature through QA
git checkout master
git merge my-feature --ff-only
```

Even if we scripted that dance, it still feels rather sloppy.

## Git mergagrdlessly

What I want is a way to create a merge commit between two branches regardless, or irregardless, of if its needed. Enter `git mergardlessly`.

With a `mergeardlessly` merge you would end up with the following:

```
     master
        v
A---B---C-----------G my-feature
         \         /
          D---E---F

```

Subsequent merges of `my-feature` into `master` would be a `--ff-only` and the SHA that was staged through QA testing is exactly the same as what runs in `master` on production.
