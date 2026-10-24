## Lab report 1: Source Control Management

Names of the Members: MIZAT Loubna Kheira

Module: DevOps

CHEMSEDDINE Khadidja LADHEM Meriem Wafaa BENALI Hosna

## Part A: Identity and import

## Exercise1:

- a) ls-a shows two files whose names start with a dot, their names are:

- .env.example and .gitignore

- b) If we use cp -r .../ctf-scoreboard/*, the hidden files are not copied because * does not include hidden files by default.

- c) This does not protect the file, because the file is already tracked by Git. Adding it to .gitignore does not remove it from the repository.

## Part B: A conflict on purpose

## Exercise2:

- a) Git cannot merge the changes automatically, because both branches modified the same line.One branch changed the points to 450, and the other changed them to 600.

- b) If the changes are in two different challenges, Git can usually merge them automatically, because they do not affect the same lines.

- c) The conflict-marker check can run automatically in a CI/CD pipeline, for example, through GitHub Actions before merging a pull request.


## Part C: Find a bad commit with bisect

## Exercise3:

- a) Git Bisect needs about 5 steps to find the bad commit among 31 commits because it uses binary search to reduce the number of commits to check.

- b) We calculate the number of steps using ([ 𝑙𝑜𝑔2 (31)] = 5). This means that about 5 steps are sufficient to find the bad commit using binary search.

- c) If check.sh is inside the repository, it might disappear when Git checks out an older commit if the script did not exist in that version. This can prevent Git Bisect from running the test correctly. Therefore, the script is placed outside the repository in /tmp.

- d) If the 31 commits are squashed into one commit, Git Bisect cannot identify which original change introduced the bug because the individual commits are no longer available in the branch history. It can only identify the combined commit as bad.

## Part D: A Secret That Stays in the History

## Exercise 4

- a) Working tree scan: exit code 0 (no leaks). History scan: exit code 1 (3 leaks found).

- b) The rule is aws-access-token.

- c) Each commit keeps the hash of its parent. The second commit is only a new commit added after the first one, so the first commit with the key is still in the history.

- d)

- 1. Revoke the key and create a new one.

- 2. Remove it from the history and force push.

We revoke first because someone may have already copied the key, and revoking stops the danger right away.


## Part E: One shared, protected repository

## Exercise 5

- a) Nobody can change main alone. Every change needs a pull request and one approval from another person, so a stolen account or a mistake can’t go directly to main. Blocking force push and deletion also protects the history.

- b) It makes the supply chain attack harder (bad code added through a hacked account).

- c) On a free account, branch protection works only on public repositories. Private ones need a paid plan.

[URL : https://github.com/MizatLoubna22/CTF-Scoreboard](https://github.com/MizatLoubna22/CTF-Scoreboard)
