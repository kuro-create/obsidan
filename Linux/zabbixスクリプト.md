#linux
#zabbix

## zabbixスクリプトについて
格納場所は「zabbix_server.conf」に記載されている
「AlertScriptsPath=」ディレクティブがそれ


設定後は「/var/log/zabbix/zabbix_server.log」を確認して、スクリプト実行時のエラーが出ていないかチェックしてください。

zabbixサーバの設定項目
[1 Zabbix server](https://www.zabbix.com/documentation/7.0/en/manual/appendix/config/zabbix_server)

### Specify the debug level:  
_0_ - basic information about starting and stopping of Zabbix processes  
_1_ - critical information;  
_2_ - error information;  
_3_ - warnings;  
_4_ - for debugging (produces lots of information);  
_5_ - extended debugging (produces even more information).  
See also [runtime control](https://www.zabbix.com/documentation/7.0/en/manual/concepts/server#runtime-control) options.