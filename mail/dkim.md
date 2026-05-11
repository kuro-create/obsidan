

| 目的              | ファイル                                            | 内容                            |
| --------------- | ----------------------------------------------- | ----------------------------- |
| opendkim のメイン設定 | **/etc/opendkim.conf**                          | 動作モード、署名方法、Socket、KeyTable など |
| Postfix との連携    | **/etc/default/opendkim**（Debian系）              | opendkim の起動設定、Socket の場所     |
| DKIM 鍵のマッピング    | **KeyTable**                                    | selector → 秘密鍵ファイルの対応         |
| 署名対象ドメインのマッピング  | **SigningTable**                                | From ドメイン → selector の対応      |
| 秘密鍵そのもの         | **/etc/opendkim/keys/<domain>/default.private** | DKIM の秘密鍵                     |

まずは/etc/opendkim.confを確認する

default._domainkey.example.com example.com:default:/etc/opendkim/keys/example.com/default.private
