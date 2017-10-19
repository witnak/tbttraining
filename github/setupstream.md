# フォーク元の更新に追いつく

前提として、リポジトリのフォークとローカルへのクローンが完了していること。

1.まずブランチの確認を行う。
```
 $git branch -a
 *master
  remotes/origin/HEAD -> origin/master
  remotes/origin/master
```
2.リモートリポジトリとして、オリジナルのリポジトリを**upstream**という名前で設定。
```
 $git remote add upstream [フォーク元のURL].git
```
このリポジトリは**upstream**という名前で本家リポジトリを参照する。　※一度設定すれば再設定の必要は無し。

3.もう一度ブランチの確認。
```
 $git branch -a
  *master
   remotes/origin/HEAD -> origin/master
   remotes/origin/master
   remotes/upsteram/master
```
確認すると**remotes/upstream/master**が加わっている。

※環境によって、```git fetch upstream``` の操作を行ってからでないと出てこない場合がある。

4.フォーク元リポジトリの変更を取り出すために **fetch** と **merge** を使用する。

**fetch**　フォーク元リポジトリの情報を元にorigin/master（ローカルのリポジトリ）を更新。
```
 $git fetch upstream
```

**merge**　origin/master　の更新情報を　master（ローカルの作業ディレクトリ） にマージ
```
 $git merge upstream/master
```