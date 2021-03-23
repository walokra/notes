# Node.js

## Cleanup

Delete all node_modules

`find . -name node_modules -type d -prune`
OR
`export LIST=$(mktemp); find . -name node_modules -type d -prune >| $LIST; $EDITOR $LIST && xargs -p rm -r < $LIST; rm $LIST`
