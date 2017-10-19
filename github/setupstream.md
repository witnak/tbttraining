# フォーク元の更新に追いつく

前提として
  ・ Gitのインストール
  ・ リポジトリをフォーク
  ・ ローカルへのクローン
が完了していること。

0. `git branch -a` を実行しbranchの状態を確認します。
以下のようになっています。
```
$ git branch -a
* master
  remotes/origin/HEAD -> origin/master
  remotes/origin/master

```

0. リモートリポジトリとして、 upstream という名前で設定します。
```

$ git remote add upstream https://github.com/witnak/tbttraining.git

```

0. 再度`git branch -a` を実行しbranchの状態を確認します。
```
$ git branch -a
* master
  remotes/origin/HEAD -> origin/master
  remotes/origin/master
  remotes/upstream/master

```
`remotes/upstream/master`という行が追加されているのがわかります。

0. おおもとのリポジトリからローカルリポジトリに変更を適用するため、fetchとmergeを行います。
まずはfetchから
```
$ git fetch upstream

```
続いて、mergeをします。
```
$ git merge upstream/master

```
ここで、対象になるファイルを開いたままにしていると、
`Unlink of file`といったエラーが出てくることがあります。


## fetch merge pullについて
### fetch
  リモートリポジトリからデータを取り出します。  
  `$git fetch 【対象ブランチ名】`  
  この作業だけだとデータを取り出しただけで何も行われません。
  fetchしたら、mergeが必要です。

### merge
  fetchを実行したのち、mergeをすることで取り出したデータをローカルリポジトリに反映します。  
  `$git merge 【対象ブランチ（git branchで出力されるremotes/以降のブランチ名）】`  
  変更されたものがない場合は、`Already up-to-date.`だけが出力されます。

### pull
  fetch・mergeで行う事をpullで一括で行います。  
  `$git pull`  
  このコマンドでmergeまでやってくれます。  
  ただし、マージしたくないものまでマージされるので注意が必要。

