# Creating and Publishing Visual Studio Code Color Themes

Visual Studio Code has this in-built color theme called **Quiet Light** which I absolutely love, as I prefer using mostly light themes when coding in the day, when my room is filled with light.

Yesterday after a long long time I was coding in darkness and wanted to switch to a dark theme. I don't remember, in the past which themes I have mostly used for Dark Mode, and I was really yearning for a theme that would be similar to **Quiet Light** in terms of the color palette. While I did find [this one extension](https://marketplace.visualstudio.com/items?itemName=JosphatGitogoSangara.quiet-dark) claiming to implement dark version of Quiet Light, it didn't actually quite live up to the expectation.

As usually happens during such yak shaving sessions, I started off with trying to edit that theme's json file, and finally ended up considering publishing my own theme to the [VS Code Marketplace](https://marketplace.visualstudio.com). So here are the steps to make your own theme and publish them.

## Create a VS Code Extension Package

VS Code team have made a `yeoman generator` for creating VS Code extensions. Start from there.

```bash
❯ npm install -g yo generator-code
❯ yo code
```

![my answers for the yo code wizard](https://cdn.hashnode.com/res/hashnode/image/upload/v1671616957924/yNV_-IGqv.png align="center")

This is what the structure of the newly created project look like

```bash
.
├── CHANGELOG.md
├── LICENSE.md
├── README.md
├── icon.png          # add an icon for marketplace listing
├── package.json
├── quieter-dark-color-theme-0.0.1.vsix
├── screenshot.png    # add a screenshot for marketplace
├── themes
│   └── Quieter Dark-color-theme.json
└── vsc-extension-quickstart.md
```

> Make sure the theme file ends with `-color-theme.json` and then VS Code will give you code completion help when editing it.

There are two main things inside a theme, `colors` and `tokenColors`

*   **colors** define background, foreground colors for elements of the UI of VS Code itself, including sidebars, status bars, editor panes, tooltips etc
    
*   **tokenColors** define syntax highlighting, like classes, functions, keywords etc.
    

Once you have created the theme as per your liking, you can move it (or symlink it) into `~/.vscode/extensions/` to have it installed to your local IDE and try it out. Once you are satisfied, you can move on to publishing it.

> You'll find more detailed documentation on creating color themes in the official docs  
> [https://code.visualstudio.com/api/extension-guides/color-them](https://code.visualstudio.com/api/extension-guides/color-them)

## Publishing a Visual Studio Code Color Theme to the Marketplace

Before we go to the further steps, keep `vsce` (the VS Code Extension manager CLI) installed.

```bash
npm install -g @vscode/vsce
```

Before you can publish, you need to ensure a few steps.

1.  Create a Azure Devops organisation if you do not have one. [(Go here)](https://learn.microsoft.com/azure/devops/organizations/accounts/create-organization)
    
2.  Get a Personal Access Token inside that organisation. This PAT must have `Marketplace > Manage` scope in it.
    
    ![Personal settings menu](https://code.visualstudio.com/assets/api/working-with-extensions/publishing-extension/token1.png align="left")
    
    ![Personal settings menu](https://code.visualstudio.com/assets/api/working-with-extensions/publishing-extension/token1.png align="left")
    
3.  Save this personal access token somewhere. I prefer having it saved in my `~/.profile` file as `VSC_MARKET_PAT=xxxxxxxxx` so I can use the env variable `$VSC_MARKET_PAT` wherever I need it.
    
4.  Create a VS Code Marketplace **publisher**. *Note this is important to remember what publisher* ***name*** *you make here as you need to add it to your package.json too.* [(Go here to make this)](https://marketplace.visualstudio.com/manage)
    
5.  Check that the publisher you made is working by doing `vsce login <publisher name>` in your terminal, and making sure the login works.
    

Make sure you add a `name` and a `publisher` to your package.json before proceeding.

To publish your extension run

```bash
vsce publish
```

You can also specify a version of your extension, when making changes and updating it

```bash
vsce publish 0.0.1 
# or 
vsce publish minor # 0.1.0 increment 
# or 
vsce publish major # 1.0.0 increment
```

> You'll get more detailed information including how to disable your extension or deprecate it, and get analytics data of it in the official docs  
> [https://code.visualstudio.com/api/working-with-extensions/publishing-extension](https://code.visualstudio.com/api/working-with-extensions/publishing-extension)

## Links and References

*   [https://github.com/championswimmer/vscode-theme-quieter-dark](https://github.com/championswimmer/vscode-theme-quieter-dark)  
    source code of the extension I made  
    
    %[https://github.com/championswimmer/vscode-theme-quieter-dark] 
    
*   [https://marketplace.visualstudio.com/items?itemName=championswimmer.quieter-dark-color-theme](https://marketplace.visualstudio.com/items?itemName=championswimmer.quieter-dark-color-theme)  
    published link of the extension  
    
    %[https://marketplace.visualstudio.com/items?itemName=championswimmer.quieter-dark-color-theme]