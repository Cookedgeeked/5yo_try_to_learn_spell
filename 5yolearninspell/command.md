
### ==DHCPの詳細情報はここからは見えない==
- 「DHCPサーバーのIP」や「リース時間」などはこのコマンドでは出てこない。
- もし知りたければ：
`sudo dhclient -v enp0s3`
 **`dhclient`**
- これはDHCPクライアント。
- 「宇宙のIPを借りたい！」とネットワークに向かって叫ぶ者。
2. **`-v`**
- verbose（詳細表示）モード。ネットワークとのやり取りを全部見せてもらうモード。交渉の内容（DISCOVER → OFFER → REQUEST → ACK）を可視化してくれる。
2. **`enp0s3`**
- どのネットワークインターフェースに向かって交渉するか。
...
DHCPDISCOVER on enp0s3 to 255.255.255.255 port 67 interval 6
DHCPOFFER of 10.0.2.15 from 10.0.2.2
DHCPREQUEST for 10.0.2.15 on enp0s3 to 255.255.255.255 port 67
DHCPACK of 10.0.2.15 from 10.0.2.2
2. あなたのVMが受け取ったIP
- ここで、**10.0.2.15** があなたのVMに割り当てられるIP。
- 10.0.2.2 は **DHCPサーバーの存在**。
VM「ねえ、IP貸して！」 → `DHCPDISCOVER`
サーバー「これどう？」 → `DHCPOFFER 10.0.2.15`
 VM「じゃあそれで！」 → `DHCPREQUEST`
 サーバー「オッケー、あなたのものね」 → `DHCPACK`

結果として、「自分のIP」と「DHCPサーバーのIP」を確認できる





hydra -L users.txt -P cewl.txt dc-2 http-form-post '/wp-login.php:log=^USER^&pwd=^PASS^&wp-submit=Log In&testcookie=1:S=Location'  
==このくそ長いコマンドは何なの一個一個の意味おしえてあとこんなの覚えられないよ== 

hydra`
- ツール本体。
`dc-2`
- ターゲットのホスト名またはIP。
`http-form-post`
- 攻撃モジュール。HTTPのフォーム送信でログインを試すモード。
`'/wp-login.php:log=^USER^&pwd=^PASS^&wp-submit=Log In&testcookie=1:S=Location'`
- モジュールに渡す「フォーム仕様」文字列。`:`で区切られた**3つのパート構成**
①`/wp-login.php`
- ログインフォームへのパス（ターゲットのルートからの相対パス）
②`log=^USER^&pwd=^PASS^&wp-submit=Log In&testcookie=1`
- POST ボディ（フォームデータウェブサイトに手紙（POSTデータ）を送るとき、手紙の**中身**の部分のことを「POSTボディ」っていうよ。）。`^USER^` と `^PASS^` がそれぞれユーザ/パスワードの置き換え箇所。`log=^USER^`  
ユーザ名フィールド。HTMLの`<input name="log">`に対応。`^USER^`はhydraがユーザ名で置き換えるプレースホルダ。
`pwd=^PASS^`  
パスワードフィールド。HTMLの`<input name="pwd">`に対応。`^PASS^`はhydraがパスワードで置き換えるプレースホルダ。
- users.txt に `taro` がある
- cewl.txt に `apple123` がある。
この組み合わせを試すと、ツールはひな形をこう変える：  
`log=taro&pwd=apple123`
これがサーバに送られるPOSTデータ（フォームの中身）。
**POST** は英語で「投函する」「手紙を出す」という意味から来てる。  
→ 「手紙の中身をウェブサイトに投げる」ってイメージ
- `&` 繋ぐ。
③`S=Location`
- 「成功判定」。レスポンスに `Location` ヘッダが含まれていたら成功と見る（ェブサイトにログインするとき、サーバはログインに**成功した人**を別のページに移動させることがある。  
そのときサーバは「次はこのページに行ってね」と教えるために、**HTTPヘッダ**の中に

`Location: 〜〜〜`
という行を入れて送る。
だからHydra（ハイドラ）はこう判断する：
 「サーバの返事に `Location` っていうメッセージが入ってたら、ログインできたってことだな！」 `S=` は Success 条件）
フォーム文字列は ①path②body③:判定` の形


