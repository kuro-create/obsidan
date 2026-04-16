#linux 
#usermin

# Rocky Linux 9：Usermin HTTPS 通信テスト & 証明書再発行まとめ

## 1. 環境情報

- OS：Rocky Linux 9（VirtualBox）
    
- ホスト（Windows）側 hosts：
    
    コード
    
    ```
    192.168.10.7 rocky2
    ```
    
- Usermin ポート：`20000/tcp`
    
- アクセス URL：
    
    コード
    
    ```
    https://rocky2:20000/
    ```
    
    または
    
    コード
    
    ```
    https://192.168.10.7:20000/
    ```
    

## 2. Usermin の HTTPS 設定ファイル

Usermin の SSL 設定は以下に存在：

コード

```
/etc/usermin/miniserv.conf
```

重要項目：

コード

```
ssl=1
ssl_cert=/etc/usermin/miniserv.pem
```

- Usermin は **証明書 + 秘密鍵を結合した PEM** を使用する
    
- Apache/Nginx のように cert/key を別々に指定できない
    

## 3. 自己署名証明書の再発行（標準 makecert.pl）

### 3.1 Usermin 停止（任意）

コード

```
systemctl stop usermin
```

### 3.2 既存証明書のバックアップ（任意）

コード

```
cp /etc/usermin/miniserv.pem /etc/usermin/miniserv.pem.bak
```

### 3.3 証明書の再生成

コード

```
/usr/libexec/usermin/makecert.pl
```

### 3.4 Usermin 再起動

コード

```
systemctl restart usermin
```

### 3.5 結果

- 新しい自己署名証明書が生成される
    
- CN は固定（多くは "localhost"）
    
- ブラウザは「保護されていない通信」と警告する（正常）
    

## 4. CN=rocky2 の自己署名証明書を作成する方法（OpenSSL）

makecert.pl は **サーバ名（CN）を指定できない**ため、 CN/SAN を指定したい場合は OpenSSL を使用する。

### 4.1 OpenSSL 設定ファイル作成

コード

```
nano /etc/usermin/openssl.cnf
```

内容：

コード

```
[ req ]
default_bits       = 2048
prompt             = no
default_md         = sha256
distinguished_name = dn
req_extensions     = req_ext

[ dn ]
CN = rocky2

[ req_ext ]
subjectAltName = @alt_names

[ alt_names ]
DNS.1 = rocky2
DNS.2 = 192.168.10.7
```

### 4.2 証明書と鍵を生成

コード

```
openssl req -x509 -nodes -days 365 \
  -newkey rsa:2048 \
  -keyout /etc/usermin/privkey.pem \
  -out /etc/usermin/cert.pem \
  -config /etc/usermin/openssl.cnf
```

### 4.3 Usermin 用 PEM を結合

コード

```
cat /etc/usermin/cert.pem /etc/usermin/privkey.pem \
  > /etc/usermin/miniserv.pem

chmod 600 /etc/usermin/miniserv.pem
```

### 4.4 Usermin 再起動

コード

```
systemctl restart usermin
```

### 4.5 結果

- CN = rocky2
    
- SAN = rocky2 / 192.168.10.7
    
- 自己署名のため警告は残るが、CN 不一致の警告は解消される
    

## 5. Firewall 設定（必要な場合）

コード

```
firewall-cmd --add-port=20000/tcp --permanent
firewall-cmd --reload
```

## 6. 動作確認

ブラウザで以下へアクセス：

コード

```
https://rocky2:20000/
```

または

コード

```
https://192.168.10.7:20000/
```

## 7. 補足：警告が出る理由

- 自己署名証明書はブラウザに信頼されない
    
- 公的 CA（Let’s Encrypt 等）で署名されていない
    
- CN/SAN が一致しない場合はさらに警告が増える
    

**→ 今回はテスト目的なので警告は無視して問題なし**