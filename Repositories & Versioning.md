# Repositories & Versioning

### Repository Hosting Service
We will be using [Bitbucket](https://bitbucket.org) for our repository hosting and all repositories should be private.

## Git Branching Model
### Cetralized Repo
The repository setup with that there shall be a central “truth” repo. Note that this repo is only considered to be the central one. We will refer to this repo as origin, since this name is familiar to all Git users.
Each developer pulls and pushes to origin. But besides the centralized push-pull relationships, each developer may also pull changes from other peers to form sub teams. For example, this might be useful to work together with two or more developers on a big new feature, before pushing the work in progress to origin prematurely. 

### The main branches 

At the core, the development model is greatly inspired by existing models out there. The central repo holds two main branches with an infinite lifetime:
- master
- develop

The master branch at origin should be familiar to every Git user. Parallel to the master branch, another branch exists called develop.
We consider origin/master to be the main branch where the source code of HEAD always reflects a production-ready state.
We consider origin/develop to be the main branch where the source code of HEAD always reflects a state with the latest delivered development changes for the next release. Some would call this the “integration branch”. This is where any automatic nightly builds are built from.
When the source code in the develop branch reaches a stable point and is ready to be released, all of the changes should be merged back into master somehow and then tagged with a release number. How this is done in detail will be discussed further on.
Therefore, each time when changes are merged back into master, this is a new production release by definition. We tend to be very strict at this, so that theoretically, we could use a Git hook script to automatically build and roll-out our software to our production servers everytime there was a commit on master.

### Supporting branches 
Next to the main branches master and develop, our development model uses a variety of supporting branches to aid parallel development between team members, ease tracking of features, prepare for production releases and to assist in quickly fixing live production problems. Unlike the main branches, these branches always have a limited life time, since they will be removed eventually.

The different types of branches we may use are:

- Feature branches
- Release branches
- Hotfix branches

Each of these branches have a specific purpose and are bound to strict rules as to which branches may be their originating branch and which branches must be their merge targets. We will walk through them in a minute.

By no means are these branches “special” from a technical perspective. The branch types are categorized by how we use them. They are of course plain old Git branches.

### Feature branches 

May branch off from:
> develop

Must merge back into:
> develop

Branch naming convention:

> anything except master, develop, release/*, or hotfix/*
but should be like this "features/yourfeature" .

#### Creating a feature branch 
When starting work on a new feature, branch off from the develop branch.
```
$ git checkout -b features/yourfeature 
Switched to a new branch "features/yourfeature"
```
### Incorporating a finished feature on develop 
Finished features have to be created a pull-request to be reviewed by the project leader and that will be merged into the develop branch to definitely add them to the upcoming release:
```
$ git add .
staged all files to commit
$ git commit -m "Meaningful commit message in details"
commit your changes
$ git push origin features/yourfeature
push your branch to origin
```
### Release branches 
May branch off from:
> develop

Must merge back into:
> develop and master

Branch naming convention:
> release/*

Release branches support preparation of a new production release. They allow for last-minute dotting of i’s and crossing t’s. Furthermore, they allow for minor bug fixes and preparing meta-data for a release (version number, build dates, etc.). By doing all of this work on a release branch, the develop branch is cleared to receive features for the next big release.

### Creating a release branch 
Release branches are created from the develop branch. For example, say version 1.1.5 is the current production release and we have a big release coming up. 
```
$ git checkout -b release/0000 develop
Switched to a new branch "release-1.2"
$ git commit -a -m "Details about this release"
[release-1.2 74d9424] Details about this release
```
### Finishing a release branch 
The changes made on the release branch need to be merged back into develop, so that future releases also contain these bug fixes.

The first two steps in Git:
```
$ git checkout master
Switched to branch 'master'
$ git merge release/00000
Merge made by recursive.
(Summary of changes)
$ git tag -a 1.2
The release is now done, and tagged for future reference.
```
To keep the changes made in the release branch, we need to merge those back into develop, though. In Git:
```
$ git checkout develop
Switched to branch 'develop'
$ git merge release/0000
Merge made by recursive.
(Summary of changes)
````
This step may well lead to a merge conflict (probably even, since we have changed the version number). If so, fix it and commit.