**ログインフォームの仕組み**
name value input や <button> タグの属性これらの意味を教えてどういう役割なのかも
分かりやすく整理する。
 1. `<input>` タグ
フォームでユーザから情報を受け取るためのタグ。種類（`type`）で振る舞いが変わる。
主な属性と意味
|属性|役割|
|`type`|入力の種類を指定 (`text`, `password`, `submit`, `hidden` など)|
|`name`|サーバに送るときの「キー名」。必須。例: `name="log"`|
|`value`|**デフォルト値や送信時に送る値**。`submit` の場合はボタンに表示される文字も兼ねる|
例
html
<input type="text" name="log" value="">
<input type="password" name="pwd">
<input type="hidden" name="testcookie" value="1">
<input type="submit" name="wp-submit" value="Log In">

 `log` と `pwd` はユーザ名とパスワードとしてサーバに送られる。
- `testcookie` はユーザ入力なしで送られる固定値（サーバがクッキー利用可否を確認）。
- `wp-submit` はボタンが押されたことをサーバに伝える。
2. `<button>` タグ
ボタンを作るタグ。`<input type="submit">` と似た役割だが、中に表示文字やHTMLを自由に書ける。
#### 属性と意味
|属性|役割|
|`type`|ボタンの種類 (`submit`, `button`, `reset`)|
|`name`|サーバに送るキー名|
|`value`|サーバに送る値（送信時にnameとセットで送られる）|
例
```html
<button type="submit" name="wp-submit" value="Log In">Log In</button>
- `<input type="submit">` と同じく、押されたらフォーム送信。
### 3. まとめ（役割感覚）
- `name` = 「サーバに送るラベル」。必須。
- `value` = 「そのラベルに対して送る値」。
- `<input>` や `<button>` の種類で挙動が変わる（入力欄、隠し値、送信ボタンなど）。
- 
デフォルト値や送信時に送る値どういうこと- **デフォルト値** → 入力欄に何も書かなかったときに送られる値
    
- **送信時に送る値** → ボタンやhiddenのように、ユーザが触らなくても必ず送られる値「デフォルト値や送信時に送る値」というのは、**箱の中に書かれている手紙（value）のこと**- **value = 箱の中の手紙の中身**
    
- **デフォルト値 = 箱に最初から入っている手紙の内容**
    
- **送信時に送る値 = ボタンや隠し箱の中に入っている、必ず送られる手紙**
簡単に言うと `value` は **そのフォーム項目がサーバに送る「中身」** です。

*- **value = 箱の中の手紙の中身**
    - `<input>` や `<button>` の **`value` 属性** = **その項目が送るデータの中身**
    
- **デフォルト値**
- **デフォルト値 = 箱に最初から入っている手紙の内容**
    
- **送信時に送る値 = ボタンや隠し箱の中に入っている、必ず送られる手紙***
- 普通の入力欄 (`type="text"` や `type="password"`)
    - ユーザが入力した文字列が送られる。
    - でも `<input type="text" name="log" value="default">` の場合は、入力しなければ `"default"` が送られる → これが「デフォルト値」。
- ボタン (`type="submit"` や `<button>`)
    - ボタンを押すと、サーバに `name=value` が送られる。
    - 例: `<input type="submit" name="wp-submit" value="Log In">`
        - 押すと `wp-submit=Log In` が送られる。
        - これは「送信時に送る値」。ユーザが変えられない固定値。
- hidden（隠しフィールド）
    - `<input type="hidden" name="testcookie" value="1">`
    - ユーザに見えないけど、フォームを送信すると必ず `testcookie=1` がサーバに送られる。
要するに **`value` はその項目が持つ「送るデータ本体」** で、
- 入力欄ならユーザ入力値
- ボタンやhiddenなら固定値をサーバに渡すためのもの、と覚えればOK。
タグ　しるし
</h1> <form name="loginform" id="loginform" action="[http://dc-2/wp-login.php](view-source:http://dc-2/wp-login.php)" method="post"> <p> <label for="user_login">Username or Email Address<br /> <input type="text" name="log" id="user_login" class="input" value="" size="20" /></label> </p> <p> <label for="user_pass">Password<br /> <input type="password" name="pwd" id="user_pass" class="input" value="" size="20" /></label> </p> <p class="forgetmenot"<label for="rememberme"><input name="rememberme" type="checkbox" id="rememberme" value="forever" /> Remember Me</label></p> <p class="submit"> <input type="submit" name="wp-submit" id="wp-submit" class="button button-primary button-large" value="Log In" /> <input type="hidden" name="redirect_to" value="http://192.168.56.104/wp-admin/" /> <input type="hidden" name="testcookie" value="1" /> </p> </form> <p id="nav"> 

1. `wp-submit=Log In` が何をしているか
- これは**送信ボタンの name=value** そのままをPOSTで送っているだけ。
1. `testcookie=1` が何をしているか
- これは **クッキー利用可否チェック用の隠しフィールド**。、**クッキーはタロウくんが誰かを教えてあげる魔法のお手紙**
- 目的はサーバが「このクライアントがクッキーを受け入れるか」を確認すること。
- 実際の流れ（簡略）：ページ表示時にサーバがテスト用のクッキーをセットする。フォーム送信時にその hidden フィールドが一緒に来る。サーバは受信時にテスト用クッキーが存在するかを確認して、クッキー不可ならログイン処理を拒否したり別の挙動をとる
- クッキーは **「この子は遊んでいいよ」っていう魔法の名前札**、ひみつメモは **「名前札持ってるよ！」って知らせる小さな手紙**
- だから hydra でログイン試行するなら、この hidden フィールドを含めて送る方が実際のブラウザと同じ振る舞いになり成功判定が正しく働く。省くとサーバが別挙動をして「失敗」と判断される可能性がある。







==$ cat pass.txt | grep -P 'sh$'==
### `-P` の意味と語源（由来）
- `-P`
-は **Perl-compatible regular expressions**（PCRE）を使うためのフラグ。
- **Perl-compatible（Perl互換）**
- Perl というプログラミング言語で作られた、超強力な正規表現の書き方に合わせていること。
- `sh$` → 「行の最後が sh で終わる行」という意味の呪文。regular expressions（正規表現）

`ssh webadmin@$IP` は **SSHでリモートに接続するときに「webadmin」というユーザー名で `$IP`（相手のIPアドレス）に入る** 命令だよ
- `ssh`：安全にリモートのマシンに入るためのコマンド（Secure SHell）。
- `webadmin@`：接続先で使う**ユーザー名**。ここで指定したアカウントでログインを試みる。
- `$IP`：接続先の**ホスト名**か**IPアドレス**。環境変数の形で書いているだけで、実行時は具体的な値に展開される。
base64 -d
base64：文字列を Base64 形式に変換したり戻したりするコマンド
-d：デコード（decode）するためのオプション


スクリプトを取る方法
dirscript -f ~/vulnhub/napping/session-$(date +%F_%H%M%S).log

終了したいときは `exit` か `Ctrl+D`。

==`os.system('/usr/bin/bash /dev/shm/revshell.sh')` の意味（一行ごとに）==
- `os.system(...)`
     **Python** の関数で、引数に渡した文字列をシェル経由で実行する命令。
- `'/usr/bin/bash /dev/shm/revshell.sh'`
    - `/usr/bin/bash` → これは **bashシェルの実行ファイル**へのフルパス。
    - `/dev/shm/revshell.sh` → これは **スクリプトファイルへのパス**（ **bash を使ってそのスクリプトを実行する** という意味になる。

### 6) 実行権限を付ける（必要なら）
```bash
chmod +x /path/to/your/script.sh
```




==echo "C%40ughtm3napping123" | php -r "echo urldecode(file_get_contents('php://stdin'));"　このコマンドを一つ一つ説明して==
#① `echo "C%40ughtm3napping123"`- **`echo "C%40ughtm3napping123"`** → 「外の世界から手紙を送る魔法のチューブ」「魔法の杖で手紙を唱える」
- **意味**：「この文字を画面に見せてね！」
#② `|`   「魔法のチューブをプログラムに繋ぐ」「杖で出した言葉を、魔法のチューブに流す」
 「紙に書いた文字を、隣の魔法使いphpにそっと手渡す小道」を作る感じ。
#③ `php -r "..."`  「プログラムの中で魔法を使う場所」
- **意味**：PHP という魔法の道具を使って、中の呪文（`"..."`）を実行する。
- `-r` は「ファイルに書かなくても、ここに書いた呪文をすぐ実行するよ！」って意味。
 「紙を渡された魔法使いが、すぐに呪文を唱える」感じ。
#④ `file_get_contents('php://stdin')` 「その魔法のチューブの中身をぜーんぶ吸い上げるストロー」
- **意味**：「さっきパイプで渡された文字を拾ってね」
- `'php://stdin'` は「標準入力」という場所で、さっきの文字が入ってる箱のこと。**stdin** = Standard Input（標準入力）キーボードから打ち込む文字、他のプログラムから送られてくるデータなど。
php://stdin → PHP専用の特別な入口。プログラムに流れ込む標準入力のストリーム川。 file_get_contents → その入口からすべてのデータを一気に吸い上げる。　*魔法のチューブ（パイプ `|`）**に流すと、手紙が川（ストリーム）に乗る感じになる。*
#⑤ `urldecode(...)`
 「紙に書かれた秘密の暗号を解読して、本当の文字に戻す
#⑥ `echo ...;`
- **意味**：最後に、変換した文字を画面に出す。
- 小学生向けに言うと、「魔法で解読した文字を、みんなに見せてあげる」感じ。



ip a

sudo netdiscover -i enp0s3 -r 192.168.56.0/24 
### `sudo`
- 「神の力で行動する魔法」
- 普通の自分の力では届かない領域にアクセスするための呪文みたいなもの
### `netdiscover`
- 「ネットワークの魂を感じ取るセンサー」
- あなたの周りに存在するPCやデバイスをスキャンしてリストアップする
- 例えると「近所の幽霊や妖精の場所をマッピングする魔法の棒」
### `-i`
- 正式には **interface**（インターフェース）の略
- どの回路（ネットワークカード）を使うか指定するスイッチ　　どの耳で世界の声を聞くか
- エネルギー的には「どのゲートから世界とつながるか選ぶ」感じ
### `-r`
- 正式には **range**（範囲）の略
- どの範囲のIPをスキャンするかを指定する
- エネルギー的には「どのエリアの幽霊を見つけに行くか宣言する」感じ



#portscan
$sudo nmap -sC -sV -Pn -p- $IP -oN portscan_result.txt

-sC common
-Pn  no ping
-p- 全ポート
- `-oN` は **“output Normal”** の略
    
- nmap のスキャン結果を **標準的な読みやすいテキスト形式で保存** するオプション

### 1) vimでファイルを開く
```bash
vim /path/to/your/script.sh
```
### 3) ファイルの先頭に行を追加したいとき（シバンを上に入れるなど）
1. `gg` を押す → ファイルの先頭へ。
2. `O`（大文字オー）を押す → 上に新しい行を作って挿入モード。
3. `#!/bin/bash` を入力。
4. `Esc` → `:wq` → Enter。

