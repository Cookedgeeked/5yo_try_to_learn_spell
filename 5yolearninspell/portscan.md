###=1=
$sudo nmap -sC -sV -Pn -p- $IP -oN portscan_result.txt

-sC common
-Pn  no ping
-p- 全ポート
- `-oN` は **“output Normal”** の略
    
- nmap のスキャン結果を **標準的な読みやすいテキスト形式で保存** するオプション