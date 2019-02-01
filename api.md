FORMAT: 1A
HOST: https://open.werunclub.net

# WeRUN WEB API

WeRUN WEB APIをご利用頂き、ありがとうございます。

# Data Structures

## クラブ (object)
+ id: 42 (number, required)
+ club_name: テストクラブ (string, required)
+ person_count: 100 (number, required)
+ icon: `https://img1.werunclub.net/10420275-b862-11e6-a133-1ea400b87ec5.jpeg` (string, required)
+ organizational_architecture(array[部門], fixed) - 組織階層

## 部门 (object)
+ team_id: 100 (number)
+ team_name: `開発部` (string)
+ person_count: 100 (number)
+ nodes(array[子部門])

## 子部门 (object)
+ team_id: 101 (number)
+ team_name: `フロントエンド` (string)
+ person_count: 100 (number)
+ nodes(array[])

## メンバー (object)
+ team_id: 100 (number)
+ user_id: `10420275-b862-11e6-a133-1ea400b87ec5` (string)
+ name: 山田 (string)
+ mobile: 08012345678 (string)
+ email: example@example.com (string)
+ join_time: 1484443433 (number) - タイムスタンプ（秒）

## 歩数データ (object)
+ user_id: `10420275-b862-11e6-a133-1ea400b87ec5` (string)
+ curday: `2017-01-02` (string)
+ step: 8000 (number)
+ calories: 700 (number)
+ meters: 4800.0 (number)
+ total_time: 50120 (number) - タイム（秒）

## ランキングメンバー (object)
+ team_id: 100 (number)
+ user_id: `10420275-b862-11e6-a133-1ea400b87ec5` (string)
+ name: 山田 (string)
+ rank: 1 (number)
+ steps: 80000 (number)

## GPSデータ (object)
+ user_id: `10420275-b862-11e6-a133-1ea400b87ec5` (string)
+ route_id: 1024 (number)
+ start_time: `2017-07-11 19:22:56` (string)
+ end_time: `2017-07-11 19:53:33` (string)
+ total_time: 1835 (number)
+ total_calories: 344.8068 (number)
+ total_length: 4440 (number)
+ sports_type: 0 (number) - 運動タイプ：0 ランニング 1 ウォーキング
+ average_speed: 413 (number) - 平均ペース（秒）
+ highest_speed: 268 (number) - 最高ペース（秒）
+ highest_speed_perkm: 268 (number) - 1kmあたりの最高ペース（秒）


# Group ご利用ガイド

### 認証

WeRUN APIを利用するためには、まず利用のお申込みを行い、`token`を入手してください。
`token`を下記のようにリクエストヘッダに追加してください。

```
Authorization: Bearer <your_api_token>
```

### 利用制限

すべてのAPIに一定時間にアクセス可能な回数に制限があります。上限を超えた場合はエラーとなります。API制限は一定時間を超えればリセットされます。

### エラーリスト

エラーコード | ステータスコード | 説明
----- | ----- | --------------
401   | 401   | token未発行
403   | 403   | 無効なtoken
500   | 500   | 不明なサーバーエラー（ヘルプデスクに連絡してください）
10001 | 403   | API制限にかかりました


# Group インタフェース一覧

## クラブ基本情報 [GET /club/get_club_basic_info]

指定クラブの基本情報を取得する。

+ Request
    + Headers

            Authorization: Bearer <your_api_token>

+ Response 200 (application/json)
    + Attributes(クラブ)


## メンバーリスト [GET /club/all_member_list{?page_num,page_size}]

クラブメンバーの一覧を取得する。
1日1回、2:00 ～ 6:00の間にのみ呼び出し可能。
戻り値の page_size と memberlist の要素数が異なる場合はページ終了となり、本日の呼び出し回数の上限に達することを意味ます。 

+ Parameters
    + page_num: 1 (number, required) - ページ番号
    + page_size: 100 (number, optional) - ページ数

+ Request
    + Headers

            Authorization: Bearer <your_api_token>

