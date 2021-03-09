# Visual Studio Code

## 【VSCode拡張機能のAPI】

VSCodeの拡張機能を作成する際に`vscode`Packageから提供されるAPIを使用して、拡張機能を作成していくことになる。
PackageをImportして、Suggestを頼りにしながらCodingをするだけでも、だいたいの拡張機能は実装できる。
ただし、より複雑なものを作ろうとすると、Contribution Pointに`when`の指定をしたり、`commands.executeCommand`からID指定で、直接組み込みコマンドを呼び出す必要がある。
そういった場合は下記のURLからAPIを調べること。

- [組み込みコマンド](https://code.visualstudio.com/api/references/commands)
- [when句の指定ID](https://code.visualstudio.com/api/references/when-clause-contexts)
- [package.jsonに組み込むManifest](https://code.visualstudio.com/api/references/extension-manifest)
- [組み込みアイコン](https://code.visualstudio.com/api/references/icons-in-labels)