---
title: "【備忘録】Gitの初期設定から基本的な操作までのコマンドを自分用にまとめた"
emoji: "📝"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["git", "github", "備忘録"]
published: true
---

## はじめに
研究室貸与のPCが入れ替わったり仮想OSを入れたりでGitの環境構築をいちからやる機会が何度かあったのですが，Git完全に理解しているわけではない自分には大変でした．  
調べたらいくらでも情報は出てきますが，毎回複数の記事を横断して設定・使用をするのも大変なので，少なくとも自分のユースケースにおいてはこの記事さえ参照すればOKという備忘録的ドキュメントが欲しくなり，筆を執った次第です．  
自分みたいに，Gitは触ったことあるけど細かいコマンドを覚えていないって方はそれなりにいると思うので，そういった方にもこの記事が届けば幸いです．

この記事では適宜参考にした記事のリンク等を貼っておくので，より詳しい説明が見たい場合はソース元を確認してもらえればと思います．

### 対象読者
- Git初心者
    - この記事では「リポジトリ」等のカタカナGit用語の詳細な説明は行っていない．ふんわり単語の意味やGitの動作は理解しているが，細かいコマンドをまだ覚えられていない方向け．
- 個人開発をする人
- 新しいPCや仮想環境にGitの環境を構築したい人
    - 自分のユースケース(主に小規模な個人開発)では，このドキュメントに従って操作をしていれば困らないはず．

また，余談ですがコマンドライン上では`"Ctrl" + "C"`等のショートカットにコピー以外の操作が割り当てられていたりするので，コピーは`"Ctrl" + "Insert"`，ペーストは`"Shift" + "Insert"`を使用するのが無難かと思います．

