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

## 【VSCode拡張機能の作り方】

基本的な作成方法は「[Your First Extension](https://code.visualstudio.com/api/get-started/your-first-extension)」にまとめっているのでこちらを参考にする。  
ものすごくざっくり説明すると、最低限必要になるのは、Node.jsとGit。  
あとは下記のコマンドを実行して、[Yeoman](https://yeoman.io/)と[VS Code Extension Generator](https://www.npmjs.com/package/generator-code)をInstallすれば準備完了。

```sh
> npm install -g yo generator-code
```

Installが完了したら、下記コマンドを実行することでテンプレートが作成される。

```sh
> yo code
```

## 【VSCode拡張機能のテスト実行】

`yo code`で拡張機能を生成すると、テストも一緒に生成されるがこれが曲者。  
Local環境で実行できたり、できなかったりとイマイチ挙動が安定しない。  

```sh
Warning: 'sandbox' is not in the list of known options, but still passed to Electron/Chromium.

[main 2021-05-25T10:16:05.799Z] Running extension tests from the command line is currently only supported if no other instance of Code is running.

[main 2021-05-25T10:16:05.801Z] Error: Running extension tests from the command line is currently only supported if no other instance of Code is running.
    at Z.claimInstance (/Users/ateae/workspace/defcommand-extensions/.vscode-test/vscode-darwin-1.56.2/Visual Studio Code.app/Contents/Resources/app/out/vs/code/electron-main/main.js:59:4914)
    at processTicksAndRejections (internal/process/task_queues.js:93:5)
    at async /Users/ateae/workspace/defcommand-extensions/.vscode-test/vscode-darwin-1.56.2/Visual Studio Code.app/Contents/Resources/app/out/vs/code/electron-main/main.js:59:1950
    at async Z.startup (/Users/ateae/workspace/defcommand-extensions/.vscode-test/vscode-darwin-1.56.2/Visual Studio Code.app/Contents/Resources/app/out/vs/code/electron-main/main.js:59:1821)

Exit code:   1
Done
```

上のようなErrorが出力され、テストが実行されない。  

> 現在、コマンドラインからの拡張機能テストの実行は、他のCodeのインスタンスが起動していない場合にのみサポートされています。
> エラーが発生しました。現在、コマンドラインからの拡張機能テストの実行は、他のCodeのインスタンスが起動していない場合にのみサポートされています。

ということらしい。ようするに他のInstanceが起動してる場合はテストが実行できない。  
不便極まりないが、これについては[公式](https://code.visualstudio.com/api/working-with-extensions/testing-extension#tips)と[Issue#112793](https://github.com/microsoft/vscode/issues/112793#issuecomment-748987123)で取り上げられている。  

具体的な解決策としては下記のようにする。

```ts
// runtest.ts
async function main() {
  try {
    const extensionDevelopmentPath = path.resolve(__dirname, '../../');
    const extensionTestsPath = path.resolve(__dirname, './suite/index');
    
    // ここでVersion "insiders"を指定する。
    await runTests({ extensionDevelopmentPath, extensionTestsPath, version: 'insiders'});
  } catch (err) {
    console.error('Failed to run tests');
    process.exit(1);
  }
}
```

`insiders`のVersionを指定することで、起動しているインスタンスと異なるインスタンスが起動するため、テストを実行できる。

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