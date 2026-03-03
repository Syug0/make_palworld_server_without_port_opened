# Palworld Dedicated Server 構築手順（Windows + ZeroTier）

この手順は、ポート開放ができない環境でも  
ZeroTier を使用してマルチプレイするための構築手順です。

想定人数：4人程度

---

# 0. 必要条件

- Windows 10 / 11
- Steam インストール済み
- メモリ 8GB 推奨（最低4GB）
- 安定したネット回線

---

# 1. Palworld Dedicated Server のインストール

1. Steam を起動
2. 「ライブラリ」
3. 検索バーに `Palworld Dedicated Server`
4. インストール

インストール完了後、まだ起動しない。

---

# 2. ZeroTier のインストール（全員）

1. https://www.zerotier.com/download/
2. Windows版をダウンロード
3. インストール
4. タスクトレイに ZeroTier アイコンが表示される

---

# 3. ZeroTier ネットワーク作成（サーバー担当のみ）

1. https://my.zerotier.com/
2. ログイン
3. 「Create A Network」
4. Network ID（16桁）をコピー

---

# 4. 全員をネットワークに参加させる

1. タスクトレイの ZeroTier を右クリック
2. 「Join New Network」
3. Network ID を入力

---

# 5. 承認作業（サーバー担当のみ）

1. my.zerotier.com に戻る
2. 作成したネットワークを開く
3. 参加者の「Auth」にチェック

---

# 6. ZeroTier を Private に変更（サーバー側のみ）

管理者 PowerShell を起動：

```powershell
Get-NetConnectionProfile
```

ZeroTier の InterfaceIndex を確認。

例：
```
InterfaceIndex : 37
```

変更：

```powershell
Set-NetConnectionProfile -InterfaceIndex 37 -NetworkCategory Private
```

確認：

```powershell
Get-NetConnectionProfile -InterfaceIndex 37
```

`NetworkCategory : Private` になればOK。

---

# 7. Windows ファイアウォール設定（サーバー側）

管理者 PowerShell で実行：

```powershell
New-NetFirewallRule -DisplayName "Palworld_UDP_8211" `
-Direction Inbound `
-Protocol UDP `
-LocalPort 8211 `
-Action Allow `
-Profile Private
```

---

# 8. サーバー初回起動

Steam から Palworld Dedicated Server を起動。

起動ログに以下が表示される：

```
Running Palworld dedicated server on :8211
```

これでサーバー起動成功。

---

# 9. ZeroTier IP の確認（サーバー側）

PowerShell：

```powershell
ipconfig
```

以下を探す：

```
ZeroTier One
  IPv4 アドレス : 172.xx.xx.xx
```

このIPを参加者に共有。

---

# 10. 接続方法

ゲーム内の IP 接続に：

```
172.xx.xx.xx:8211
```

を入力。

---

# 11. 動作確認

参加者が：

```cmd
ping 172.xx.xx.xx
```

で応答があればVPN接続正常。

---

# 12. 推奨設定

長時間運用する場合：

- 1日1回サーバー再起動
- 他の重いアプリを閉じる
- メモリ不足なら再起動

---

# トラブルシューティング

## 接続できない場合

1. ZeroTier が ONLINE か確認
2. ping が通るか確認
3. サーバーが起動しているか確認

```powershell
netstat -ano | findstr 8211
```

`UDP 0.0.0.0:8211` が表示されれば正常。

---

以上で構築完了です。
