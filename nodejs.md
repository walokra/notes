# Node.js

## Cleanup

Delete all node_modules

`find . -name node_modules -type d -prune`
OR
`LIST = $(mktemp); find . -name node_modules -type d > $LIST; $EDITOR $LIST && xargs -p rm -r < $LIST; rm $LIST`
