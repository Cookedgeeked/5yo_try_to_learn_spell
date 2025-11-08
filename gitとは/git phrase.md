#remote 遠くの図書館リポジトリ
githubがこの図書館
ここに絵（ファイル）を置くと、みんなが見られる

#origin 図書館（リポジトリ）のニックネーム（名前）

#branch たくさんの紙に同時に絵を描ける
main 完成作品
feature まだ見せない練習の絵

#stage 絵を図書館に出す前に入れる魔法の箱

#comitte 魔法箱の中身をノートに書き込む


### ファイルをGitHubに置く流れ

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