+ Response 200 (application/json)
    + Attributes
        + errcode: 0 (number)
		+ errmsg: `` (string)
        + page_num: 1 (number)
        + page_size: 100 (number)
        + member_list(array[メンバー], fixed)


## 新規メンバーリスト [GET /club/new_member_list{?begin_time,end_time}]

指定時間帯に新規参加メンバーの一覧を取得する。
1日100回まで呼び出し可能。
7日間以内の新規メンバーのみ取得可能。指定時間帯は1時間以内。

+ Parameters
    + begin_time: 1484443433 (number, required) - 開始時間
    + end_time: 1484446833 (number, required) - 終了時間

+ Request
    + Headers

            Authorization: Bearer <your_api_token>

+ Response 200 (application/json)
    + Attributes
        + member_list(array[成员], fixed)


## 歩数データ [GET /club/member_step_data{?begin_time,end_time}]

指定時間帯の歩数データを取得する。
1日100回まで呼び出し可能。
7日間以内の同期済みの歩数データのみ取得可能。指定時間帯は1時間以内。

+ Parameters
    + begin_time: 1484443433 (number, required) - 開始時間
    + end_time: 1484446833 (number, required) - 終了時間

+ Request
    + Headers

            Authorization: Bearer <your_api_token>

+ Response 200 (application/json)
    + Attributes 
        + data_list(array[歩数データ], fixed) - 歩数データ

## 指定日付の歩数データ [GET /club/member_step_data_by_day{?day,page_num,page_size}]

指定日付の歩数データを取得する。
1ページあたり1日5回まで呼び出し可能。
7日間以内の同期済みの歩数データのみ取得可能。

+ Parameters
    + day: `2017-01-02` (string) - 日付、フォーマット：yyyy-mm-dd
    + page_num: 1 (number, required) - ページ番号
    + page_size: 100 (number, optional) - ページ数、Max1000

+ Request
    + Headers

            Authorization: Bearer <your_api_token>

+ Response 200 (application/json)
    + Attributes 
        + data_list(array[歩数データ], fixed) - 歩数データ
    

## 指定期間の歩数データ [GET /club/get_member_step_data_between_date{?user_id,start_date,end_date}]

指定期間の歩数データを取得する。
データ補足ためのインターフェース。1分あたり100回まで呼び出し可能。
7日間以内の同期済みの歩数データのみ取得可能。

+ Parameters
    + start_date: `2017-01-02` (string) - 日付、フォーマット：yyyy-mm-dd
    + end_date: `2017-01-07` (string) - 日付、フォーマット：yyyy-mm-dd
    + user_id: `10420275-b862-11e6-a133-1ea400b87ec5` (string, required) - ユーザーID

+ Request
    + Headers

            Authorization: Bearer <your_api_token>

+ Response 200 (application/json)
    + Attributes 
        + data_list(array[歩数データ], fixed) - 歩数データ


## 会員ランキングリスト [GET /club/ranking{?type,page_num,page_size}]

会員ランキングリスト（今日、今週、今月）を取得する。
1分あたり100回まで呼び出し可能。（ランキングリストのデータは10分ごとに更新されるため、キャッシュを用意してください。）

+ Parameters
    + type: `daily` (string) - ランキングタイプ：daily 今日, weekly 今週, monthly 今月
    + page_num: 1 (number, required) - ページ番号
    + page_size: 100 (number, optional) - ページ数

+ Request
    + Headers

            Authorization: Bearer <your_api_token>

+ Response 200 (application/json)
    + Attributes
        + member_list(array[ランキングメンバー], fixed)
    
## GPSデータ [GET /club/member_gps_data{?begin_time,end_time}]

指定時間帯のGPSデータを取得する。
1日あたり100回まで呼び出し可能。
7日間以内の同期済みの歩数データのみ取得可能。指定時間帯は1時間以内。

+ Parameters
    + begin_time: 1484443433 (number, required) - 開始時間
    + end_time: 1484446833 (number, required) - 終了時間

+ Request
    + Headers

            Authorization: Bearer <your_api_token>

+ Response 200 (application/json)
    + Attributes 
        + data_list(array[GPSデータ], fixed) - GPSレコード
