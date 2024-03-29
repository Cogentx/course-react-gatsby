# VSCode FAQ

Q: How can I set a custom Emmet expand keyboard shortcut?

A: In VS Code, you can go to File -> Preferences -> Keyboard Shortcuts (Windows, Linux) or Code -> Preferences -> Keyboard Shortcuts (Mac). Search for Emmet Expand Abbreviation and you should see the current keyboard shortcut there. Double-click on the current key-bind to set a new keyboard shortcut. I have mine set to Ctrl + E which I really like but you can pick any combo you like!

Q: How do I set PATH on Mac so I can run VSCode in terminal

A: In VSCode, open Command Palette (cmd + shift + p). Type in "path" and you will see a command to "Shell Command: Install 'Code' command in PATH"

Q: How to change a TAG and have closing tag change as well

A: VSCode extension - Auto Imports
![auto rename tag](../_img/vscode-ext-auto-rename-tag.png)

Q: How do I get auto imports working?

A: Auto Imports is now a VS Code built-in feature. Try `ctrl+space`. If that does not work then may need to add a **jsconfig.json** file to the project root (`checkJs` must be set to `true`). For example:

```json
{
  "compilerOptions": {
    "module": "commonjs",
    "target": "es2016",
    "jsx": "preserve",
    "checkJs": true,
    "strictFunctionTypes": true
  },
  "exclude": ["node_modules", "**/node_modules/*"]
}
```

Q: How do I paste a number range within  a string

A: VSCode extension - TextPastry





