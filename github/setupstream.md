# フォーク元の更新に追いつく

前提として  

* リポジトリをフォーク  
* ローカルへのクローン  

が完了していること。  

1. まずブランチの確認を行う。
```
 $git branch -a
 *master
  remotes/origin/HEAD -> origin/master
  remotes/origin/master
```

2. リモートリポジトリとして、 upstream という名前で設定します。
```
$ git remote add upstream https://github.com/witnak/tbttraining.git
```
　このリポジトリは**upstream**という名前で本家リポジトリを参照する。　※一度設定すれば再設定の必要は無し。

3. もう一度ブランチの確認。
```
 $git branch -a
  *master
   remotes/origin/HEAD -> origin/master
   remotes/origin/master
   remotes/upsteram/master
```
確認すると　**remotes/upstream/master**　が加わっている。  
環境によって、```git fetch upstream``` の操作を行ってからでないと出てこない場合がある。　　

4. フォーク元リポジトリの変更を取り出すために **fetch** と **merge** を使用する。
  **fetch**　フォーク元リポジトリの情報を元にorigin/master（ローカルのリポジトリ）を更新。
```
 $git fetch upstream
 remote: Counting objects: 1, done.
 remote: Total 1 (delta 0), reused 1 (delta 0)
 Unpacking objects: 100% (1/1), done.
 From [フォーク元のURL]
  4d531a2..0d7c835  master     -> upstream/master
```
**merge**　origin/master　の更新情報を　master（ローカルの作業ディレクトリ） にマージ
```
 $git merge upstream/master
 readme.md | 3 ++-
 1 file changed, 2 insertions(+), 1 deletion(-)
```
変更がない場合は"Already up-to-date."と表示される。

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
