# **GitFlow** <!-- omit in toc -->

## Table of Contents <!-- omit in toc -->
- [Introduction](#introduction)
- [What is a successful Git workflow?](#what-is-a-successful-git-workflow)
- [Centralized Workflow](#centralized-workflow)
- [Feature Branch Workflow](#feature-branch-workflow)
- [Git Flow](#git-flow)
- [Forking Workflow](#forking-workflow)
- [Discussion](#discussion)
- [References](#references)

## Introduction

Software development is a pretty collaborative task. Because of that, we need tools to help us to work seamlessly with our team mates. That's why we have a lot of tools like SVN, Mercurial, Perforce and, of course, Git. Git is an extremely powerful and flexible distributed version-control system and is widely adopted by the industry.

Because of it's flexibility, developers have a lot of freedom to decide how to work with Git. This can lead to chaotic teams were each developer uses the workflow he wishes. This is the reason teams usually adopt guidelines for it's usage, and with a lot of usage some teams accumulated a lot of knowledge on best practices in the usage of Git.

This notebook will talk about some of the recommend workflows.

## What is a successful Git workflow?

> What is a successful Git workflow?
> When evaluating a workflow for your team, it's most important that you consider your team’s culture. You want the workflow to enhance  the effectiveness of your team and not be a burden that limits productivity. Some things to consider when evaluating a Git workflow are:
>
> - Does this workflow scale with team size?
> - Is it easy to undo mistakes and errors with this workflow?
> - Does this workflow impose any new unnecessary cognitive overhead to the team?

[What is a successful Git workflow - Atlassian Tutorials](https://www.atlassian.com/git/tutorials/comparing-workflows)

## Centralized Workflow

This workflow is as simple as it can get. You have a `master` branch in which all the work of all the developers is done.

## Feature Branch Workflow

> The core idea behind the Feature Branch Workflow is that all feature development should take place in a dedicated branch instead of the master branch. This encapsulation makes it easy for multiple developers to work on a particular feature without disturbing the main codebase. It also means the master branch will never contain broken code, which is a huge advantage for continuous integration environments.

[Feature Branch Workflow - Atlassian Tutorials](https://www.atlassian.com/git/tutorials/comparing-workflows/feature-branch-workflow)

In this workflow we also have a `master` branch, which contains working code. Instead of working directly in that branch, developers will create a new `feature` branch each time they start working on a new feature. When the feature is completed the `feature` branch is merged into `master`.

## Git Flow

In Git Flow we have two *Main Branches*:
- `master` - all the code in this branch is ready to production.
- `develop` - integration branch for all current development.

Beyond this two main branches we have three kinds of *Support Branch*:
- `feature` - is branched from `delevop` to be used in the development of features. When the work is complete it's merged into `develop`. Most of the work is done in this branch, and if it's a long life branch, it is a good idea to merge `develop` into it with frequency for reducing merging problems when the feature is completed.
- `release` - is branched from `develop` and represents a preparation of a new production release, for example for test purposes. If any bug is found it should be fixed directly in this branch and merged into `develop`. When code is ready for production, the branch should be merged into `master`, a tag should be created and the branch should also be merged into develop.
- `hotfix` - is branched from `master` in case a bug is found on production code. When the bug fix is completes, the branch should be merged into `master`, `develop` and `release`, if there is an existing `release` branch.

It's recommended to use the `--no-ff` flag in all the merges even if the merge could be performed with a fast-forward. This avoids losing information about the historical existence of a feature branch and groups together all commits that together added the feature.

The good thing about Git Flow is that there is a CLI supporting the workflow called git-flow.

## Forking Workflow

> Instead of using a single server-side repository to act as the “central” codebase, it gives every developer their own server-side repository. This means that each contributor has not one, but two Git repositories: a private local one and a public server-side one. The Forking Workflow is most often seen in public open source projects.

[Forking Workflow - Atlassian Tutorials](https://www.atlassian.com/git/tutorials/comparing-workflows/forking-workflow)

The project will have a public repository to host it's codebase. Instead of cloning this repository in their own machine, developers will fork it. This copy serves as their **personal public repository**.

Whenever the developer is ready to publish code, he will commit it to its personal public repository. 

To integrate the code into the project's repository, the developer creates a **Pull Request** that will be analysed by project's maintainers and if approved, integrated into project codebase.

All of these personal public repositories are really just a convenient way to share branches with other developers and inside this repositories any variety of workflow can be adopted.

## Discussion

Centralized Workflow has a lot of drawbacks and should not be used professionally. Using this workflow you lose a lot of Git's powers.

Feature Branch Workflow is suitable for any size of project and organization. It's strong in it's simplicity, allowing developers to cooperate without overhead. 

As the code in master is always ready for production, the usage of Feature Branch Workflow is recommended for organizations that have a high level of maturity in it's software development process. For example,organizations that have a good CI pipeline, use static code analysis tools, use code review and have a good quality of automated tests.

Git Flow, on the other side, is a really complete workflow and will add some overhead in the development process. Because it's complete, it supports more rigorous release workflows. 

For example, if the software is released in sprints, after every sprint the new code has to be freezed so that a QA team can test the code. If bugs are found, the fixes are supposed to be made without including any new feature being developed in the next sprint. Git Flow is suitable for these cases.

## References

* https://en.wikipedia.org/wiki/Git
* https://www.atlassian.com/git/tutorials/comparing-workflows
* https://nvie.com/posts/a-successful-git-branching-model/
* https://medium.com/@muneebsajjad/git-flow-explained-quick-and-simple-7a753313572f
