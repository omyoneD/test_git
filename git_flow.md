- [Git Flow](#git-flow)
  - [Branch model](#branch-model)
  - [Branch naming](#branch-naming)
  - [Commit messages](#commit-messages)
  - [Cherry-picking things](#cherry-picking-things)
  - [Release naming](#release-naming)

# Git Flow

It's important to select correctly the base branch before creating a new one to start coding. This results in a much cleaner commit history, which allows a better tracking of changes, what in turn, eases many frequent tasks such as the process of assembling a quality changelog.

## Branch model

Types of branches:

- **master**: Future release base branch and must be changed only by approved Pull Requests from release or hotfixes. (CI must be applied to this branch)
- **release/X.y.z**: Support branch to use as Release Candidates to be merged to master.
- **devel**:  Base branch of all other working branches and must be changed only by approved Pull Requests. 
- **feature/fix/refactor/doc**: short lived branches for development and testing, must be deleted after merging to a devel branch.

The most important principle is that the base branch to start developing a change must be the oldest, not deprecated, release to which such change applies.

For example, if there are 4 branches: release/2.6, release/3.0, release/3.1 and devel (which is the future release, currently in development),
and the change must be applied from release/3.0 to master, the base branch should be the release/3.0. The development starts by creating a new branch from the base branch and commiting/pushing changes as needed until the change is ready for merging. In order to merge changes to the upstream, 3 different pull requests must be created, each one applying to the appropriate branch: release/3.0, release/3.1 and master.

If there are **conflicts** in most recent releases, a new branch must be created from __task branch__ to resolve them and make a pull request for that recent release and subsequent ones.

## Branch naming

Branch names must follow the format:

```text
<type>/<jira_ticket_id>_<description>
```

- *types*:
  - **feature**: new functionality is added
  - **fix**: a bug is corrected
  - **refactor**: code is rearranged to improve performance or readability. Also old unused code is removed.
  - **doc**: changes related to documentation (when code hasn't changed at all)
  - **version**: new version is going to be released.
- *jira_ticket_id*: format DATIOX-XXXX
- *description*: a couple of words describing the changes (use hyphens to separate words)

Examples:
```text
  fix/DATIO5-1234_luaproxy-mstr-redirection
  refactor/DATIO5-1235_remove-old-configuration
```

## Commit messages

It's a good practice to format commit messages as follows:

```text
[<entity>] <action>
```

Where entity should be something like a role name, iluvatar (if is a general change), doc, etc. And action is the change made to the repository o the result of that change.

Examples:
```text
[ldap] Generate new service users
[ansible] Add AWS support
[ranger-kms] Remove fake policy
[usersync] Fix problems with timer execution
```

## Cherry-picking things

When a fix applies to different branches, changes could be added by 'copy-pasting' the commits containing such changes into the corresponding branches.

**Only cherry-pick where changes are very specific/isolated and be very cautious with conflict resolution.**

It's recommended to use `git diff` command in order to ensure your changes are correctly integrated by comparing the previous status against the new additions.

```bash
# bring a single commit
git cherry-pick -x <commit-sha1>

# bring multiple commits (A is not included)
git cherry-pick -x A..D

# bring multiple commits (including A)
git cherry-pick -x A^..D
```

Note: `-x` adds to the commit message the sha1 reference of the cherry-picked commit, like this: `(cherry picked from commit adf1cdbecaf6458a5f52720394e221abac91bb26)`


## Release naming

Release versions are named with rc1, rc2, rc3...

Example: usersync:1.0.3-rc2

**Note**: Version name never must include `-SNAPSHOT` in its name, because then version not correspond with docker image version tag.

Once a release is tested and stable it must be merged to master and tagged with version definition **vX.y.z**

Example: v1.0.3


