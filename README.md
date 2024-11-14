# SplitPRs
If you endup with a feature branch in git with too many changes, the PR review might be a nightmare, so it's a good idea to make it smaller. But how to do this after you already have all the commits in a single branch?

Even worse, probably you have commits out of order, because you worked on a component, then another one, did some changes, came back to the same component again etc, all this while committing those changes to the same branch throughout the days, making it inviable to use cherry pick like indicated in this article.

By following these steps you will have at the end a sequence of small branches that can then be used in sequential PRs:

1. Commit and push your BIG branch (e.g: my-big-branch)
This branch has lots of different changes that you want to split into separate branches for creating smaller PRs

2. Checkout the base branch for your PR reviews (e.g: main)
git checkout main
git pull main

3. Study how to split your big branch into smaller ones
Keep in mind that you will create multiple branches, each one with small changes that later will become a separate PR
Strategy 1: Each branch touches a different dir/component. e.g: “All files in dir /shared/formatter-utils”
Strategy 2: Each branch has a group of files touched with the same objective. e.g.: “Enhancing README files of all services to stick to the new standards”
Draw a sequence of branches you will create, from the least dependant to the most one. e.g.: "Readme files" -> "formatter-utils" -> "api-service1" -> "ui-service1"

4. Create the branch that will receive the small changes

# make sure you are starting this branch from the correct base
git checkout main
git pull origin main

git checkout -b 1-readme-changes

5. Copy files from the big branch to the newly created branch
cd [root-of-repo]
git checkout my-big-branch -- .
At this point, all files from the big branch will be available in your workspace, but they are all unstagged
You checked-out "my-big-branch" contents, but your workspace is still in the "1-readme-changes" branch
Tip
Checkout from the original branch to the newly created branch only the files you want to include as part of this PR (you won't see all the files so be careful not to lose track of all the files you eventually want to be part of a PR).

git checkout my-big-branch -- specific/dir/iwant.go

6. Add only the files related to this small branch, commit and push it
For example, in this point you might want to add only README files, or only files from a certain directory.
Add file by file in VSCode git view, or do something like
git add "**/README.md"
git commit -m "chore: Fixing wrong info"
git push origin 1-readme-changes
Now you have your first small branch pushed to repository, ready to be used in a PR review

7. Create a PR for branch "1-readme-changes"
Review it and make changes, taking care of not adding the other unstaged files still present in workspace
Have your PR merged to "main"

8. Now repeat the process for all branches you need
Checkout "main", pull merged contents after PR approval and start a new branch as in step 4.
Pay attention to the order of the PRs so your codebase doesn't break.
# change base branch
git checkout main

# fetch new contents after PR merge
git pull origin main

# create new branch
git checkout -b 2-formatter-utils

# add files for this branch
# these files are already unstagged in the workspace
git add "shared/formatter-utils"
git commit -m "feat: New method for formatting dates"
git push origin 2-another-change

9. Do this until all branches/PRs are merged
Advanced Tip
You can create all branches at once without having to wait for the PR approvals by creating one branch from the base of another and then merging them back to main as the PRs are approved. This can become messy specially if you need to make important changes as part of the PR review, as you will then need to rebase the entire chain of branches, so do this with caution.
It would become something like

# change base branch
git checkout main
git pull origin main

# first branch
git checkout -b 1-first-branch
git add "shared/formatter-utils"
git commit -m "first one"
git push origin 1-first-branch

# second branch (will start from 1-first-branch)
git checkout 1-first-branch
git checkout -b 2-dependant-branch
git add "ui"
git commit -m "adding ui"
git push origin 2-dependant-branch

# after PR 1-first-branch is merged, merge main contents
git checkout 2-dependant-branch
git pull origin main
git push origin 2-dependant-branch
