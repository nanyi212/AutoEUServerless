# AutoEUServerless

AutoEUServerless 是一个基于腾讯云函数 Serverless 架构的自动化工具，用于自动续期 EUserv 免费 IPv6 VPS。项目目的是简化续期过程，避免因遗忘续期而导致服务中断。

## 功能
- **腾讯云函数和AWS工作流**：无服务器化部署。
- **自动续期**：自动获取账号内所有的 VPS 项目，并检测是否需要续期。
- **多账户支持**：支持配置多个 EUserv 账户。
- **自动获取PIN码**：支持通过转发规则解析获取邮箱PIN码。
- **验证码识别**：自动识别登录过程中的验证码。
- **Telegram 通知**：通过 Telegram Bot 发送续期状态通知。

```
TRC20: TBY7e7YUQCG7wEH3PA8pk6fQPwpshb8Z55
```

## 截图  
![Alt text](image/screenshot1.png)

## 如何使用
1. 新建一个BeautifulSoup层  
    进入[腾讯云函数服务](https://console.cloud.tencent.com/scf/)  
    新建一个层
    ![Alt text](image/step1.jpg)
    - [**BeautifulSoup.zip**](BeautifulSoup.zip)  <-- 此处下载BeautifulSoup
    - 运行环境选择**Python3.6**
    - 注意层要选择非大陆地区且和第二部的层地区一致

2.  选择函数服务,新建一个函数 
    ![Step1](image/step2.png)
    - 由于免费的德鸡在德国,腾讯云函数只有一个欧洲机房,所以我们选择法兰克福
    - 其实选择HK或者SG也是差不多的  

3.  新建一个函数,和图片操作保持一致
    ![Step3](image/step3.png)
  
4.  复制本库的 [**main.py**](/main.py) 中的全部代码  
    粘贴到在线编辑框内  
    
5.  填写这一部分  *
    ![Step4](image/step4.png)  
    - TelegramBOT相关如何获取请搜索Youtube
    - **此时如果你还不知道如何填写Mailparser部分,暂时留空,下面有教学*

6.  **在EUserV中关闭登录二次验证!**  
    ![Step6](image/step6.png)  
    - 进入EuserV的管理面板,点击[**Settings**] -> 勾选 [**Deactivated (insecure)**] -> 点击[**Save**]

7.  配置函数的运行函数
    ![Step7](image/step7.png)
    - [函数服务] -> [函数管理]
    - 内存64MB就足够,执行事件考虑网络延迟给到[300]秒
    - 下面有个日志投递,建议开启方便debug

8.  绑定BeautifulSoup层
    ![Step8](image/step8.png)

9.  测试函数
    ![Step9](image/step9.png)
    - 此时可以点击部署并测试函数,检查日志是否有错误
    - 检查Telegram机器人是否正常发送日志
    - 没填写Mailparser也可以先测试以下看下TG Bot和登录是否正常

10. 创建定时触发器
    ![Step10](image/step10.png)
    ```
    0 0 12 */7 * * *
    ```
    - 代表每七天的中午12点触发一次,你也可以自定义

#### 11. 现在你可以毫无顾虑的让你的🐥小鸡吃灰了


## 如何配置 Mailparser
- 此功能的原理是让你绑定邮箱的PIN码邮件转发到 [Mailparser]("https://mailparser.io/")
- 之后通过规则功能筛选出PIN码,填表续订的目的
- [PIN码模板](#pin码邮件模板) 在文档的最后面

1. 首先去注册一个[Mailparser]("https://mailparser.io/")然后创建一个邮箱,不多解释
2. 然后将你绑定邮箱添加一个转发规则,这里以Gmail作为例子
    ![Step11](image/step11.png)
3. 按照图片填写转发规则
    ![Step12](image/step12.png)
    ![Step13](image/step13.png)
    - 你的登录PIN码和续订PIN码不是一样的!所以不要擅作主张修改
    - 只要一字不差的按照图片填写是没有错的!
4.  添加邮箱规则
    ![Step14](image/step14.png)
    - 此步骤需要注意, 你需要至少有一封邮件才可以创建规则
    - 你可以使用你自己的邮箱向收件箱发送一个[模板](#pin码邮件模板)

#### 此步骤开始严格按照教程走,禁止开动小脑筋

5.  #### 添加取PIN码的规则
    ![Alt text](<image/mailparser_data_parsing_rules_pin .png>)
6.  #### 添加取邮件标题的规则
    ![Alt text](image/mailparser_data_parsing_rules_subject.png)
7.  #### 添加筛选发件人邮箱地址的规则
    ![Alt text](image/mailparser_data_parsing_rules_sender.png)
8.  #### 筛选出收件人邮箱地址的规则  
    ![Alt text](image/mailparser_data_parsing_rules_receiver.png)
9.  #### 现在你的规则列表看起来应该是这样子的  
    ![Alt text](image/role.png)
10. #### 接下来我们添加一个下载URL
    ![Alt text](image/downloads.png)
11. #### 下载URL设置  
    ![Alt text](image/downloadurlsetting.png)
12. #### 收件设置
    ![Alt text](image/setting.png)
    ```
    邮件标题:
    EUserv - PIN for the Confirmation of a Security Check
    ```

13. ### 终于我们可以拿到Mailparser的URL_ID了
    ![Alt text](image/geturlid.png)
    ### 填写到python代码中
## PIN码邮件模板
```
From：	     EUserv Support <support@euserv.de>
To：	         你的绑定@邮箱.康姆
Subject：	 EUserv - PIN for the Confirmation of a Security Check
Content-Type: text/plain; charset = utf-8
Dear 你的名字,

you have just requested a PIN for confirmation of a security check at EUserv. If you have not requested the PIN then ignore this email.

PIN:
123456

PLEASE NOTE: If you already have requested a new PIN for the same process this PIN is invalid. Also this PIN is only valid within the session in which it has been requested. This means the PIN is invalid if you for example change the browser or if you logout and perform a new login.


Sincerely,
Your customer support EUserv

--
Web ................: http://www.euserv.com
Login control panel.: https://support.euserv.com
FAQ ................: http://faq.euserv.com
Help & Guides.......: http://wiki.euserv.com
Community / Forum...: http://forum.euserv.com
Mailing-Liste ......: http://www.euserv.com/en/?show_contact=mailinglist
Twitter ............: http://twitter.com/euservhosting
Facebook ...........: http://www.facebook.com/euservhosting
--

EUserv Internet
is a division of
ISPpro Internet KG

Postal address:
ISPpro Internet KG
Division EUserv Internet
P.O. Box 2224
07622 Hermsdorf
GERMANY

Support-Phone: +49 (0) 3641 3101011 (English speaking)

Administration:
ISPpro Internet KG
Neue Str. 4
D-07639 Bad Klosterlausnitz
GERMANY

Management...............: Dirk Seidel
Register.................: AG Jena, HRA 202638
VAT Number...............: 162/156/36600
Tax office ..............: Jena
International VAT Number.: DE813856317
```
