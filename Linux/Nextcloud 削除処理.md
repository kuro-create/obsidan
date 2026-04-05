#linux 
#nextcloud 

# 🗂 Nextcloud Trashbin（ゴミ箱）削除処理まとめ

## 1. 削除処理の全体像

Nextcloud の削除は **3 段階構造**。

### ① 論理削除（Trashbin へ移動）

- ユーザーが削除したファイルは即座に消えない
    
- `data/<user>/files_trashbin/` に移動
    
- ファイル名にタイムスタンプが付く 例：`photo.jpg.d1712345678`
    

### ② 自動削除（Retention / Quota）

- 日数ルール（保持期間）
    
- 容量ルール（quota 超過時）
    
- 判定ロジックは `Expiration.php`
    

### ③ 物理削除（ストレージから完全削除）

- 実体ファイル削除
    
- メタデータ削除（`.dmeta`）
    
- DB（filecache）から削除
    
- 実装は `TrashBackend.php`
    

## 2. 削除条件（Expiration.php）

### A. 保持日数（Retention）

- 設定：`trashbin_retention_obligation`
    
- 削除日時（timestamp）と現在時刻を比較
    
- 指定日数を超えたら削除対象
    

php

```
if (now - deletedAt > retentionDays) {
    delete();
}
```

### B. 容量（Quota）

- ユーザーの使用量が quota を超えた場合
    
- ゴミ箱内のファイルを「古い順」に削除
    
- quota を下回るまでループ
    

php

```
while (usedSpace > quota) {
    delete(oldestTrashFile);
}
```

## 3. 物理削除（TrashBackend.php）

### 実際の削除処理の流れ

1. **実体パスの解決**
    
    コード
    
    ```
    data/<user>/files_trashbin/files/<filename>.d<timestamp>
    ```
    
2. **ファイル削除（unlink 相当）**
    
    php
    
    ```
    $node->delete();
    ```
    
3. **メタデータ削除**
    
    コード
    
    ```
    <filename>.d<timestamp>.dmeta
    ```
    
4. **filecache（DB）から削除**
    
5. **外部ストレージの場合は API 呼び出し**
    
    - SMB
        
    - S3
        
    - WebDAV
        

## 4. 削除処理の呼び出しタイミング

- cron（`cron.php`）
    
- `occ trashbin:cleanup`
    
- ユーザーが「ゴミ箱を空にする」を実行
    

cron の場合：

コード

```
apps/files_trashbin/lib/Expiration.php の cleanTrashFiles()
```

## 5. タイムアウトが発生するポイント

Nextcloud の削除処理は複数レイヤーを通るため、 タイムアウトは以下のどこかで発生する。

### 🟥 1. TrashBackend（物理削除） ← 最も多い

- 外部ストレージの削除が遅い
    
- 大容量ファイルの削除
    
- PHP の `max_execution_time` 超過
    

ログ例：

コード

```
Error: Maximum execution time of 360 seconds exceeded
```

### 🟧 2. Expiration（削除判定）

- ゴミ箱内に大量ファイルがある
    
- filecache のクエリが重い
    
- 削除ループが長時間になる
    

### 🟨 3. 外部ストレージの遅延

- SMB の応答遅延
    
- S3 API のレイテンシ
    
- WebDAV のレスポンス遅延
    

### 🟦 4. PHP-FPM / CLI の実行時間制限

- `php.ini` の `max_execution_time`
    
- cron の実行時間制限（例：300 秒）
    

## 6. 関連ファイル一覧（コードリーディング用）

|ファイル|役割|
|---|---|
|`appinfo/routes.php`|API ルート定義（/preview など）|
|`Controller/PreviewController.php`|プレビュー生成処理|
|`Trash/TrashManager.php`|ゴミ箱管理（ID 取得など）|
|`Trash/TrashBackend.php`|物理削除の本体|
|`Trash/Expiration.php`|削除条件（Retention / Quota）|

## 7. まとめ（要点）

- Nextcloud の削除は「移動 → 判定 → 物理削除」の 3 段階
    
- 削除条件は Retention（日数）と Quota（容量）
    
- 実際の削除は TrashBackend が担当
    
- タイムアウトはほぼ「物理削除」で発生
    
- 外部ストレージ利用時は特に発生しやすい