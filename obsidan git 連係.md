#git
#obsidan

# # Obsidian × GitHub（SSH）連携手順まとめ（Windows）

## ## 1. Obsidian Vault の準備

- Vault フォルダ例： `D:\obsidan\mywork`
    
- このフォルダを Git リポジトリ化する
    

powershell

```
cd D:\obsidan\mywork
git init
```

## ## 2. SSH キーの作成と GitHub 登録

### ● SSH キーの存在確認

powershell

```
ls ~/.ssh
```

### ● なければ作成

powershell

```
ssh-keygen -t ed25519 -C "your_email@example.com"
```

### ● 公開鍵を GitHub に登録

powershell

```
cat ~/.ssh/id_ed25519.pub
```

GitHub → Settings → SSH and GPG keys → New SSH key に貼り付け。

## ## 3. Windows の SSH 鍵パーミッション修正（重要）

SSH がエラーを出した場合、 `.ssh` フォルダと鍵ファイルのアクセス権を修正する。

### ● 修正ポイント

- `C:\Users\<ユーザー名>\.ssh\`
    
- `id_ed25519` / `id_ed25519.pub`
    

### ● 残すべき権限

- **自分のユーザー（nitig）だけ**
    
- Everyone / Users / Authenticated Users は削除
    

## ## 4. GitHub への SSH 接続確認

powershell

```
ssh -T git@github.com
```

成功例：

コード

```
Hi <username>! You've successfully authenticated, but GitHub does not provide shell access.
```

## ## 5. GitHub リポジトリを作成

- GitHub 上で新規リポジトリを作成
    
- **README を作らない（空の状態が理想）**
    

## ## 6. リモート（origin）の設定

### ● 間違った例（今回のケース）

コード

```
orgin
```

### ● 正しい設定

powershell

```
git remote add origin git@github.com:kuro-create/obsidan.git
```

### ● 確認

powershell

```
git remote -v
```

## ## 7. `.gitignore` の配置

Vault の直下に `.gitignore` を置く：

コード

```
D:\obsidan\mywork\.gitignore
```

### ● 推奨内容

コード

```
.obsidian/cache
.obsidian/workspace
.obsidian/workspace.json
Thumbs.db
desktop.ini
```

## ## 8. 初回コミット

powershell

```
git add .
git commit -m "Initial commit"
git branch -M main
```

## ## 9. GitHub に push（初回）

powershell

```
git push -u origin main
```

## ## 10. push が拒否された場合（今回のケース）

GitHub 側に README などがあると push が拒否される。

### ● 解決方法（安全）

powershell

```
git pull origin main --allow-unrelated-histories
git push -u origin main
```

# # まとめ

この手順で：

- Windows の SSH 認証
    
- Obsidian Vault の Git 管理
    
- GitHub への push
    
- `.gitignore` の最適化
    

がすべて完了する。