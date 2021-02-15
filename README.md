# 对接说明



1. ##### 信息

   版本号：1.0.0

   更新时间：2021-02-15

   

2. ##### 签名算法

   提交的所有参数加上appkey，根据key对关联参数进行升序排序，然后连接所有的值和接口url组成的字符串取md5

   过程如下：

   1. 提交参数为：

      mid: 10000

      m_username: aabbcc

      ts: 1613301503

   2. 加上appkey后为：

      mid: 10000

      m_username: aabbcc

      ts: 1613301503

      appkey: c4ca4238a0b923820dcc509a6f75849b

   3. 按照key值生序排序后为：

      appkey: c4ca4238a0b923820dcc509a6f75849b

      m_username: aabbcc

      mid: 10000

      ts: 1613301503

   4. 连接所有的value组成字符串：

      c4ca4238a0b923820dcc509a6f75849baabbcc100001613301503

   5. 连接接口url

      c4ca4238a0b923820dcc509a6f75849baabbcc100001613301503http://merchant.bloption.com/user/reg

   6. 组合成的字符串取md5值即为签名sign的值

      md5("c4ca4238a0b923820dcc509a6f75849baabbcc100001613301503http://merchant.bloption.com/user/reg")

   

   说明：

   mid和appkey由我方提供

   

3. ##### 注册新用户

   POST Url: http://merchant.bloption.com/user/reg

   Request：

   | 参数名     | 说明                           | 参与签名 |
   | :--------- | ------------------------------ | -------- |
   | mid        | 商户id                         | 1        |
   | m_username | 商户系统的用户名，商户系统唯一 | 1        |
   | ts         | 时间戳，秒级                   | 1        |
   | sign       | 签名,算法见 1.签名算法         | 0        |

   Response：

   1. 第一次请求注册返回：

      `{"status":200,"data":{"username":"bl_aabbcc","password":"LchIEewRJjn2","msg":"new user inserted"}}`

      username和password用于web自动登录。

   2. 用户如果已经注册过：

      `{"status":200,"data":{"username":"bl_aabbcc","password":"lJqPkaNNwzhL","msg":"password updated"}}`

      会自动更新密码，需要对应做密码更新。

   Curl example:

   ​	`curl -d "mid=10081&m_username=testuser&ts=1613318988&sign=4cc52c30c0b04a8723f6d5a06f6e584e" "http://merchant.bloption.com/user/reg" -i`

   

4. ##### 转账资金至二元期权账户

   Post Url: http://merchant.bloption.com/user/transfer

   Request：

   | 参数名     | 说明                                                         | 参与签名 |
   | :--------- | ------------------------------------------------------------ | -------- |
   | mid        | 商户id                                                       | 1        |
   | m_username | 商户系统的用户名，商户系统唯一                               | 1        |
   | amount     | 转账金额，decimal(22,2)。正值表示转入钱包；负值表示撤销转账，钱包扣除相应金额。 | 1        |
   | ts         | 时间戳，秒级                                                 | 1        |
   | sign       | 签名,算法见 1.签名算法                                       | 0        |

   Response:

   成功：

   `{"status":200,"data":""}`

   失败：

   `{"status":507,"data":""}`

   Curl example:

   ​	`curl -d "mid=10081&m_username=testuser&amount=1005&ts=1613318988&sign=2b422af0deec9738001dd5a64bfcf4de" "http://merchant.bloption.com/user/transfer" -i`

   

5. ##### 从二元期权账户提取资金(所有资金) 

   Post Url: http://merchant.bloption.com/user/withdraw

   Request：

   | 参数名     | 说明                           | 参与签名 |
   | :--------- | ------------------------------ | -------- |
   | mid        | 商户id                         | 1        |
   | m_username | 商户系统的用户名，商户系统唯一 | 1        |
   | ts         | 时间戳，秒级                   | 1        |
   | sign       | 签名,算法见 1.签名算法         | 0        |

   Response：

   成功：

   `{"status":200,"data":{"amount":"1005.00"}}` amount为提现金额

   失败：

   `{"status":508,"data":""}` 

   Curl example:

   ​	`curl -d "mid=10081&m_username=testuser&ts=1613318988&sign=4cc52c30c0b04a8723f6d5a06f6e584e" "http://merchant.bloption.com/user/withdraw" -i`

   

6. ##### 查询余额

   Post url: http://merchant.bloption.com/user/balance

   Request:

   | 参数名     | 说明                           | 参与签名 |
   | :--------- | ------------------------------ | -------- |
   | mid        | 商户id                         | 1        |
   | m_username | 商户系统的用户名，商户系统唯一 | 1        |
   | ts         | 时间戳，秒级                   | 1        |
   | sign       | 签名,算法见 1.签名算法         | 0        |

   Response:

   ​	`{"status":200,"data":{"balance":"1005.00"}}`

   Curl example:

   ​	`curl -d "mid=10081&m_username=testuser&ts=1613374391&sign=09adf2509174723f63363fed7ba43c94" "http://merchant.bloption.com/user/balance" -i`

   

7. ##### 错误代码

   | 代码 | 说明                  |
   | ---- | --------------------- |
   | 501  | 商户不存在            |
   | 502  | 签名错误              |
   | 503  | action参数错误        |
   | 505  | 时间戳ts错误          |
   | 506  | 用户不存在            |
   | 507  | 转账失败              |
   | 508  | 提现失败              |
   | 509  | 提现失败，账户余额为0 |

   


