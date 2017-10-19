# フォーク元の更新に追いつく

前提として  
  ・ Gitのインストール  
  ・ リポジトリをフォーク  
  ・ ローカルへのクローン  
が完了していること。

1. `git branch -a` を実行しbranchの状態を確認します。
以下のようになっています。
```
$ git branch -a
* master
  remotes/origin/HEAD -> origin/master
  remotes/origin/master

```

2. リモートリポジトリとして、 upstream という名前で設定します。
```

$ git remote add upstream https://github.com/witnak/tbttraining.git

```

3. 再度`git branch -a` を実行しbranchの状態を確認します。
```
$ git branch -a
* master
  remotes/origin/HEAD -> origin/master
  remotes/origin/master
  remotes/upstream/master

```
`remotes/upstream/master`という行が追加されているのがわかります。

4. おおもとのリポジトリからローカルリポジトリに変更を適用するため、fetchとmergeを行います。
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
  この作業だけだとデータを取り出しただけで何も行われません。

### merge
  mergeをすることで取り出したデータをローカルリポジトリに反映します。  
  変更されたものがない場合は、`Already up-to-date.`だけが出力されます。

### pull
  fetch・mergeで行う事をpullで一括で行います。

