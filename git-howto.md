# Git Guide

Table of content

- [Working with Git](#working-with-git)
  - [Name a Feature/Topic branch](#name-a-featuretopic-branch)
  - [Create a feature branch](#create-a-feature-branch)
  - [Catch up with the remote master](#catch-up-with-the-remote-master)
  - [Merge into master](#merge-into-master)
  - [Remove a feature branch](#remove-a-feature-branch)
  - [Commit messages](#commit-messages)
  - [Useful git aliases](#useful-git-aliases)
- [Creating Pull Requests](#creating-pull-requests)
  - [Before Opening the PR](#before-opening-the-pr)
  - [After Opening the PR](#after-opening-the-pr)
  - [After Merging the PR](#after-merging-the-pr)
- [See also](#see-also)

## Working with Git

### Name a Feature/Topic branch

All fixes, features and improvements must be done in a separate branch which
has to be branched from the **master** branch.

```text
  * 93a6175 - (topic-branch) Your second commit
  * a135597 - Your first commit
 /
* 82405b6 - The point you branch out
* cdf18eb - An earlier commit
```

The name of your development branch should be in a format that was agreed upon
by all team members:

- Keyword. In order to make the purpose of the branch as clear as
  possible the branch name should be prefixed with one of the following
  keywords:

  - *major*. If you are introducing a new feature that breaks backward.
    compatibility
  - *feature*. If you are introducing a new feature in a backward compatible way.
  - *fix*. If the changes are not introducing a new feature e.g. bug fix or
    refactoring.

- Another useful thing to include in the branch name is the JIRA task number
  e.g. UPPSF-123. There is integration between JIRA and Github that will show
  the branch in the JIRA task under the Development tab. For more information
  see Jira's article about "[Referencing issues in your development work][referencing-issues-in-your-development-work]"

Use the pattern: `<type-of-change>/<Jira-ticket-id>-<meaningful_name>`. An
example name following the rules above will be:
*feature/UPPSF-123-my-cool-implementation*

[referencing-issues-in-your-development-work]: https://confluence.atlassian.com/jirasoftwarecloud/referencing-issues-in-your-development-work-777002789.html

### Create a feature branch

```bash
# Ensure you are in the master branch
git checkout master
# Get the latest changes from the remote master branch
git pull --rebase
# Should say you are up to date with origin/master and clean
git status
# Create a new local branch
git checkout -b <branch-name>
# Push local branch to remote origin
git push –u origin <branch-name>
```

*[Example]*: Create a feature branch for Jira ticket UPPSF-123:

```bash
git checkout master
git pull --rebase
git checkout -b feature/UPPSF-123-my-cool-implementation
git push -u origin feature/UPPSF-123-my-cool-implementation
```

### Catch up with the remote master

```bash
# Pull latest master
git checkout master
git pull --rebase

# Rebase changes from master that was just updated with remote master to ensure
# feature branch is up to date. If this is done the right way there shoudn't be
# any merge conflicts.
git checkout <branch>
git rebase master

# Push the changes to the remote feature branch.
# --force-with-lease will prevent you wiping someone else's changes
git push --force-with-lease
```

Catch up with Master branch example:

```bash
git checkout master
git pull --rebase
git checkout feature/UPPSF-123-my-cool-implementation
git rebase master
git push –-force-with-lease
```

### Merge into master

Merge the feature branch with the following strategy - rebase and merge into
master with --no-ff:

Rebase before merging, because this provides a clean, linear history that's
easier to understand. Merge with --no-ff, because it creates a merge commit
that:

- makes it easier to see which commits belong to which branch.
- can be easily reverted.

For example you branched off master and made two commits. While you're working,
somebody pushed a commit to the upstream master. Currently the repo looks like
this.

```text
* dfb434e - (HEAD -> master, origin/master) Upstream commit made after you branched out
| * 93a6175 - (topic-branch) Your second commit
| * a135597 - Your first commit
|/
* 82405b6 - The point you branch out
```

You should do the below:

```bash
git checkout master
git pull
git checkout <branch>
git rebase master
git push --force-with-lease
git checkout master
git merge --no-ff <branch>
```

When finally merged, your history should look like this:

```text
*   1dde652 - (HEAD -> master, origin/master) Merge branch 'topic-branch'
|\
| * fdb8a7e - (topic-branch) Your second commit
| * f28ae50 - Your first commit
|/
* dfb434e - Upstream commit made after you branched out
* 82405b6 - The point you branch out
* cdf18eb - An earlier commit
```

### Remove a feature branch

Once you are done with the work in the feature branch you should remove it both
locally and from the remote.

```bash
# Remove the local branch
git branch -d <feature-branch>
# Remove the branch from the remote
git push origin --delete <feature-branch>
# (optional) Prunes tracking branches not on the remote.
git remote prune origin
```

Remove feature branch example:

```bash
git branch -d feature/UPPSF-123-my-cool-implementation
git push origin --delete feature/UPPSF-123-my-cool-implementation
```

### Commit messages

Commit messages are a way you communicate with your team members and your
future self about code. Good commits are crucial to maintenance. It might not
seem so when you're writing them, but commits tend to be very useful when
investigating the code.

Here are good reads about the importance of good commit messages:

- [A Note About Git Commit Messages](https://tbaggery.com/2008/04/19/a-note-about-git-commit-messages.html)
- [The importance of commit messages](https://wildbit.com/blog/2008/11/11/the-importance-of-commit-messages)
- [5 Useful Tips For A Better Commit Message](https://thoughtbot.com/blog/5-useful-tips-for-a-better-commit-message)
- [How to Write a Git Commit Message](https://chris.beams.io/posts/git-commit/)
- [Better Commit Messages with a .gitmessage Template](https://thoughtbot.com/blog/better-commit-messages-with-a-gitmessage-template)

### Useful git aliases

Rebase with the *--preserve-merges* option passed to git rebase so that locally
created merge commits will not be flattened.

```text
pl = git pull --rebase=preserve
```

Outputs branches history In a beautiful/readable state

```text
l = log --all --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr)%Creset %Cblue%an%Creset' --abbrev-commit --date=relative
```

Will protect all remote refs that are going to be updated by requiring their
current value to be the same as the remote-tracking branch we have for them

```text
pfl = git push –-force-with-lease
```

## Creating Pull Requests

### Before Opening the PR

Before you open a pull request you should consider the git history of your
development branch. In order to ease the code review process, the commits in
the development branch should:

- Represent a single logical unit of work.
- Have meaningful names, describing what the commit is introducing.
- Have a CI build that is passing

If your commits don't have the properties listed above, your PR may be
rejected. If you want to avoid that you might need to do some splitting,
squashing and rewording of your commits. After you have crafted your commits to
have the above properties, the changes from your development branch should be
deployed and tested on the team's dev environment.

**Note on squashing**. The need for squashing at this stage arises from the
requirement that each commit should represent a single logical unit of work. So
it will be a bad idea to squash all commits into a single one here (or at later
stage) the following reasons:

- This will make the code review process harder and your PR may even be
  rejected.
- If the PR is approved and merged into master, the master history will get a
  big set of changes in a single commit and that will make the exploring and
  understanding of the project's history harder.

**Note on descriptions**. It's very important to write a good Pull Request
description. Reviewers will normally read it before looking at the diff, so
make sure it gives them enough context so they know what they are looking at.

### After Opening the PR

- At least 2 reviewers should approve the PR in order for it to be merged into
  master. So sit back, relax and take care of any review comments related to
  your PR.
- After your reviewers approved the PR, you can proceed with merging the
  development branch into master. The preferred way to merge into master is
  with a merge commit, because the commits in your development branch should be
  already well crafted (no additional squashing is needed) and the merge commit
  will provide an explicit view of all the changes introduced by the PR

**Note for the reviewers**. In order to enable continuous learning and
improvement add review comments to the actual PR in Github, so that the review
process will be visible to all team members.

### After Merging the PR

- After merging your code changes to master it is good to delete the
  development branch, so that it will not create any clutter
- When creating a tag use [semantic versioning](https://semver.org/)

## See also

- [Oh shit, git!](https://ohshitgit.com/)
- [Understanding git’s –force-with-lease](https://blog.developer.atlassian.com/force-with-lease/)
