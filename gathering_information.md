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
