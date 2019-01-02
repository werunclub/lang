FORMAT: 1A
HOST: https://club-open.codoon.com

# 企业咕咚开放接口

欢迎使用企业咕咚开放接口

# Data Structures

## 俱乐部 (object)
+ id: 42 (number, required)
+ club_name: 测试俱乐部 (string, required)
+ person_count: 100 (number, required)
+ icon: `https://club-img1.codoon.com/10420275-b862-11e6-a133-1ea400b87ec5.jpeg` (string, required)
+ organizational_architecture(array[部门], fixed) - 组织架构

## 部门 (object)
+ team_id: 100 (number)
+ team_name: `研发部` (string)
+ person_count: 100 (number)
+ nodes(array[子部门])

## 子部门 (object)
+ team_id: 101 (number)
+ team_name: `前端` (string)
+ person_count: 100 (number)
+ nodes(array[])

## 成员 (object)
+ team_id: 100 (number)
+ user_id: `10420275-b862-11e6-a133-1ea400b87ec5` (string)
+ name: 张三 (string)
+ mobile: 13838003800 (string)
+ email: example@example.com (string)
+ join_time: 1484443433 (number) - 时间戳（秒）

## 计步数据 (object)
+ user_id: `10420275-b862-11e6-a133-1ea400b87ec5` (string)
+ curday: `2017-01-02` (string)
+ step: 8000 (number)
+ calories: 700 (number)
+ meters: 4800.0 (number)
+ total_time: 50120 (number) - 运动时长（秒）

## 排行榜成员 (object)
+ team_id: 100 (number)
+ user_id: `10420275-b862-11e6-a133-1ea400b87ec5` (string)
+ name: 张三 (string)
+ rank: 1 (number)
+ steps: 80000 (number)

## GPS数据 (object)
+ user_id: `10420275-b862-11e6-a133-1ea400b87ec5` (string)
+ route_id: 1024 (number)
+ start_time: `2017-07-11 19:22:56` (string)
+ end_time: `2017-07-11 19:53:33` (string)
+ total_time: 1835 (number)
+ total_calories: 344.8068 (number)
+ total_length: 4440 (number)
+ sports_type: 0 (number) - 运动类型：0 跑步 1 健走
+ average_speed: 413 (number) - 平均配速（秒）
+ highest_speed: 268 (number) - 最高配速（秒）
+ highest_speed_perkm: 268 (number) - 最高每公里配速（秒）


# Group 使用指南

### 认证

请联系客服申请接口调用权限，通过申请后可以在管理平台查看和刷新`token`。
然后按以下方式在请求头部添加`token`

```
Authorization: Bearer <your_api_token>
```

### 使用限制

每个接口都有对应的请求频率限制，超过限制后返回错误，下个周期自动恢复。

### 错误列表

错误码 | 状态码 | 说明
----- | ----- | --------------
401   | 401   | 未提供 token
403   | 403   | 无效 token 
500   | 500   | 服务器未知错误（请联系客服）
10001 | 403   | 请求次数超过限制


# Group 接口列表

## 俱乐部基本资料 [GET /club/get_club_basic_info]

获取本俱乐部基本资料

+ Request
    + Headers

            Authorization: Bearer <your_api_token>

+ Response 200 (application/json)
    + Attributes(俱乐部)


## 全部成员列表 [GET /club/all_member_list{?page_num,page_size}]

获取全量俱乐部成员列表  
每天只能调用一次，并且只能在2:00 － 6:00之间调用  
当返回的 page_size 和 memberlist 的个数不同时，则认为翻页结束，也意味着当天的调用次数已用  

+ Parameters
    + page_num: 1 (number, required) - 页码
    + page_size: 100 (number, optional) - 返回数量

+ Request
    + Headers

            Authorization: Bearer <your_api_token>

