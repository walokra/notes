# macOS

## VS Code’s in-built terminal is slow or laggy on MacOS Big Sur

```bash
codesign --remove-signature /Applications/Visual\ Studio\ Code.app/Contents/Frameworks/Code\ Helper\ \(Renderer\).app
```

See: <https://shageevan.medium.com/vs-codes-in-built-terminal-is-slow-or-laggy-on-macos-big-sur-d5867a7d9771>


## Delete DS_Store

```
find . -type f -name .DS_Store -exec rm -i {} \;
```
