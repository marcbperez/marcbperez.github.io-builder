# Clean Git branching

Branching is essential when working with Git. This article explains how to work
collaboratively while developing new enhancements, releasing them and hot-fixing
issues for those releases. While not official, this is a widely used technique
described in many other articles and implemented in several clients.

## Main branches

Projects diverge from one branch: `master`. This is the deployment branch and
holds the last stable version that can be deployed. The integration branch,
commonly known as `develop` and initially cloned from `master`, will store
unreleased stable changes from features. While `develop` can merge new features,
`master` can only include changes from `develop` once it is ready to be
released, and hot-fixes to cover bugs.

```bash
# Make the first commit to master.
git checkout master
touch .gitignore
echo "" > .gitignore
git add .gitignore
git commit .gitignore -m "Added blank .gitignore file."
# Push master, the deployment branch.
git push -u origin master
# Create and push develop, the integration branch.
git checkout -b develop master
git push origin develop
```

## New features

When it comes to adding new functionality, we use a feature branch. Features are
cloned directly from the latest version in the integration branch and are
usually prefixed by `feature`, for example, `feature-login-view`. To create a
new feature and start developing, first check it out.

```bash
# Create a new feature from the latest code in develop.
git checkout -b feature-login-view develop
```

Now new work can be added inside the feature branch. Commits can be made without
polluting `develop`, enhancements can be tested independently and merged back
atomically into integration again. Once all the commits have been made and, in
the case there is one, the `Unreleased` section of the changelog has been
updated the feature is ready to be merged.

```bash
# Merge back the new feature into develop.
git checkout develop
git merge --no-ff feature-login-view -m "Merged feature-login-view."
# Push develop and, optionally, the feature.
git push origin feature-login-view develop
```

This can also be done via a pull request. For this push the feature branch after
all the changes have been committed, and follow the instructions from the
the platform you are working with. Both [Github][github-pull-request] and
[Bitbucket][bitbucket-pull-request] have guides on how to make pull requests
using their platforms.

## Releasing develop

Once we have merged once, or several, branches into `develop` and their
integration has been successful it is time to publish them and increase the
version of the project. This is done with release branches, usually prefixed
with `release-` and the new version number.

```bash
# Create a release of the integrated version.
git checkout -b release-0.1.0 develop
```

After the version has been bumped, the `Unreleased` section of the changelog is
now empty has moved its changes to the new release section, and the version
number has been changed across the project, the release is ready to be merged
and tagged as the latest stable version. Again, merging can be done via pull
requests.

```bash
# Merge back the new release into develop.
git checkout develop
git merge --no-ff release-0.1.0 -m "Merged release-0.1.0."
# Push develop and, optionally, the release.
git push origin release-0.1.0 develop
# Merge back develop into master.
git checkout master
git merge --no-ff develop -m "Merged development version 0.1.0."
# Tag the release and push it along with master.
git tag -a 0.1.0 -m "Tagged as 0.1.0."
git push origin 0.1.0 master
```

## Fixing master

While features and releases diverge from `develop`, severe bugs that occur in
production need to be fixed directly on `master` and merged back into the
deployment branch too. These are prefixed with `hotfix-` and, like releases,
followed by the new version number.

```bash
# Create a hotfix from the latest non-stable version.
git checkout -b hotfix-0.1.1 master
```
Once again, after all the changes have been made, the version bumped and the new
section added to the changelog, the fix can be merged back into `master` and
`develop`, and tagged like it is done with a release.

```bash
# Merge back the new hotfix into master.
git checkout master
git merge --no-ff hotfix-0.1.1 -m "Merged hotfix-0.1.1."
# Tag the new hotfixed version.
git tag -a 0.1.1 -m "Tagged as 0.1.1."
# Merge back the new hotfix into develop.
git checkout develop
git merge --no-ff hotfix-0.1.1 -m "Merged hotfix-0.1.1."
# Push the hotfix, its tag, master and develop.
git push origin hotfix-0.1.1 0.1.1 master develop
```

---

## References

- Bitbucket. Pull Requests [online]. Available at
  [https://www.atlassian.com/git/tutorials/making-a-pull-request][bitbucket-pull-request]
  (Accessed 8 August 2017).
- Github. Creating a pull request [online]. Available at
  [https://help.github.com/articles/creating-a-pull-request/][github-pull-request]
  (Accessed 8 August 2017).

---

Last edited on 8 August 2017.

[bitbucket-pull-request]: https://www.atlassian.com/git/tutorials/making-a-pull-request
[github-pull-request]: https://help.github.com/articles/creating-a-pull-request/