+ Response 200 (application/json)
    + Attributes
        + errcode: 0 (number)
		+ errmsg: `` (string)
        + page_num: 1 (number)
        + page_size: 100 (number)
        + member_list(array[成员], fixed)


## 新成员列表 [GET /club/new_member_list{?begin_time,end_time}]

获取本俱乐部指定时间段内的新增成员  
每日限制调用100次  
只能获取七日内指定时间段内的新增成员，指定时间段需要小于1小时  

+ Parameters
    + begin_time: 1484443433 (number, required) - 起始时间
    + end_time: 1484446833 (number, required) - 结束时间

+ Request
    + Headers

            Authorization: Bearer <your_api_token>

+ Response 200 (application/json)
    + Attributes
        + member_list(array[成员], fixed)


## 成员上传计步数据 [GET /club/member_step_data{?begin_time,end_time}]

获取本俱乐部指定时间段内的上传的计步数据  
每日限制调用100次  
只能获取七日内指定时间段内的上传的计步数据，指定时间段需要小于1小时  

+ Parameters
    + begin_time: 1484443433 (number, required) - 起始时间
    + end_time: 1484446833 (number, required) - 结束时间

+ Request
    + Headers

            Authorization: Bearer <your_api_token>

+ Response 200 (application/json)
    + Attributes 
        + data_list(array[计步数据], fixed) - 计步数据

## 成员指定日期计步数据 [GET /club/member_step_data_by_day{?day,page_num,page_size}]

获取本俱乐部成员指定日期计步数据  
每页每日限制调用5次    
只能获取七日内计步数据  

+ Parameters
    + day: `2017-01-02` (string) - 日期，格式：yyyy-mm-dd
    + page_num: 1 (number, required) - 页码
    + page_size: 100 (number, optional) - 返回数量,最大1000

+ Request
    + Headers

            Authorization: Bearer <your_api_token>

+ Response 200 (application/json)
    + Attributes 
        + data_list(array[计步数据], fixed) - 计步数据
    

## 获取成员指定时间段计步数据 [GET /club/get_member_step_data_between_date{?user_id,start_date,end_date}]

获取成员指定时间段计步数据
此接口仅用于补数据使用，请求频率限制为每分钟调用100次
只能获取七日内计步数据

+ Parameters
    + start_date: `2017-01-02` (string) - 日期，格式：yyyy-mm-dd
    + end_date: `2017-01-07` (string) - 日期，格式：yyyy-mm-dd
    + user_id: `10420275-b862-11e6-a133-1ea400b87ec5` (string, required) - 用户ID

+ Request
    + Headers

            Authorization: Bearer <your_api_token>

+ Response 200 (application/json)
    + Attributes 
        + data_list(array[计步数据], fixed) - 计步数据


## 成员排行榜 [GET /club/ranking{?type,page_num,page_size}]

获取本俱乐部成员排行榜（今日、本周、本月）  
频率限制每分钟100次（排行榜10分钟更新一次，请做好缓存） 

+ Parameters
    + type: `daily` (string) - 排行榜类型：daily 今日, weekly 本周, monthly 本月
    + page_num: 1 (number, required) - 页码
    + page_size: 100 (number, optional) - 返回数量

+ Request
    + Headers

            Authorization: Bearer <your_api_token>

+ Response 200 (application/json)
    + Attributes
        + member_list(array[排行榜成员], fixed)
    
## 成员上传GPS数据 [GET /club/member_gps_data{?begin_time,end_time}]

获取本俱乐部指定时间段内的上传的GPS数据  
每日限制调用100次  
只能获取七日内指定时间段内的上传的计步数据，指定时间段需要小于1小时  

+ Parameters
    + begin_time: 1484443433 (number, required) - 起始时间
    + end_time: 1484446833 (number, required) - 结束时间

+ Request
    + Headers

            Authorization: Bearer <your_api_token>

+ Response 200 (application/json)
    + Attributes 
        + data_list(array[GPS数据], fixed) - GPS记录