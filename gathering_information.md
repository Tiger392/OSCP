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
# ・-p-：全ポートをスキャン（e.g. -p 80, -p 0-2000）
#（おまけ）
# -sS：SYNスキャン（コネクション確立せず判断→IDS検知されにくい・ログが残りにくい・sudoが必要・サーバ負荷高い）
# -v：スキャン結果の詳細を取得（-vvでより詳細を取得）
```

<br>

### DNS列挙
```bash
# hostコマンド（ドメインのIPアドレス、MXレコード[メールサーバ[、TXTレコードなどを取得）
host <domain>
host -t mx <domain>
host -t txt <domain>

# Forward Lookup Brute Force（ワードリストやIP範囲を指定してサブドメインやホスト名を総当たりで発見）
for ip in $(cat wordlists.txt); do host $ip.<domain>; done

# Reverse Lookup Brute Force
for ip in $(seq 50 100); do host 10.10.10.$ip; done | grep -v "not found"

# Get DNS Server
host -t ns <domain> | cut -d " " -f 4

# DNS Zone Transfer（DNSサーバから全てのDNSレコード取得試行[設定ミスがある場合のみ成功]）
host -l <domain> <dns server address>

# dnsrecon（DNSの包括的な列挙ツールでゾーン転送やブルートフォースが可能）
dnsrecon -d <domain> -t axfr
dnsrecon -d <domain> -D wordlists.txt -t brt
```