#vi ファイル名
挿入モードに入る（どれかを押す）

i : カーソルの位置から挿入
入力を終えたら Esc を押してノーマルモードに戻る。
保存・終了（ノーマルモードでコマンド）
:w 保存
:wq 保存して終了

sudo python3 -m http.server 80 　　この-mはなに
`-m` は「モジュールをスクリプトとして実行する」オプション。

コマンド
wfuzz -c -z file,/home/bitch/SecLists/Discovery/Web-Content/raft-large-files.txt --hc 404 "$IP/FUZZ"


#pwd print working directory
現在の作業ディレクトリのパスを調べる
#pwd-p (physical) 本当のパスを表示　notリンク 

#ls list ファイルやディレクトリの一覧を表示する,
役目は渡されたものの中身や名前を表示する
ls　今いる場所の中身を表示
ls / ルートディレクトリの中身を表示

-l (long) 詳細表示
-a (all) 隠しファイル
-la そのフォルダにある全ファイルを詳細表示付きで表示するコマンド


#cd change directory 現在作業ディレクトリを移動する
cd だけだとホームディレクトリに戻る
ホームディレクトリだと～と表示される

#find ファイルを検索するコマンド　ディレクトリを指定して

#mv (move) ファイルの移動または名前変更

#whoami 自分のユーザー名を確認
#id ユーザーやグループで誰がファイルを参照実行できるかを管理している
引数にユーザー名を入れれば、そのユーザーの情報を表示できる

