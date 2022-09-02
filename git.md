# Git

## Clone all repositories from organisation

```shell
export GHORG="my-github-organisation"
mkdir ~/projects/$GHORG
cd ~/projects/$GHORG
gh api 'orgs/$GHORG/repos?private=true&per_page=1000' | jq -r '.[].ssh_url' | xargs -P $(nproc --all) -L1 git clone
```

## Squash

Squash several commits to one or two commits with new messages:

```
git reset --soft HEAD~7 && git add --all && git commit -m "New message here"
```

## Git aliases

(git kit)[https://github.com/akx/git-kit]

```
af 'git kit autofixup'
amend 'git commit --amend'
fixup 'git commit --fixup'
gb 'git branch'
gcan 'git commit --amend --no-edit'
gcm 'git commit -m'
gco 'git checkout'
gcobm 'git checkout master -b'
gcop 'git checkout -p'
gfa 'git fetch --all -p'
gkdm 'git kit del-merged'
gl 'git log --oneline --color --decorate -n20 --graph'
gp 'git pull --ff-only --all -p'
gpa 'git push -u akx HEAD'
gph 'git push -u origin HEAD'
gpuo 'git push -u origin'
gpv 'git push -u valohai HEAD'
gre 'git rebase'
grea 'git rebase --abort'
grec 'git rebase --continue'
grem 'git rebase -i master'
greom 'git rebase -i origin/master'
greum 'git rebase -i upstream/master'
grh 'git reset HEAD~'
gs 'git status'
gxa 'gitx --all'
namend 'git commit --amend --no-edit'
```
