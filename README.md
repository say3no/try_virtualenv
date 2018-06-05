# try_virtualenv

仕事でPythonを触る機会が増えたため、開発環境を整理する目的でvirtualenvを活用したい。その実態を掴むためにちょっと触ってみるその記録。

まだpythonの開発環境の管理というか、ライフサイクルがわかっていない。

たとえばjsなら`git pull`して`npm install`して`npm start`でいい。phpなら`composer install`して`php -s`なわけだ。この実行環境がlocalhostだったりコンテナだったりリモートホストだったりdocker-composeだったりっていうマシンリソースの場所というのはその後考えればいい。
ミドルウェアより上のレイヤの話をしたい。

ではpythonではどうなんだろう。pythonの2とか3とかのverは今回考慮しないとすると、`pip install -r requirements.txt -t ./site-packages/`で良いのかと思われた。しかし、composerのautoloaderみたいな仕組みや`package.json`みたいな仕組みが見当たらない。つまり、pythonでは依存関係のあるパッケージへのパスなりネームスペースの処理というのはどういうプラクティスがあるのかがわからない。

で、pythonがどういうふうにimportするディレクトリを参照しているのかまだ良くわかっていないんだけど、コードの中で以下の様に叩けばimport先として親ディレクトリが見れる。

```python
import sys
sys.path.append('../')
```

[Pythonのimportについてまとめる - Qiita](https://qiita.com/suzuki-hoge/items/f951d56290617df4279e)によれば、次の順序にsearchするようだ。

>読み込み時に検索する範囲
>
1. 実行ディレクトリと同ディレクトリ
2. カレントディレクトリ
3. 環境変数「PYTHONPATH」に列挙したディレクトリ
4. sys.pathに含むディレクトリ
>
>sys.pathは絶対パスの文字列リストであり、import sys; print sys.path 等の方法で確認できる

ここでようやく職場の先輩が推奨していたpyenv+virtualenv+direnvで環境を作ると良いみたいな言葉の理解がわずかに進む。要するにdirenvでリポジトリごとに`PYTHONPATH`を変更すればよいということなのだろう。


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