#passwd パスワードを変更するコマンド
#-name 大文字小文字区別（←→iname)

#echo 言ったことをそのまま表示させる機能、中身をそのまま見せる機能
#echo$SHELL 今どの窓口（shell)を使って話しているかわかる

#shopt shell options このシェルの動き方を切り替えるスイッチ　-s set, -u unset

#grep global regular expression (regex) print 全体正規表現
→見つけたものを表示するコマンド、全体を探して特定の文字、文字列を探す
ex 渡された文字列の中からbitchを含む行だけを表示
正規　正式に決まっていること、決まったルール、規則で説明できる
表現　そのルールを文字で書いたもの
「パターンで文字列を説明するための数学的な言葉、ルールを文字列で表現する方法」
#regexの別文法　複雑な条件
. どんな一文字にもマッチなんでも一文字
＊なんでもたくさん　`*` → 直前の文字（ここでは `.`）が **0回以上繰り返す**
^ はじまり　＄終わり　.＊任意の文字列０文字以上そのあと何文字でもいい、`.` が1文字を表しても、`*` が付くと「0文字でもOK、何文字でもOK」に拡張される

. lsだと.から始まる　find regexだと何文字でもok


#tr translate  低レベルな文字処理ツール
#ps process status 今どんな生き物がいるかチェックする虫眼鏡、terminal で実行中のプロセスを表示する（プロセス＝その紙を見て、今動いてる生き物）
#ps-x 自分のユーザーの全プロセスを見る
#ps-aux 全ユーザーの全プロセスを見る　（all users, user-orientedユーザー情報も一緒に表示, processes without controlling terminal端末を持たないプロセスも含める

