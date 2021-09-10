# Git Handbook for RTM Developers

Most of the content and more are also available on: https://guides.github.com/introduction/git-handbook/


## Basics Operations

* **Create and Push a new Repository**:
    * Within local folder: `git init -b master`
    * Create a branch in GitHub `gh repo create project-name` (*requires GitHub CLI*)
    * If you created README or LICENSE on GitHub, pull those now: `git pull --set-upstream origin master`
    * Add, Commit and Push: `git add . && git commit -m "initial commit" && git push`
    * Now, go to repo setting and add collaborators as needed.
* **Clone Repository**: `git clone --depth 20 {remote_name} {branch_name}`
* **Pull Latest**: `git pull {remote_name} {branch_name}`
* **View Remote**: `git remote -v`
* **View Git History**: `gitk`
* **View Changes**: `git status`
* **Stage Changes**: `git add .`
* **Commit Changes**: `git commit -m 'xx'`
* **Push Changes**: `git push {remote_name} {branch_name}`


## Managing Branches

* **View Branches**: `git branch`
* **Create New Local Branch**: `git checkout -b {my_new_branch}`
* **Pull and Merge Changes Locally**: `git pull {remote_name} {another_branch_name}`
    * This method avoids the use of pull requests, and is recommended when attempting to update current working branch to the latest of a protected branch like *master*.
    * Never use git pull to update a protected branch locally and push a protected branch. Protected branch will reject git push.
* **Resolving Merge Conflicts**: if multiple updates were made to the same file from separate branches, you will encounter a merge conflict. Git does its best to resolve these conflicts automatically, but if it fails to do so, manual edits are required. Look for `<<<<<<<, =======, >>>>>>>` style texts within the file with commit conflicts to resolve them. See [GitHub Docs on Merge Conflict](https://docs.github.com/en/github/collaborating-with-pull-requests/addressing-merge-conflicts/resolving-a-merge-conflict-using-the-command-line) for more detail.
* **Protected branches**: in GitHub, you can create rules to mark certain branches protected. Once protected, certain review and approval criteria must be met in order to make updates to this branch. Protected branches also cannot be deleted. See [GitHub Docs About Protected Branches](https://docs.github.com/en/github/administering-a-repository/defining-the-mergeability-of-pull-requests/about-protected-branches) for more detail
* **Pull Requests and Approval Procedure**:
    * Pull Requests (PRs), as noted in [GitHub Docs About Pull Requests](https://docs.github.com/en/github/collaborating-with-pull-requests/proposing-changes-to-your-work-with-pull-requests/about-pull-requests), is a formal way of merging one branch into another. PRs provide an opportunity to document changes made, and request a reviewer's input before the changes are finalized and merged into another branch.
    * master branch, prerelease branches, and certain project branches are *protected branches* in the *rtm* repository. PRs are required to make changes to these *protected branches*.
    * always create a new branch to make working updates, do development and testing work on your working branch
    * once working branch has been tested and validated, create a pull request in the direction of `{master_protected_branch_name} <- {my_working_branch_name}`.
    * you may keep pushing updates to your working branch while PR is being reviewed, and the PR will keep receive commit updates as you do that. This feature allow you to address comments from reviewers without needing to create a new PR.
    * if you run into merge conflicts during PR, you need to update your working branch to the latest of the master or protected branch, resolve merge conflict. Once you push commits that resolve the merge conflicts, PR can be automatically merged after approval.
* **Forking versus Copy of Repository**:
    * **Fork**: forking allows another GitHub account to make a copy of the *entire repository*. Forking maintains connection between the *upstream repository* and the *fork*. Forks can create pull requests to the *upstream repository* and may fetch and merge with *upstream repository*'s branches at any time.
    * **Copy of Repository**: you are only allow one fork per account. In order to maintain "extra copies" for the repository to restrict project repository access, you can create extra copies of the same repository. These copies are uploaded onto GitHub as a separate repository and have *no connection* with the original *upstream repository*. To bring changes back to the *upstream repository*, push the change as a branch to the *upstream repository*, then use PR as you would with a working branch.


## Working with Submodules

* **Clone repo with submodules**:
    * `git clone --recurse-submodules {remote_name} {another_branch_name}`, or
    * `git clone {remote_name} {another_branch_name}`, then `git submodule update --init`
* **Pull latest updates for submodules**:
    * `git submodule update --recursive`, or 
    * `git pull --recurse-submodules`
* **Make commits with submodules**
    The following example uses *EMAT_Tools* as the example toolbox
    1. clone the ubcx_emat repo: `git clone --recurse-submodules -b emat https://github.com/TransLinkForecasting/rtm.git`
    2. go to git submodule folder and check out a working branch:
        * `cd rtm`
        * `cd RTM/Scripts/EMAT_Tools`
        * `git checkout {your_submodule_branch}`
    3. now you can replace the csv file or whatever else in the submodule folder
        * `git commit -m "your_msg"`
        * `git push origin {your_submodule_branch}`
    4. you have updated the submodule repo, now you need to do one more thing, update the ubx_emat repo's reference to the submodule
        * `cd ../../..` (brings you back to main folder)
        * `git add /EMAT/Scripts/EMAT_Tools`
        * `git commit -m "update reference to submodule"`
        * `git push origin {your_branch}`
    5. now you have updated both main repo and submodule repo, you are done.
    6. for newer versions of git, you can push both main repo and submodule repo changes all at once:
        * `git config push.recurseSubmodules on-demand`
        * `git push`
* **Submodule references and attaching submodules**:
    The following example uses *tl_internal_tools* as the example toolbox 
    1. Within your current rtm local git repo, add submodule
        * `git submodule add -b master https://github.com/TransLinkForecasting/tl_internal_tools.git RTM/Scripts/Internal_Tools`
    2. Commit `.gitsubmodule` file and `RTM/Scripts/Internal_Tools` reference pointer to your main repo.
    3. Push changes, now your project repo is attached to the *tl_internal_tools* submodule.


## Release Management
* **Commit SHA**: a unique string is assigned to every commit, you may use the commit SHA to find your commit and tag it for release.
* **Tags**: to tag a version, you need to specify a version_number, commit SHA and a short message describing the version.
    * `git tag -a {version_number} {commit_sha} -m "{msg}"`

## Other concepts

* **Bundles**: packaged git commit data that can be sent to and from people without access to a remote repository.
    * To Send: `git bundle create {file_name}.bund {latest_SHA_tag}..{bundle_branch}`
    * To Receive: 
        * Fetch into a temp branch: `git fetch {file_name}.bund {bundle_branch}:{tmp_branch}`
        * Merge: `git merge {bundle_branch} {tmp_branch}`
* [Cherry picking](https://git-scm.com/docs/git-cherry-pick)
* [Revert commit](https://git-scm.com/docs/git-revert)

## Useful tools
* VSCode Git Lens (with git blame, ability to manage submodules, and browse history)
