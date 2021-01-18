# Git

## Clone all repositories from organisation

```shell
export GHORG="my-github-organisation"
mkdir ~/projects/$GHORG
cd ~/projects/$GHORG
gh api 'orgs/$GHORG/repos?private=true&per_page=1000' | jq -r '.[].ssh_url' | xargs -P $(nproc --all) -L1 git clone
```
