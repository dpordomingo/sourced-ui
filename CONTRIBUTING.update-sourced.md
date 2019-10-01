$ FROM=0.33.0rc1
$ TO=0.34.0rc1

# Commit since sourced-ui diverged from upsteam.
# It reads from the commit history, the commits message that contains `git-subtree-split`
# It is automatically added by `git` when merging upstream subtree into `sourced-ui`
# `git-subtree-split` points to the commit from upstream that was merged into `sourced-ui`
# Split commit will equal ${FROM} if upstream did not rewrote ${FROM}, and the merge
#    commit, created when the last upgrade was made, used the default message proposed by `git`
# DISCLAIMER: If ${SPLIT} does not equal ${FROM}, ${FROM} should be updated according to the actual situation.
$ SPLIT=`git log | grep git-subtree-split | head -n 1 | awk '{print $2}'`

# Commit from where the release branch was started.
# It may be the same as ${FROM} if ${TO} release branch started from ${FROM}
# It may be behind ${FROM} if ${TO} does not contain all its commits
$ COMMON_ANCESTOR=`git merge-base ${FROM} ${TO}`

# Branch to update superset, and to create the PR
$ UPDATE_BRANCH_NAME=update_superset_${TO}

# Name of `src-d/sourced-ui` remote
$ REMOTE_NAME=sourced

# Let's rollback changes at ${FROM} which are not in ${TO}
# It will fail, and rollback nothing if ${TO} is reachable from ${FROM} (so ${COMMON_ANCESTOR} equals ${FROM})
# It must not cause conflicts
$ git checkout ${FROM}
$ git revert --no-commit ${COMMON_ANCESTOR}...${FROM}
$ git commit --signoff --message "Revert changes made by ${FROM}, not in ${TO}"
$ REVERT_COMMIT=`git rev-parse HEAD`

# Merge previous revert into `sourced-ui`
# It may cause conflicts, in case there are changes in `superset-ui` colliding with
#    changes made only in ${FROM}, not in ${TO}
$ git fetch ${REMOTE_NAME}
$ git branch ${UPDATE_BRANCH_NAME} ${REMOTE_NAME}/master
$ git checkout ${REMOTE_NAME}/master
$ git subtree merge --squash --prefix superset ${REVERT_COMMIT}
# resolve conflicts that it could have appeared, and commit them with `git merge --continue`
# once the revert commit has been fully merged, we should sign the squash and merge commits off
$ REVERT_SQUASH_COMMIT=`git rev-parse ${UPDATE_MERGE_COMMIT}^2`
$ git checkout $REVERT_SQUASH_COMMIT
$ git commit --amend --signoff
$ REVERT_SQUASH_COMMIT=`git rev-parse HEAD`
$ git checkout ${UPDATE_BRANCH_NAME}
$ git merge --signoff --message "" 


$ git commit --amend --signoff


# Merge the new release ${TO} into `sourced-ui`
# It may cause conflicts, in case there are changes in `superset-ui` colliding with
#    changes made also in ${TO}
$ git subtree merge --squash --prefix superset ${TO}
# resolve conflicts that it could have appeared, and commit them with `git merge --continue`

# sign the squash and merge commits off
$ UPDATE_MERGE_COMMIT=`git rev-parse HEAD`
$ UPDATE_SQUASH_COMMIT=`git rev-parse ${UPDATE_MERGE_COMMIT}^2`
$ git checkout $UPDATE_SQUASH_COMMIT
$ git commit --amend --signoff








from 0.33.0rc1 - 7591a709bb61a0841ad14b643088584af7cf36c6                       ${FROM} = ${SPLIT}
  to 0.34.0rc1 - a04fad858644466219b7ea399aead110cb8ea655                       ${TO}

sourced-ui
pre-update-0.34.0rc1 - 07d241267

# Looks for common ancestor in mailing list
Sha: 51068f007

    $ git merge-base 0.33.0rc1 0.34.0rc1
    51068f007efb1362cf59a54f7d3909a526bd24c4                                    ${COMMON_ANCESTOR}

# Checkout to split commit (When sourced-ui diverged from upsteam)
    $ git log | grep git-subtree-split | head -n 1 | awk '{print $2}';
    7591a709bb61a0841ad14b643088584af7cf36c6                                    ${SPLIT} = ${FROM}
    $ git checkout ${SPLIT}

# Revert cherry-picked commits in the release branch
    $ git revert --no-commit ${COMMON_ANCESTOR}...0.32.0rc1 # should be ${FROM}=${SPLIT} <<<-- typo??
    $ git commit -s -m "revert to upstream master from 0.32.0rc1" # IDEM <<<----------- typo??
        -> cad1d756f
            Squashed 'superset/' changes from 7591a709..940771b0
            git-subtree-split: 940771b0a9839ea475a5cd79283f7d1ab6b7642f

# Merge revert into sourced-ui tree
    $ git checkout master
    $ git subtree merge -P superset HEAD@{1} --squash  -> 5db9fbb8a

# Merge new release
    $ git subtree merge -P superset ${TO} --squash



-- Find common ancestor in the mailing list
-- Checkout to split commit
-- Revert commits
-- Merge revert into sourced-ui tree
-- Merge new release




 


from 0.32.0rc2 - 8fb4ba0d74652eb0d901f40d9b18fb01a4f138e2                       ${FROM} = ${SPLIT}
  to 0.33.0rc1 - 7591a709bb61a0841ad14b643088584af7cf36c6                       ${TO}

sourced-ui
pre-update-0.33.0rc1 - f4309675e

# Looks for common ancestor in mailing list
Sha: 1fece0d2f

    $ git merge-base 0.32.0rc2 0.33.0rc1
    1fece0d2fa8bc35e296d49289678ada5ea8aaa0e                                    ${COMMON_ANCESTOR}

# Checkout to split commit (When sourced-ui diverged from upsteam)
    $ git log | grep git-subtree-split | head -n 1 | awk '{print $2}';
    8fb4ba0d74652eb0d901f40d9b18fb01a4f138e2                                    ${SPLIT} = ${FROM}
    $ git checkout ${SPLIT}

# Revert cherry-picked commits in the release branch
    $ git revert --no-commit ${COMMON_ANCESTOR}...${FROM}
    $ git commit -s -m "revert to upstream master from ${FROM}" # IDEM <<<----------- typo?? -> cad1d756f
                                                                                                    Squashed 'superset/' changes from 7591a709..940771b0
                                                                                                    git-subtree-split: 940771b0a9839ea475a5cd79283f7d1ab6b7642f

# Merge revert into sourced-ui tree
    $ git checkout master
    $ git subtree merge -P superset HEAD@{1} --squash  -> 5db9fbb8a

# Merge new release
    $ git subtree merge -P superset ${TO} --squash

