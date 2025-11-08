#remote 遠くの図書館リポジトリ
githubがこの図書館
ここに絵（ファイル）を置くと、みんなが見られる

#origin 図書館（リポジトリ）のニックネーム（名前）

#branch たくさんの紙に同時に絵を描ける
main 完成作品
feature まだ見せない練習の絵

#stage 絵を図書館に出す前に入れる魔法の箱

#comitte 魔法箱の中身をノートに書き込む


### 最初にファイルをGitHubに置く流れ

１フォルダに行く
cd "C:      ......."
２正しい図書館リモートを確認
git remote -v
３魔法箱に入れる
git add "  ......"ファイル名
４魔法箱の中身を確認
git status
５魔法ノートに記録
git commit -m "Add ファイル名"
６図書館に置く
git push origin main

### 二回目以降はこの4行だけ。

`cd "C:パス"
git add "フォルダ名/ファイル名.md"
git commit -m "Add ファイル名.md" 
git push`

意味：

1. **cd** – 作業フォルダに入る。
    
2. **git add** – 新しく作ったり直したファイルを登録。
    
3. **git commit** – 登録した内容をまとめる。
    
4. **git push** – まとめた内容をGitHubへ送る。



