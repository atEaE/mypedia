# Visual Studio Code

## 【VSCodeでKey操作のContext情報を確認方法】

VSCodeの拡張機能を作成する場合や、Settingsをカスタマイズする場合、Key Shortcutを変更する場合、操作したKeyのコンテキスト情報が知りたい場合がある。  
特にKey Shortcutを作成する場合は、`"when"`枠をこまめに設定しないと、簡単にShortcutを上書きして、キーマップを潰してしまうので非常に重要。  
Command Palletを開き、`Toggle Developer Tool`で開発者ツールをOpenする。このとき、Consoleに表示する状態にしておく。  
また、Command Palletを開き、今度は`Developer: Inspect Context Key`を選択する。  
すると、画像のようにカーソルで選択された箇所が赤くなるので、これでOK。  
![inspect key](./img/inspect%20key.png)
後は、知りたいキー操作を行うことで、ConsoleにContextが表示される。  
![context info](./img/context%20info.png)

## 【VSCode拡張機能のAPI】

VSCodeの拡張機能を作成する際に`vscode`Packageから提供されるAPIを使用して、拡張機能を作成していくことになる。
PackageをImportして、Suggestを頼りにしながらCodingをするだけでも、だいたいの拡張機能は実装できる。
ただし、より複雑なものを作ろうとすると、Contribution Pointに`when`の指定をしたり、`commands.executeCommand`からID指定で、直接組み込みコマンドを呼び出す必要がある。
そういった場合は下記のURLからAPIを調べること。

- [組み込みコマンド](https://code.visualstudio.com/api/references/commands)
  `vscode`Packageの`commands`Namespaceから参照できる限りのコマンドはごく少数で、ほぼ参照系。
  画面に通知メッセージを出したり、QuickAccessのメニューを出したりとかはできるが、それ以上となるとだいたいは組み込みコマンドを使うことになる。
  
  ```ts
  import * as vscode from 'vscode';

  vscode.commands.executeCommand('builtin.commandId', ...anyArguments);
  ```
  
  上のように第1引数にCommandIDを取り、第2引数以降にコマンドに応じて引数を渡す。
  このとき注意したいのは、第2引数は型が`any`なので、Build時のErrorは検知できない。
  
- [when句の指定ID](https://code.visualstudio.com/api/references/when-clause-contexts)
- [package.jsonに組み込むManifest](https://code.visualstudio.com/api/references/extension-manifest)
- [組み込みアイコン](https://code.visualstudio.com/api/references/icons-in-labels)