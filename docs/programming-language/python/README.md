# Python

## 【Pythonの実行環境】

Pythonを使って具体的な開発を行う際に躓きやすいのが、実行環境の構築、いわゆる仮想環境とは何なのか？という部分だと思う。  

> Python を使って開発や実験を行うときは、用途に応じて専用の実行環境を作成し、切り替えて使用するのが一般的です。こういった、一時的に作成する実行環境を、**「仮想環境」** と言います。
> -- [python japan](https://www.python.jp/install/windows/venv.html#:~:text=Python%20%E3%82%92%E4%BD%BF%E3%81%A3%E3%81%A6%E9%96%8B%E7%99%BA,%E4%BB%AE%E6%83%B3%E7%92%B0%E5%A2%83%E3%80%8D%20%E3%81%A8%E8%A8%80%E3%81%84%E3%81%BE%E3%81%99%E3%80%82) より

Python Japanでも紹介されている通り、Pythonにおいて「仮想環境」を使用するのは**一般的**とされているので、当たり前過ぎで説明されないという状態になりやすい。  

仮想環境を作る目的は、

1. システム全体で使うPython環境に影響を与えずにモジュールの追加・入れ替えをしたい
2. 異なるバージョンの Python を使いわけたり、同じモジュールの、複数のバージョンを使い分けたい。

上のような理由から。いずれの場合も影響を**特定の範囲**に限定したい場合に使用される。  
開発において特定の範囲とは、**プロジェクト単位**とか、**モジュール単位**などに言い換えられる。  
より具体性をあげるのであれば、ファイルシステムを使用している以上、**特定のフォルダ**と言っても過言ではない。

長くなったが、要するに開発をする場合はプロジェクトや自分の作りたいものに応じてフォルダ分けをするのが一般的。  
フォルダ毎に仮想環境を構築してあげれば、フォルダ間の依存関係はなくプロジェクト毎に自由にモジュールを追加・入れ替えができて**最高**というのが仮想環境を構築する意味である。  
完全に同じというわけではないがNode.jsにおいて、package.json配置フォルダが一つのworkspaceとして認識されるようなもの。

python3には仮想環境構築用の標準ライブラリ`venv`が用意されているのでそれを使用する。  
仮想環境の生成と有効化には下記のコマンドを実行する。  

```console
# 仮想環境生成コマンド
python -m venv .venv

#　仮想環境の有効化
. .venv/bin/activate
```

## 【`if __name__ == '__main_'`のおまじない】

Pythonプログラムでよく見かける上の記述。  
おまじないで片付けられていることが多く、意外とそのUsecaseについて説明されているケースが少ない。  

## 【pipによるパッケージの管理】

Pythonのパッケージインストールには、`pip`を使用するが、これにもnpm同様に`package.json`のようにパッケージを管理するための方法が存在する。  
指定のファイルにInstallするパッケージの一覧を記載し、オプション指定で`pip install`を実行することにより、パッケージの回復を行うことができる。  

pipの[ドキュメント](https://pip.pypa.io/en/latest/cli/pip_install/#EXAMPLE%20REQUIREMENTS%20FILE) にも記載がある通り、ファイル名は何でもよいが、慣例的には、`requirements.txt`という名称が使われる。  

```txt
gspread >= 3.7.0
stopwatch.py >= 1.0.1
pylint >= 2.8.2
```

上記のように、Installしたいパッケージと対象のVersionを記載する。  
手書きで書いても、Install自体はされるが「めんどくさい」かつ「正確性に欠ける」ので、`freeze`コマンドを使うのがよい。  

```sh
pip freeze > requirements.txt
```

後は`-r`オプション付きでInstallを実行すれば、依存関係が解決されて対象のパッケージがインストールされる。  

```sh
> pip install -r requirements.txt
```

## 【関数に対する引数の指定方法】

関数に対する引数の指定方法は以外と言語による差がある部分。  
加えて、引数の数が多い場合や可変長引数を許可している関数の場合は、可読性にも直結するので引数の指定方法は覚えておいて損はなし。

```py
# 仮引数を指定する方法
def add(a, b, c):
    d = a + b + c
    return d

# 仮引数=実引数という書き方ができる
print(add(a="Hello", b="World", c="!!"))

# or 

# 仮引数を指定した場合、引き渡し順は何でも良い。
print(add(b="World", c="!!", a="Hello"))
```

## 【with文を使用したリソースの管理】

他の言語と同様に`file`などPCのリソースを消費する場合には、使用後にそのリソースを開放する必要がある。  
リソースの開放忘れはBugに繋がりやすい部分なので、Pythonにもリソース開放を楽にするための構文`with`が用意されている。  

try-finallyを使った場合

```py
file = open("./example.txt")
try:
    print(file.read())
finally:
    file.close()
```

withを使った場合

```py
with open("./example.txt") as file:
    print(file.read())
```

## Module, Package, Namespaceの違い

pythonに限った話ではないが、タイトルにあげられる「Module」、「Package」、「Namespace」はコードを物理的、論理的な領域にわけて管理するためよく使用される。  
pythonもこの3つ区分を使っており、これらはPythonの公式ドキュメント上で説明されている。  
<https://docs.python.org/ja/3/glossary.html>

### moduleとは？

Python においてコードを再利用する際の基本単位のこと。すなわち、他のコードから `import` されるひとかたまりのコード。  
(わかりにくければ、○○.pyファイルのことだと思えばよい。)  
<https://docs.python.org/3/glossary.html#term-module>

### packageとは?

複数のファイル、ディレクトリ構造からなっており、特定のルールに従ってひと固まりにまとめたもの。  
<https://docs.python.org/3/glossary.html#term-package>

## 【withに対応したクラスを定義する】

`with`文に対応したClassを生成するためには指定のメソッドをClassに対して実装しておく必要がある。  
これについては、[with文とコンテキストマネージャ](https://docs.python.org/ja/3/reference/datamodel.html#context-managers)の章で紹介されている。　　

## 【Python情報サイト】

- [python japan](https://www.python.jp/index.html)  
  Python japanの公式サイト.各種ドキュメントへのリンクもあるため、情報収集の足がかりとして非常に便利。
