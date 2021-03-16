# Python

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

## 【Python情報サイト】

- [python japan](https://www.python.jp/index.html)  
  Python japanの公式サイト.各種ドキュメントへのリンクもあるため、情報収集の足がかりとして非常に便利。