#tty teletype 今はなしている端末が本物かどうかチェックするコマンド、端末＝ターミナル、窓口、画面（not シェル）
疑似端末＝本物のターミナルのふりをするプログラム
tty判定は入力経路を出しているだけで、元が端末かどうかは関係ない
今使ってるコンピュータとの文字、窓口（端末）がどれか教えて
→今この作業してるターミナルの場所は　/dev/tty3000

#echo 文字をそのまま出すだけのコマンド
#type コマンドの種類（外、ディレクトリにもってある、多くor内、ビルトイン机の引き出しに）を教える

#/bin/nice プログラムの実行優先度（どのくらいCPUを使うか）を調整して起動するコマンド

#suユーザー名　任意のユーザー名に切り替え、そのユーザーのパスワードが必要
#su rootになる
#suコマンド　このコマンドをrootとして実行してね、自分のユーザーのまま、一時的

#sudo-l このユーザーがどのコマンドを管理者権限で実行できるかを一覧表示する
（all:all)all だったらなんでもroot権限で実行できる。特定コマンドだけ書かれていたらそれだけ許されている

#cat 
cat自体は「入力を読んで出力に流す」だけの単純な道具
#fing 探すロボット
#find/ パソコンの一番上/ルートから全部探す

#tee T  一本の水が来て、T字で二つに分かれる、→出力を画面に表示しつつ、ファイルにも保存できる

#msfconsole meta sploit framework 起動
　　　　　　　　　脆弱性を突くためのコード
　　　　　　そのうえの、それをまとめて管理できる高次のしくみ
　　　　　　console = コマンドを入力する画面、ターミナル、ラテン語ともに、フランス語支える台→操作台→指令画面
#msfvenom ペイロード生成ツール　metasploitで毒を作る
　　　　　　ここで接続先IP（parrotosマシン）指定、evilファイル生成

