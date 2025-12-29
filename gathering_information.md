# 情報収集

### ホスト調査
```bash
# ping scanを実行してNW上の稼働ホストを発見（ポートスキャンは実施せず）
nmap -sn <IP>/16

# SMBプロトコルを使用してNW範囲内のホストを列挙
crackmapexec smb <IP>/24

# Ping Sweep[PowerShell]（指定したIP範囲に対してpingを送信し応答のあるホストを特定）
for ($1=1;$i -lt 255;$i++) { ping -n <IP>.$i| findstr "TTL" }

# Ping Sweep（Bash）
for i in {1..255}; do (ping -c 1 <IP>.$i | grep "bytes from" &); done

# 特定のホストに対して開放しているポートを手動で確認
# (Bash)
for i in {1..65535}; do (echo > /dev/tcp/<IP>/$i) >/dev/null 2>&1 && echo $i is open; done
# (NetCat)
nc -xvn <IP> 1-1000
```

<br>

### ポートスキャン
```bash
nmap -sC -sV -A -Pn -T5 -p- <IP>
# (オプション)
# ・-sC：デフォルトスクリプト実行
# ・-sV：サービスバージョン検出
# ・-A：OS検出、バージョン検出など有効化
# ・-Pn：pingを送信せずスキャン（Windows OSではping拒否する）
# ・-T5：スキャン速度指定（0~5）
# ・-p-：全ポートをスキャン
#（おまけ）
# -sS：SYNスキャン（コネクション確立せず判断→IDS検知されにくい・ログが残りにくい・sudoが必要・サーバ負荷高い）
```
