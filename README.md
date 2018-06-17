# try_virtualenv

try_virtualenvという名の「ぼくのつかっているpython開発環境」の紹介みたいな感じになってる。結果から言うと`virtualenv`,`pyenv`,`Pycharm CE`で作業している。

## virtualenvについて
[virtualenv](https://packaging.python.org/guides/installing-using-pip-and-virtualenv/) はpythonの環境情報を一時的に定義しなおしてくれる。ただそれだけ。
プロジェクトごとにpython_pathを定義しなおすことによって，プロジェクト単位で依存関係をfixできる。`bundle install`や`npm install`,`composer install`みたいに。

ちなみに`import`にあたって直後に`python_path`が参照されるのではなく、次のような段階を経て参照しているらしい（検証はしていない）。

>読み込み時に検索する範囲
>
1. 実行ディレクトリと同ディレクトリ
2. カレントディレクトリ
3. 環境変数「PYTHONPATH」に列挙したディレクトリ
4. sys.pathに含むディレクトリ
>
>sys.pathは絶対パスの文字列リストであり、import sys; print sys.path 等の方法で確認できる
>引用：[Pythonのimportについてまとめる - Qiita](https://qiita.com/suzuki-hoge/items/f951d56290617df4279e)

>venv/virtualenvはPythonのデファクトスタンダードなので、各種ツールのサポートが充実しているのが強みになります。JetBrains社のPython IDEのPyCharmは、プロジェクトごとの環境設定で環境の選択ができますし、venvでプロジェクト以下に環境作ってからPyCharmで開くと、選ばなくてもデフォルトでvenv環境使ってくれるそうです。

```
$ python -m virtualenv | grep -A 2 -- --no-site-packages
--no-site-packages    DEPRECATED. Retained only for backward compatibility.
                      Not having access to global site-packages is now the
                      default behavior.
```

## かんけいない話

今回、pythonの環境整備のために`virtualenv`とか`pyenv`とか`direnv`とか`anyenv`がいいという情報を聞いていた。どれも聞いたことはあるけれど使ったことはないという情況で、安直にQiitaなどの記事を読み漁って環境構築を試みようとしていた。これが大幅なタイムロスを招いた。ついつい「口コミ」レベルの記事を宛にしてそれに倣おうとしがちである。僕はけして原著厨ではない。しかし「また聞き」レベルの記事を読み漁るくらいなら、オーナーのかいた公式のドキュメントを読んだほうが100000000000000000000000000000000000000000倍よりクリティカルでエッセンシャルな解説を得ることができる。

何がいいたいかというと１次ソースをよめ。

中には一次ソースを批判するような記事等も見つけたが、そういう人は外部サイトで批判してないで公式docにプルリク送ればいいのにね。混乱を招くだけでした。

## よんだやつ(一部)

[https://gist.github.com/alexhayes/cb1e6ad873c147502132ae17362a9daf]: [1]
[https://qiita.com/caad1229/items/325ca5c8ad198b0ebce7]: [2]
- https://github.com/direnv/direnv/wiki/Python
 - https://gist.github.com/alexhayes/cb1e6ad873c147502132ae17362a9daf
 - http://kujira16.hateblo.jp/entry/2017/10/02/150327
 - https://qiita.com/komorin0521/items/02d4097ac5e48243e0ec

- [pyenvが必要かどうかフローチャート - Qiita](https://qiita.com/shibukawa/items/0daab479a2fd2cb8a0e7)