## gitのインストール
今回は，最近環境を作ったVirtualBoxの仮想環境上のDebian GNU/Linuxにて，練習がてらGitの環境構築をしてみます．  
Linux環境なのでインストールはコマンドラインから行いましたが，Windowsなら[Git公式サイト](https://gitforwindows.org/)からでもインストールできます．Git bashとかもついてくるのでおすすめです．  

というわけでインストールをします．以下のコマンドを実行します．

```bash
$ sudo apt-get install git-all
```

一応ちゃんとインストールできたか確認します．

```bash
$ git --version
git version 2.39.5
```

`version 2.39.5`のGitがインストールされたみたい．

:::message
`apt`コマンドで入手した場合，少し古いバージョンがインストールされる場合が多いです．  
自分としてはこれでも困らないのでこのまま進めましたが，気になる方は最新バージョンのインストール方法を調べてみてください．
:::

これ以降のコマンド操作等は基本的にはOSを問わず共通かと思いますので，安心して読み進めてください．


## gitの初期設定をする
まずは初期設定を行います．  
自身のメールアドレスとユーザーネームを設定します．  
これはコミットログに残るので，分かるように設定してください．

```bash
$ git config --global user.email "hoge@example.com"
$ git config --global user.name "Your Name"
```

設定を確認するときは`$ git config`．  
色々オプションはありますが，とりあえずすべての設定を確認したいときは以下の通りです．

```bash
$ git config --list
```

## ローカルにあるディレクトリでGitを使う

Gitを使いたいディレクトリに移動します．

```bash
$ cd <移動先のパス>
```

移動先のディレクトリでGitリポジトリを作成(初期化)します．

```bash
$ git init
```

自分の環境だと以下のようなメッセージが表示されました．

```bash
hint: Using 'master' as the name for the initial branch. This default branch name
hint: is subject to change. To configure the initial branch name to use in all
hint: of your new repositories, which will suppress this warning, call:
hint: 
hint: 	git config --global init.defaultBranch <name>
hint: 
hint: Names commonly chosen instead of 'master' are 'main', 'trunk' and
hint: 'development'. The just-created branch can be renamed via this command:
hint: 
hint: 	git branch -m <name>
```

デフォルトだとブランチの名前の設定が`'master'`になっているよう．  
このままでもいいかもですが，slaveを連想させることから近年はmainを使うようになっているらしいです．  
GitHub上でリポジトリを作成したときもmainになるので，ここは`'main'`に変更しておきましょう．

```bash
$ git config --global init.defaultBranch main
```

これで，これ以降`git init`する際はブランチ名は`main`になります．

また，さっき作ってしまったmasterブランチをmainブランチに変更しておきましょう．以下のコマンドを打てばOKです．

```bash
$ git branch -m master main
```

https://qiita.com/fk_chang/items/a4839a595fef9a2c3724


次に，`README.md`ファイルと`.gitignore`ファイルを作ります．  
個人で使う分にはそこまで気にしなくて良いかもしれないですが，`README.md`ファイルにメモ程度でもどんなリポジトリ・プロジェクトなのかの説明を残しておいたら後から確認等しやすいですし，`.gitignore`ファイルをちゃんと書いておけばキャッシュ等がリモートリポジトリに入ることも無くなり，間違ってAPIキーを全世界公開してしまうことも無くなるのでなるべく用意したほうが良いと思います．

というわけで，以下のコマンドで`README.md`ファイルと`.gitignore`ファイルを作成します．  
普段はGUIでポチポチファイルを作っていますが，今回は練習がてらコマンドを打ってみました．(`touch`コマンドはLinuxのコマンドなので，Linux以外ではお使いの環境に合わせて操作をしてください)

```bash
$ touch README.md .gitignore
```

ファイルができたら，`README.md`にはリポジトリの説明を書いておきましょう．  

`.gitignore`には以下のリポジトリを参考に，使用言語に合わせた内容を記入(コピペ)しておきましょう．  
https://github.com/github/gitignore


## リモートリポジトリをローカルにcloneする
既にGitHub上に存在するリポジトリをローカルに持ってきて作業したい場合はcloneを行います．

```bash
$ git clone <リモートリポジトリURL>
```

Gitの設定ごと降ってくるので，新たに`$ git init`等は不要．いきなりcommitしてpushして大丈夫です．

## commitする
commitの前に，新規作成したファイルをステージングする必要があります．  
何もしないと新たに作成した/変更したファイルはcommitできないため，`$ git add`コマンドでステージング(commitするファイル群に追加)しましょう．

色々オプションがありますが，はじめのうちはとりあえず全てのファイルをステージングしてしまえばいいと思います．

```bash
$ git add --all
```

`--all`じゃなくて`-A`でも可．

これでやっとcommit準備完了！  
以下のコマンドを打ってcommitしましょう．  

```bash
$ git commit -m "コミットメッセージ"
```

なんとなくコミットメッセージは英語で書くものだと思い込んでいたけど，別にそんなことはなかったです．  
個人開発ならともかく，グループで開発するなら分かりやすいメッセージを心がけましょう．

ちなみに，`-m`オプションを付けずに単に`$ git commit`とすると，コミットメーッセージを書くためにエディタ(vimとか)が起動するので，そこから書き込むこともできます．

## リモートリポジトリにpushする
まずはローカルリポジトリをリモートリポジトリに接続します．

### リモートリポジトリを作成する

先に，GitHub上でリモートリポジトリを作成しておきます．  
GitHubで自分のページを開き，Repositories → New からリモートリポジトリを作成します．  

![](/images/20250526_git_memorandum.md/20250526_01.png)

リポジトリの名前を入力し(基本はローカルのディレクトリ名と同じものにするのが無難)，公開設定(Public)か非公開設定(Private)にするかを選択します．  
リモートリポジトリを先に作成する場合は，作成時に`README.md`ファイルや`.gitignore`ファイルを一緒に作ることもできます．    
準備ができたらCreate repositoryをクリックします．これでリモートリポジトリが作成されます．

![](/images/20250526_git_memorandum.md/20250526_02.png)

リポジトリを作成したら，リモートリポジトリのURLがコピーできるので，コピーします(httpsとsshが選べる．今回はhttpsで)．

![](/images/20250526_git_memorandum.md/20250526_03.png)

URLがコピーできたら，ターミナルに戻って以下のコマンドを打ちます．

```bash
$ git remote add origin <コピーしたリモートリポジトリのURL>
```

また，後ほど詳しく説明しますが`git push`する前に以下のコマンドを実行しておくと都合が良いです．

```bash
$ git config --global credential.helper store
```

続いて，リモートリポジトリにcommit内容をpushします．  
基本的には以下の通りです．

```bash
$ git push origin <ブランチ名>
```

`<ブランチ名>`のオプションを`HEAD`にすることで，ブランチ名を指定せず現在のブランチにpushするようにできます．

```bash
$ git push origin HEAD
```

そして，`-u`オプションを追記することで，以降のpushのオプションを省略して，`git push`のみでpushできるようになります．

```bash
#初回
$ git push -u origin HEAD

# 2回目以降
$ git push
```

また，gitをインストールして初めてpushする際は認証情報を求められると思います．  
この辺の挙動自分もちゃんとわかってなくて，Windowsでやったときはブラウザが開いてそれでログインしたらいけた気がするんですが，今回は以下のようにコマンドライン上で入力が求められたのでその通りにやっておきます．  

```bash
$ git push -u origin HEAD
Username for 'https://github.com': <自身のユーザーネーム>
Password for 'https://<自身のユーザーネーム>@github.com': <生成したアクセストークン>
```

ここで，Passwordのところははgithubアカウントのパスワードでなくアクセストークンなるものが必要になります．  
というわけで，次はアクセストークンの作り方を説明していきます．

### アクセストークンを生成する

GitHubの右上のアイコンからSettingを選び，左のメニューの一番下のDeveloper Settingsを選択して開きます．  
そして，Personal access tokensからFine-grained tokensを選択し,右上にあるGenerate new tokenを選択します(過去に生成したトークンがない場合は，画面の真ん中に生成ボタンが出てくるかも)．  

![](/images/20250526_git_memorandum.md/20250526_04.png)

そして，Token nameに適当な名前を入力してください．その名の通りトークン識別用の名前になります．  
また，Expirationからtokenの有効期限が設定できます．今回は個人で使うだけなので無期限(No expiration)を選択しましたが，きちんと有効期限を考えて設定したほうが望ましいとは思います．

![](/images/20250526_git_memorandum.md/20250526_05.png)

また，Repository accessからはprivate含む自分の全てのリポジトリにアクセスしたいのでAll repositoriesを選択します．

![](/images/20250526_git_memorandum.md/20250526_06.png)

次にPermissionsから各種許可を設定します．  
とりあえずRepository permissionsの中のContentsのAccessをRead and WriteにすればOK(~~この辺英単語が多くてオフチョベットしたテフみたいな状態になる~~)．

![](/images/20250526_git_memorandum.md/20250526_07.png)
![](/images/20250526_git_memorandum.md/20250526_08.png)

最後に，一番下のGenerate tokenを押してください．

![](/images/20250526_git_memorandum.md/20250526_09.png)

これでトークンが発行されるので，パスワードマネージャ等を用いて保管してください．

![](/images/20250526_git_memorandum.md/20250526_10.png)

:::message alert
ページを移動すると，生成したトークンは再表示できなくなるので要注意
:::

https://note.com/karanobu/n/nb8314bf386aa

このトークンを，先程の`<生成したアクセストークン>`のところにペーストして実行すればログインが完了すると思います．
```bash
Password for 'https://<自身のユーザーネーム>@github.com': <生成したアクセストークン>
```

また，デフォルトではpushするたびに認証情報が求められるので，git configに認証情報を追記しておきましょう．

ここで，最初の方にさらっと出てきた以下のコマンドが活躍します．  
このコマンドによって，git configに認証情報を追記することができます．

```bash
$ git config --global credential.helper store
```

そして，もう一度pushした際に認証情報を入力することで，以降はトークン入力をせずにpushできるようになります．(この記事通りに設定していれば，はじめに`$ git config --global credential.helper store`を打っているので，先ほどトークンを入力したところで情報が保存されます．)

```bash
$ git push
```

https://qiita.com/mtkhs/items/919264a9b04b9ecc5d5a



## ブランチの操作をする
個人開発ならずっとmainで作業していてもなんとかなるかもしれないですが，共同開発時はちゃんとブランチを分けて作業しましょう．  
間違ってもいきなりmainをいじくり回してはいけません．  
また，個人開発でも，mainブランチは常に安定して動作する状態にしておき，基本は開発用のブランチで作業をしておく等の使い分けをしたほうが望ましいです．  
自分も徹底できていないので心がけたい．

### ブランチの作成と基本操作
新しいブランチの作成は以下のとおりです．

```bash
$ git branch <作成するブランチ名>
```

作成したブランチへの切り替えは以下のとおりです．

```bash
$ git checkout <切り替え先のブランチ名>
```

ブランチ作成と切り替えを同時に行うときは，`-b`オプションを付けます．

```bash
$ git checkout -b <作成するブランチ名>
```

不要なブランチの削除は`-d`オプション．  
マージされていないブランチの場合は，`-d`オプションの代わりに`-D`オプションで強制削除できます．

```bash
$ git branch -d <削除するブランチ名>
```

ブランチの一覧の確認は以下のコマンドです．

```bash
$ git branch
```

また，`-a`オプションを付けるとリモートリポジトリも含め全てのブランチを確認できます．  
`-r`オプションはリモートブランチのみを一覧表示できます．

```bash
# 全てのブランチを確認
$ git branch -a

# リモートブランチを確認
$ git branch -r
```


### ブランチをリモートリポジトリにpushする
作成したブランチをpushしようとすると，リモートにその名前のブランチが無い！って怒らてしまいました．

```bash
$ git push
fatal: The current branch <ブランチ名> has no upstream branch.
To push the current branch and set the remote as upstream, use

    git push --set-upstream origin <ブランチ名>

To have this happen automatically for branches without a tracking
upstream, see 'push.autoSetupRemote' in 'git help config'.
```

エラーメッセージに従って，

```bash
$ git push --set-upstream origin <ブランチ名>
```

としてもいいですが，以下のコマンドを打つことでGitの設定としてpushする際に自動でリモートリポジトリを設定してくれるようになります．

```bash
$ git config --global push.autoSetupRemote true
```

### 作成したブランチの編集内容を他のブランチにmergeする

merge(マージ)される側にブランチを切り替え(`git checkout`)し，以下のコマンドを打つとmergeできます．

```bash
$ git merge <マージするブランチ>
```
ここで，コンフリクト(同じ行が編集されておりそのままmergeできない状態)が発生した場合はこんな感じになります．

```txt:コンフリクト時の表示例
<<<<<<< <現在のブランチ>
あいうえお
=======
ABCDE
>>>>>>> <マージするブランチ>
```

上下を見比べて不要な行を削除し，コンフリクトマーカー(`>>>>>>>` `<<<<<<<` `=======`)も削除しましょう．

VScodeを使っている場合，上に`Accept Current Cange`, `Accept Incomming Change`, `Accept Both Changes`, `Compare Changes`みたいなのが出てくると思います．これのいずれかを選択することで，その変更が反映されるようになります．例えば，`Accept Current Cange`なら元のブランチの記述のみが残るとおもいます．

![](/images/20250526_git_memorandum.md/20250526_11.png)

編集が完了したら，ステージング(`$ git add --all`)してコミット(`$ git commit -m "コミットメッセージ"`)しましょう．  
これで無事コンフリクトが解消できたはず．

ちなみに，`$ git status`で現在の状態(どのファイルがコンフリクトしているか等)がわかります．



## リモートリポジトリからfetch/pullする

簡単にfetchとpullの説明を．  
- fetch: リモートリポジトリの内容をローカルにもってくる．まだローカルのブランチにmergeはされていない．  
- pull: リモートリポジトリの内容をローカルのブランチにmergeする(fetch + merge)．

fetchとpullについては，個人的に以下の記事がわかりやすかったです．

https://qiita.com/wann/items/688bc17460a457104d7d

それぞれコマンドはこんな感じです．

```bash
$ git fetch origin <ブランチ名>
$ git pull origin <ブランチ名>
```

また，どちらも引数を省略しての実行も可能です．その場合は，今自分がいるブランチをfetch/pullします．

```bash
$ git fetch
$ git pull
```


## さいごに
いざ自分が使うコマンドを網羅的に書いてみると，結構なボリュームになってしまいました．  
先人が機能や操作ごとに記事を書いている理由が少しわかった気がします...  
しかし，こういった記事もどこかに需要があるはず(まさしく自分がこういう記事を欲していたので書いた)なので，誰かの助けになるのを願っています．  
また，技術的な説明のミス等があるかもしれませんので，何か指摘があればコメント欄等に書き込んでいただければと思います．  
ミスの修正やさらに使うようになったコマンドの追記等があれば，随時記事を更新しようと思います．