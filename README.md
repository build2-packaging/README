# The `build2` packaging

The aim of this organization is to serve as a convenient collaboration place
for packaging third-party projects for `build2`.

By hosting your packaging efforts under this organization (as opposed to, say,
your personal workspace) you make it easier for others to join or, if you are
no longer interested, to take over the package development/maintenance.

In addition to this organization we also have the
[`packaging@build2.org`](https://lists.build2.org) mailing list and the
[`#build2`](https://cpplang.slack.com/app_redirect?channel=build2) channel
on [`cpplang.slack.com`](https://cpplang.slack.com/). To join, simply get
in touch using either medium.

## Version and Release Management

This section describes the recommended version and release management for an
upstream package with the semantic version. The main challenge compared to
developing our own project is that the version is controlled by upstream.

> Note: The `bdep-release` command doesn't need the project to be
> `bdep-init`'ed so we can use it for stubs (packages that do not contain
> source code and can only be obtained from the system).

> Note: If we are dealing with a multi-package project (for example, `sqlite`,
> which consists of a library and an executable), then we assume they are all
> hosted in the same project repository and use `$` in their `depends` version
> constraints.

1. When packaging upstream for the first time, say version `A.B.C`, start with
   its snapshot version `A.B.C-a.0.z`.

   If instead packaging a stub for the first time, tag the first release:

   ```
   $ bdep release --tag --show-push
   $ # review tag
   $ git push ...
   ```

2. When it's time to release, if the package has a final version, then there
   is nothing to do for the version and we can go directly to publishing.

   If, however, the package has the snapshot version, then release its final
   version:

   ```
   $ bdep release --no-open --show-push
   $ # review commit
   $ git push ...
   ```

   To publish:

   ```
   $ bdep publish
   ```

3. After final version `A.B.C` has been released, the package enters the
   "revision phase".

   During this phase we can change the "infrastructure" (`buildfiles`, etc)
   but should limit upstream source code changes only to bugfixes/warning
   squashing (preferably backported).

   In this phase every commit must be accompanies by the revision increment:

   ```
   $ # make changes
   $ git add .
   $ bdep release --revision --show-push
   $ # review commit
   $ git push ...
   $ bdep publish
   ```

4. When a new upstream version is available, say `A.B.C`, and we are ready to
   upgrade, switch to its snapshot version `A.B.C-a.0.z`, similar to step 1:

   ```
   $ bdep release --open --no-commit --open-base A.B.C
   ```

   Note that you should only push this change to `master` once you are certain
   upstream is good (i.e., you won't be able to go to a previous version of
   upstream once you've done this). So it may make sense to do this in a
   branch if external testing is required.
