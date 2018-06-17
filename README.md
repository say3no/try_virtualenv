# try_virtualenv

try_virtualenvという名の「ぼくのつかっているpython開発環境」の紹介みたいな感じになってる。

結果から言うと`pip`,`virtualenv`,`pyenv`,`Pycharm CE`で作業している。

## virtualenvについて
今の自分の理解で説明するとこうだ。virtualenvはpython_pathを一時的に定義しなおしてくれる。ただそれだけ。
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


## それはそれとしてvirtualenvの話をしようぜ
でまあこんな感じで実行する。

```
cd
sudo pip install virtualenv #brewでやったかもしれない
virtualenv --nosite-packages hoge
cd hoge
source ./bin/activate
```
./bin/activateすると指定の仮想環境になる…というよりはpythonとそのsite-packagesのパスを変えてるっぽい？
python本体は重複せずにpyenvで管理して、site-packagesだけをリポジトリの下においてPYTHONPATHにそれを追加する…みたいなことをやっているっぽい。

調べてみるといろいろと解決手法が与えられているらしい。
 - https://github.com/direnv/direnv/wiki/Python
 - https://gist.github.com/alexhayes/cb1e6ad873c147502132ae17362a9daf
 - http://kujira16.hateblo.jp/entry/2017/10/02/150327
 - https://qiita.com/komorin0521/items/02d4097ac5e48243e0ec

最新版あたりのpython3ならばこのあたりの問題をオフィシャルで対応しているらしいのだがpython2系が主戦場らしい我が身…。依存関係を整理するためにいくつもツールを入れるっていう態度が好きではない。じゃあもうver毎のpythonコンテナ作ってしまうのがいいのだろうか…とあさっているとこういう記事にもぶつかった。

- [pyenvが必要かどうかフローチャート - Qiita](https://qiita.com/shibukawa/items/0daab479a2fd2cb8a0e7)

のっけからパンチのあるツイートを引用している。しかし一理ある。そもそも必要かどうかという点を問い直すべきだろう。重要なのは依存関係を明示してCI/CDしやすいような独立した状況を作ることで、七面倒なツールをいれまくることではないのだ。

>venv/virtualenvはPythonのデファクトスタンダードなので、各種ツールのサポートが充実しているのが強みになります。JetBrains社のPython IDEのPyCharmは、プロジェクトごとの環境設定で環境の選択ができますし、venvでプロジェクト以下に環境作ってからPyCharmで開くと、選ばなくてもデフォルトでvenv環境使ってくれるそうです。

有力情報。CEでもできるならもうこれで終わりにしたい。2.7.xでもできるといいのだけど。

>環境設定で「pyenv」「pyenv」と書かれている人に知っておいて欲しいことは2つ
処理系のバージョン指定はvirtualenvでもできるしpyenvのpython-buildでインストールだけすれば十分。virtualenv(pyvenv)が今後の標準。レールに乗ろう。
**PythonにRailsはない。繰り返す。PythonにRailsはない。Pythonはnode.jsでもない。**

はい…。


## virtualenv

[公式のドキュメントが一番だよね。](https://packaging.python.org/guides/installing-using-pip-and-virtualenv/)

```bash
$ mkdir try_virtualenv
$ cd try_virtualenv
$ sudo pip install virtualenv
$ python -m virtualenv hogehoge #仮想環境`hogehoge`の作成
$ which python
 /Users/say3no/.pyenv/shims/python
$ source hogehgoe/bin/activate
$ which python
 Users/say3no/try/try_virtualenv/hogehgoe/bin/python
```

virtualenvのやっていることはpythonのパスとpython_pathの変更？

 - virtualenvのverについて
```
$ python -m virtualenv --version
```

##  依存パッケージについて

### no-site-packages
`---no-site-packages`でglobalなsite-packagesをpython_pathから外すのだろうか…？
```bash
$ python -m virtualenv no-site --no-site-packages  # `--no-site-packages`🐜の場合
$ source no-site/bin/activate
$ python show_python_path.py | grep site-packages
/Users/say3no/try/try_virtualenv/no-site/lib/python2.7/site-packages

$ deactivate

$ python -m virtualenv yes-site
$ source yes-site/bin/activate
$ python show_python_path.py | grep site-packages
/Users/say3no/try/try_virtualenv/no-site/lib/python2.7/site-packages
```

変化ないやんけ！！！！なんでや！！！

```
$ python -m virtualenv | grep -A 2 -- --no-site-packages
--no-site-packages    DEPRECATED. Retained only for backward compatibility.
                      Not having access to global site-packages is now the
                      default behavior.
```

コンパチのために残してあって、いまだとデフォでglobal site-packagesにアクセスできないらしい。
やっぱり一次ソースが一番大事なんやなって。


[https://gist.github.com/alexhayes/cb1e6ad873c147502132ae17362a9daf]: [1]
[https://qiita.com/caad1229/items/325ca5c8ad198b0ebce7]: [2]

