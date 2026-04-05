#linux
#nextcloud

参考リンク
[Nextcloud アップデート後に「データベースに不正な行フォーマットが見つかりました。」の警告メッセージ - ex1-lab](https://ex1.m-yabe.com/archives/7613)

警告メッセージの内容ですが、Nextcloudがバージョンアップ後にデータベーステーブルの ROW_FORMAT が Dynamic に変更されたのが原因です。  
Nextcloudのパフォーマンスを最適化するために、テーブルの ROW_FORMAT=DYNAMIC に変更する必要があります。

確認方法
１：　occ status 


２：　テーブルの行ステータス確認
★ログイン
mysql -u nextcloud -p 

show DATABASES;

use nextcloud;

★テーブル定義確認

SELECT TABLE_NAME, ROW_FORMAT
FROM information_schema.tables
WHERE table_schema = 'nextcloud';
※ `nextcloud` はあなたの Nextcloud の DB 名に置き換えてください。

プラいべーとの環境で確認
mysql> select table_name,row_format FROM information_schema.tables WHERE table_schema = 'nextcloud';
+-----------------------------+------------+
| TABLE_NAME                  | ROW_FORMAT |
+-----------------------------+------------+
| oc_accounts                 | Dynamic    |
| oc_accounts_data            | Dynamic    |
| oc_activity                 | Dynamic    |
| oc_activity_mq              | Dynamic    |
| oc_addressbookchanges       | Dynamic    |
| oc_addressbooks             | Dynamic    |
| oc_appconfig                | Dynamic    |
| oc_appconfig_ex             | Dynamic    |
| oc_authorized_groups        | Dynamic    |
| oc_authtoken                | Dynamic    |
| oc_bruteforce_attempts      | Dynamic    |
| oc_calendar_invitations     | Dynamic    |
| oc_calendar_reminders       | Dynamic    |
| oc_calendar_resources       | Dynamic    |
| oc_calendar_resources_md    | Dynamic    |
| oc_calendar_rooms           | Dynamic    |
| oc_calendar_rooms_md        | Dynamic    |
| oc_calendarchanges          | Dynamic    |
| oc_calendarobjects          | Dynamic    |
| oc_calendarobjects_props    | Dynamic    |
| oc_calendars                | Dynamic    |
| oc_calendarsubscriptions    | Dynamic    |
| oc_cards                    | Dynamic    |
| oc_cards_properties         | Dynamic    |
| oc_circles_circle           | Dynamic    |
| oc_circles_event            | Dynamic    |
| oc_circles_member           | Dynamic    |
| oc_circles_membership       | Dynamic    |
| oc_circles_mount            | Dynamic    |
| oc_circles_mountpoint       | Dynamic    |
| oc_circles_remote           | Dynamic    |
| oc_circles_share_lock       | Dynamic    |
| oc_circles_token            | Dynamic    |
| oc_collres_accesscache      | Dynamic    |
| oc_collres_collections      | Dynamic    |
| oc_collres_resources        | Dynamic    |
| oc_comments                 | Dynamic    |
| oc_comments_read_markers    | Dynamic    |
| oc_dav_absence              | Dynamic    |
| oc_dav_cal_proxy            | Dynamic    |
| oc_dav_shares               | Dynamic    |
| oc_direct_edit              | Dynamic    |
| oc_directlink               | Dynamic    |
| oc_ex_apps                  | Dynamic    |
| oc_ex_apps_daemons          | Dynamic    |
| oc_ex_apps_routes           | Dynamic    |
| oc_ex_deploy_options        | Dynamic    |
| oc_ex_event_handlers        | Dynamic    |
| oc_ex_occ_commands          | Dynamic    |
| oc_ex_settings_forms        | Dynamic    |
| oc_ex_speech_to_text        | Dynamic    |
| oc_ex_speech_to_text_q      | Dynamic    |
| oc_ex_task_processing       | Dynamic    |
| oc_ex_text_processing       | Dynamic    |
| oc_ex_text_processing_q     | Dynamic    |
| oc_ex_translation           | Dynamic    |
| oc_ex_translation_q         | Dynamic    |
| oc_ex_ui_files_actions      | Dynamic    |
| oc_ex_ui_scripts            | Dynamic    |
| oc_ex_ui_states             | Dynamic    |
| oc_ex_ui_styles             | Dynamic    |
| oc_ex_ui_top_menu           | Dynamic    |
| oc_external_applicable      | Dynamic    |
| oc_external_config          | Dynamic    |
| oc_external_mounts          | Dynamic    |
| oc_external_options         | Dynamic    |
| oc_federated_reshares       | Dynamic    |
| oc_file_locks               | Dynamic    |
| oc_filecache                | Dynamic    |
| oc_filecache_extended       | Dynamic    |
| oc_files_metadata           | Dynamic    |
| oc_files_metadata_index     | Dynamic    |
| oc_files_reminders          | Dynamic    |
| oc_files_trash              | Dynamic    |
| oc_files_versions           | Dynamic    |
| oc_flow_checks              | Dynamic    |
| oc_flow_operations          | Dynamic    |
| oc_flow_operations_scope    | Dynamic    |
| oc_group_admin              | Dynamic    |
| oc_group_user               | Dynamic    |
| oc_groups                   | Dynamic    |
| oc_jobs                     | Dynamic    |
| oc_known_users              | Dynamic    |
| oc_login_flow_v2            | Dynamic    |
| oc_migrations               | Dynamic    |
| oc_mimetypes                | Dynamic    |
| oc_mounts                   | Dynamic    |
| oc_notifications            | Dynamic    |
| oc_notifications_pushhash   | Dynamic    |
| oc_notifications_settings   | Dynamic    |
| oc_oauth2_access_tokens     | Dynamic    |
| oc_oauth2_clients           | Dynamic    |
| oc_open_local_editor        | Dynamic    |
| oc_photos_albums            | Dynamic    |
| oc_photos_albums_collabs    | Dynamic    |
| oc_photos_albums_files      | Dynamic    |
| oc_preferences              | Dynamic    |
| oc_preferences_ex           | Dynamic    |
| oc_privacy_admins           | Dynamic    |
| oc_profile_config           | Dynamic    |
| oc_properties               | Dynamic    |
| oc_ratelimit_entries        | Dynamic    |
| oc_reactions                | Dynamic    |
| oc_recent_contact           | Dynamic    |
| oc_retention                | Dynamic    |
| oc_schedulingobjects        | Dynamic    |
| oc_sec_signatory            | Dynamic    |
| oc_share                    | Dynamic    |
| oc_share_external           | Dynamic    |
| oc_shares_limits            | Dynamic    |
| oc_storages                 | Dynamic    |
| oc_storages_credentials     | Dynamic    |
| oc_systemtag                | Dynamic    |
| oc_systemtag_group          | Dynamic    |
| oc_systemtag_object_mapping | Dynamic    |
| oc_taskprocessing_tasks     | Dynamic    |
| oc_text2image_tasks         | Dynamic    |
| oc_text_documents           | Dynamic    |
| oc_text_sessions            | Dynamic    |
| oc_text_steps               | Dynamic    |
| oc_textprocessing_tasks     | Dynamic    |
| oc_trusted_servers          | Dynamic    |
| oc_twofactor_backupcodes    | Dynamic    |
| oc_twofactor_providers      | Dynamic    |
| oc_twofactor_totp_secrets   | Dynamic    |
| oc_user_status              | Dynamic    |
| oc_user_transfer_owner      | Dynamic    |
| oc_users                    | Dynamic    |
| oc_vcategory                | Dynamic    |
| oc_vcategory_to_object      | Dynamic    |
| oc_webauthn                 | Dynamic    |
| oc_webhook_listeners        | Dynamic    |
| oc_whats_new                | Dynamic    |
+-----------------------------+------------+
133 rows in set (0.00 sec)

|ROW_FORMAT|Nextcloud 32 の評価|
|---|---|
|**Dynamic**|◎ 推奨（問題なし）|
|**Compressed**|○ 許容|
|**Compact**|× 古い形式 → 警告対象|
|**Redundant**|× かなり古い → 警告対象|
