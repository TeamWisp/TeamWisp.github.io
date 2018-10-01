---
layout: post
title:  "Development Pipeline"
date:   2018-09-27 11:43:16 +0200
category: common
weight: 5
permalink: /dev_pipeline/
---

## New Code / Major Bug Fixes / Refactoring
### 1. Create Branch

1. Update **development** and/or **feature** branches to the latest version.
1. Create Branch
	- Feature branches are prefixed with `feature_`.
	- short and descriptive naming.

### 2. Make Changes

1. Make Changes
1. Test
	- Make and/or run unit tests.
1. Commit
	- Prefix the message with: `[CHANGE]`, `[FIX]` or `[WIP]`.
	- Split your work into small parts. Every part is a commit. If the lead developer notices a lack of commits action will be taken.

### 3. Push Changes

1. Pull master for any changes.
1. Rebase branch onto master.
1. Squash any `[WIP]` commits.
	- Try to prevent submitting `[WIP]` commits to the feature branch.
1. Refactory if nessessary.
1. Push changes/branch up to Github.

### 4. Pull Request

1. Open pull request into the `development branch`. (**[Tutorial](https://help.github.com/articles/about-pull-requests/)**)
	- Describe changes in detail.
	- Add a bullet point changelog. (No need to prefix.)
1. The [build server]({{site.url}}/build_server) runs the pull request.

### 5. Code Review

1. Other devs should review code and leave suggestions. (**[Tutorial](https://github.com/features/code-review/)**)
	- No overly complex or clever code.
	- Check for potential issues.
	- Architectural improvements.
	- Code is properly abstracted.
	- Consider potential tests.

1. Code author is responsible for making any changes
	- Commit process follows the same process.

1. At least 2 reviewers should approve the code.
	- Leave a comment with an emoji thumbs up.
	- Briefly explain why it is okay.

## Minor Bug Fixes

1. Update **development** and/or **feature** branches to the latest version.
1. Checkout your target branch.
1. Make Changes
1. Commit Changes
	- Prefix the message with: `[FIX]`.
2. Push Changes

## Other Notes

### Exceptional Work

Exceptional code will be show cased and discussed during a meeting to both reward the author and educate the other programmers.

### Broken Builds

When a build breaks there will be a public message in Discord noting who broke it. This is not to punish people but to ensure there is always some one who knows who to contact when the build is broken and or go to him/her to help fix it.

No action will be taken against people who brake the build. However if it becomes a regular occurrence for an individual this will change. Action would vary case by case and is performed by the lead programmer.