#wget ネットからファイルをダウンロードするコマンド

#lsof-i list open files 
　　　　　lsof  今コンピュータで開かれているファイルや通信を一覧で表示する。ネットワークも開いてるファイルとして扱える
           -i ネットワーク関連の情報を表示するオプション
       lsof -i =ネットワークの窓口だけを絞って見える

#ifconfig interface configuration 設定
ネットワークインターフェースの設定や状態を確認、変更するコマンド

#pip pip installs package pipに意味ない、python で新しい道具を取り寄せて追加する

#uname-a 自分がどんなコンピュータか教えて　-aは全部見せてね

#which この名前のプログラムはどこにあるの

#& 裏で動き続ける、ターミナルはすぐに自由になって次のコマンドを打てる

#curl ブラウザみたいに見せるんじゃなく、サイトの返事だけキャッチする魔法の手紙受信機って感じ

サーバーにhttpリクエストを送るコマンド
このＵＲＬの情報をちょうだいと宇宙に投げるメッセージみたいなもの

#export-p 今設定されている環境設定を全部一覧で見せて！`-p` は **print** の頭文字。

#export このコンピュータの中で使う特別な略称（環境変数）をほかのプログラムでも使えるようにする。ニックネームを貼りだすイメージ。（輸出する、外に出す）

#scp 遠くの家にファイルを送る道具

#vim テキストエディタ
カーソル移動、編集、削除、ファイル保存、検索、設定変更

#ctrlplusz ジョブのサスペンド、一時停止

stty raw -echo (端末のふるまいを切り替える)
平たく：端末に「今から入力はそのまま生で送って、表示はしないでね」と頼む

stty set ternimal
    　端末の身体の感覚を設定するスイッチ
raw      enterまで待たず、入力した文字をそのま
　　　ま即座に渡す
　　　「話し言葉を逐一録音するマイク」をオンにする感じ
-echo キーボードで打った文字を画面に表示
　　　しない　「「口を動かしても自分の声が聞こえないモード」にする感じパスワード入力みたいな、画面に入力を表示したくないとき

fg       一時停止してるし仕事
　　（バックグラウンドジョブ）を今すぐこっちで開始していいよって呼び戻すボタン

⇒入力の可視化は消すけど、操作自体は完全に可能という状態を作ること

#サスペンドしても設定が維持される理由
プロセスは動きを止めるだけで、端末との接続自体は維持される
sttyで設定するのは、端末自体の属性として保存されている。
⇒サスペンドせずにそのままstty -echo やればいいじゃん

魂を呼び戻す前に端末を整える手順みたいな
あとサスペンドすると履歴も一時停止される

#exportTERM＝xterm 
(TERM環境変数）
概念：端末の種類（ターミナルの能力）を示す環境変数
適切に設定されていると、補完や色表示、カーソル制御などの機能が正しく働きやすくなる

シグナルの問題：波動（シグナル）がノイズによって、歪められ、外界にうまく伝わらない問題

xterm 端末エミュレーターの種類で文字色、カーソル改行などのふるまいを定義している

exportこれで子プロセスにも引き継ごうとしている


#head コマンド　分厚い絵本の最初のちょこっとだけ読む道具
-nオプション　最初の何行読むか自分で決められるスイッチ

#wc word count ファイルや文の言葉の数を数えるマシン
 -w word 

#groups` コマンドは、現在のユーザー（または指定したユーザー）が属している**グループ（group）**を表示するLinux/Unix系のコマンド。

#find/-groupadministrators2＞/dev/null 

/ルートディレクトリ＝pcの一番上のフォルダからファイルやフォルダを探す命令

ーgroup administrators ファイルのグループがadministratorsのものだけ見つけてね

エラーはゴミに
pcの中のすべての箱を見て、管理者仲間の持ち物だけ探して途中で開けませんと言われても聞こえないふりする


#＄　プログラムでは＄を付けたら箱になる。
何かを覚えておく小さな箱、変数、右のものを左の箱に入れる
プログラミングでは入れるって意味

#file` コマンドは、**ファイルの種類を調べるコマンド**。












