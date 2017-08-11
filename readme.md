# weslack-sdk使用帮助

> welslack-sdk 将wx操作封装成类，通过操作wx对象执行相应操作

### 依赖安装

    weslack使用环境为python3
    相关依赖安装:
    执行pip3 install -r requirements.txt

### 引入包以及初始化

```python
from weslack_sdk import wx
# 引入库wx类

wx_instance = wx(sn, secret)
# 实例化微信操作对象,
# sn: weslack序列号,由weslack提供
# secret: weslack密钥,由weslack提供

```

### 发送消息

```python
    wx_instance.send_msg(wx_name, isgroup=True,
                         remark_name="",
                         text=None, filepath=None)
    # wx_name 接收人昵称 | 非必需
    # isgroup 是否为群 | 非必需,默认为真
    # remark_name 备注名称,群为机器人在群内的昵称| 非必需,与wx_name至少一
    # text 消息文本内容 | 非必需
    # filepath 文件路径 | 非必需, 但与text必需有一个

    # 文本消息可以和文件内容同时发送,weslack将根据文件头信息自动识别图片(gif, jpg, png),视频(mp4),和附件(其他)格式
    # 返回内容为是否发送成功"{errcode:0, errmsg:"成功"}"(str)
```

### 获取好友信息

```python
    wx_instance.get_friends_lst()
    # 获取好友昵称列表,失败返回[]
    wx_instance.friends
    # 所有好友信息字典,以昵称为key,value(字典)中包含NickName(昵称),UserName(临时用户id),RemarkName(备注名),Signature(个性签名),Province(省份),City(城市),Sex(性别)等信息
    wx_instance.get_friend(nick_name="",remark_name="")
    # 返回好友信息,nick_name为昵称,remark_name为备注名称,无参数调用返回当前机器人信息
```

### 获取群聊信息

```python
    wx_instance.get_chatrooms_lst()
    # 获取机器人所在群昵称列表,失败返回[]
    wx_instance.chatrooms
    # 所有群聊信息字典,以机器人所在群昵称为key,value(字典)NickName(昵称),UserName(临时用户id),MemberList(成员列表),IsAdmin(机器人是否管理员,目测已废),MemberCount(成员数),selfDisplayName(机器人所在群昵称)
    wx_instance.get_chatroom(nick_name="",remark_name="")
    # 返回群聊信息,nick_name为群昵称,remark_name为机器人所在群昵称,无参数调用返回当前机器人信息
```

### 消息webhook

```python
    wx_instance.get_chatroom_callback()
    # 群事件注册回调地址,未注册返回""
    wx_instance.set_chatroom_callback(url)
    # 设置群事件回调地址,成功返回0
    wx_instance.get_chat_callback()
    # 实时聊天推送地址,未设置为空
    wx_instance.set_chat_callback(url)
    # 设置实时聊天推送地址

    群事件webhook请求信息:
    方法: POST
    body: {
        "NickName": "xx", # 群事件涉及人昵称,可能为空
        "DisplayName": "", # 群事件涉及人群内昵称,可能为空
        "RemarkName": "", # 群事件涉及人备注名,可能为空
        "event": "add"(加入)|"remove"(退出)等,  # 群事件类型
        "t": 12234123431, # 群事件时间戳
    }

    实时聊天推送:
    方法: POST
    body: {
        "NickName": "xx", # 群聊发送人昵称
        "DisplayName": "", # 群聊群内昵称,可能为空
        "RemarkName": "", # 群聊备注名（如果是好友）,可能为空 【暂未实现】
        "t": 12234123431, # 群事件时间戳
        "msgId": 12123, # 消息id
        "msgType": "Text" # | "Recording" | "Picture" |"Video" |"Attachment" |"Note"
            # "Card"(名片)|"Sharing", # 消息类型
        "text": "xx", # 消息文本内容
        "isAt": True, # 是否是@事件
        "atSelf": False, # 是否@自己
        "atWho": "dwe", # 被@人昵称 [暂未实现]
        "atWhoRemark": "sss", # 被@人备注 [暂未实现]
        "isgroup": True, # 是否为群聊
        "selfDisplayName": "", # 机器人在群内昵称
        "chatroomName":"", # 群聊名称
    }
	
	# 输出信息,文本
{'NickName': '老衲有理了',
 'RemarkName': '',
 'Username': '@d91be73042eeabac073d993c489161f0',
 'msgId': '1912272070477312894',
 'msgType': 'Text', 
 'pyQuanPin': 'laonayoulile',
 't': 1502422012,
 'text': '1'} # 包括系统默认表情 'text': '[微笑]'

 # 输出信息,语音
 
 {'NickName': '老衲有理了',
 'RemarkName': '',
 'Username': '@d91be73042eeabac073d993c489161f0',
 'filename': '170811-113344.mp3',
 'pyQuanPin': 'laonayoulile',
 'msgId': '8878382022220349020',
 'msgType': 'Recording',
 't': 1502422424,
 'text': '',
 'voiceLength': 3180}
 
 # 输出信息, 图片
 {'NickName': '老衲有理了',
 'RemarkName': '',
 'Username': '@d91be73042eeabac073d993c489161f0',
 'filename': '170811-114541.png',
 'imgHeight': 150,
 'imgWidth': 125,
 'msgId': '5255827694134084268',
 'msgType': 'Picture',
 'pyQuanPin': 'laonayoulile',
 't': 1502423141,
 'text': ''} # 包括系统购买表情包,该文件无法成功下载, 用户自定义表情,可以下载
 
 # 输出视频
 
{'NickName': '老衲有理了',
 'RemarkName': '',
 'Username': '@d91be73042eeabac073d993c489161f0',
 'filename': '170811-122407.mp4',
 'msgId': '1086854517769505496',
 'msgType': 'Video',
 'playLength': 6,
 'pyQuanPin': 'laonayoulile',
 't': 1502425447,
 'text': ''}
 
 # 输出附件
 
 {'NickName': '老衲有理了',
 'RemarkName': '',
 'Username': '@d91be73042eeabac073d993c489161f0',
 'fileSize': '120477',
 'filename': '项目.mp',
 'msgId': '9086062804589195742',
 'msgType': 'Attachment',
 'pyQuanPin': 'laonayoulile',
 't': 1502425825,
 'text': ''}
 
 # 输出通知[红包，转账]
 {'NickName': '老衲有理了',
 'RemarkName': '',
 'Username': '@d91be73042eeabac073d993c489161f0',
 'msgId': '7184802347113302643',
 'msgType': 'Note',
 'pyQuanPin': 'laonayoulile',
 't': 1502425874,
 'text': '收到红包，请在手机上查看'} # 转账：老衲有理了向你转账0.01元
 
 # 输出名片
 {'NickName': '老衲有理了',
 'RemarkName': '',
 'Username': '@d91be73042eeabac073d993c489161f0',
 'msgId': '3085576819082358692',
 'msgType': 'Card',
 'pyQuanPin': 'laonayoulile',
 't': 1502426591,
 'text': {'Alias': '',  # 内容为字典
          'AttrStatus': 32,
          'City': '不丹',
          'Content': '',
          'NickName': '《A》<span class="emoji emoji1f493"></span>l<span '
                      'class="emoji emoji1f46f"></span>洋<span class="emoji '
                      'emoji1f48b"></span>洋', # 昵称中包含emoji等
          'OpCode': 0,
          'Province': '不丹',
          'QQNum': 0,
          'Scene': 17,
          'Sex': 2,
          'Signature': '',
          'Ticket': '',
          'UserName': '@f42d57ad19989b1115eb59b8df7225058ccca959ca7ae21cf6b85b3db507ce1f',
          'VerifyFlag': 0}}
 
# 输出地址信息
{'NickName': '老衲有理了',
 'RemarkName': '',
 'Username': '@d91be73042eeabac073d993c489161f0',
 'msgId': '6492503751072481390',
 'msgType': 'Map',
 'pyQuanPin': 'laonayoulile',
 't': 1502426867,
 'text': '北京市朝阳区四惠京通商务楼(京通快速公路北)',
 'url': 'http://apis.map.qq.com/uri/v1/geocoder?coord=39.910968,116.508298'}
 
 
 # 输出分享
 {'NickName': '老衲有理了',
 'RemarkName': '',
 'Username': '@d91be73042eeabac073d993c489161f0',
 'msgId': '9043330962919561897',
 'msgType': 'Sharing',
 'pyQuanPin': 'laonayoulile',
 'raw': '<?xml version="1.0"?>\n'  # 消息原格式
        '<msg>\n'
        '\t<appmsg appid="" sdkver="0">\n'
        '\t\t<title>女子编程真的不如男？真相告诉我们这不过是直男癌的自嗨</title>\n'
        '\t\t<des>Google真的反对了言论自由吗？</des>\n'
        '\t\t<action>view</action>\n'
        '\t\t<type>5</type>\n'
        '\t\t<showtype>0</showtype>\n'
        '\t\t<content />\n'
        '\t\t'
        '<url>http://mp.weixin.qq.com/s?__biz=MjM5MjM3NzQwMA==&amp;mid=2650984008&amp;idx=1&amp;sn=4839f594584ed03f2abbca996b231c00&amp;chksm=bd51511f8a26d809852552ba535494f2e2f81620cfa760e4801e27daf65eb255746021992e87&amp;mpshare=1&amp;scene=1&amp;srcid=0811vsKfCAiDojl7eE0Im3VE#rd</url>\n'
        '\t\t<dataurl />\n'
        '\t\t<lowurl />\n'
        '\t\t<lowdataurl />\n'
        '\t\t<recorditem><![CDATA[]]></recorditem>\n'
        '\t\t'
        '<thumburl>http://mmbiz.qpic.cn/mmbiz_png/EfY115GicZB5J7MOdXXdhRaLhV4Ttl1nD6HedgRXuaLK2SEhABZuVZp3675nmMCF725wYVvG4icHj4a1fm8xicJjw/300?wx_fmt=png&amp;wxfrom=1</thumburl>\n'
        '\t\t<extinfo />\n'
        '\t\t<sourceusername />\n'
        '\t\t<sourcedisplayname />\n'
        '\t\t<commenturl />\n'
        '\t\t<appattach>\n'
        '\t\t\t<totallen>0</totallen>\n'
        '\t\t\t<attachid />\n'
        '\t\t\t<emoticonmd5 />\n'
        '\t\t\t<fileext />\n'
        '\t\t\t<aeskey />\n'
        '\t\t</appattach>\n'
        '\t</appmsg>\n'
        '\t<fromusername>Vicksml</fromusername>\n'
        '\t<scene>0</scene>\n'
        '\t<appinfo>\n'
        '\t\t<version>1</version>\n'
        '\t\t<appname></appname>\n'
        '\t</appinfo>\n'
        '\t<commenturl></commenturl>\n'
        '</msg>\n',
 't': 1502432423,
 'text': '女子编程真的不如男？真相告诉我们这不过是直男癌的自嗨', # 链接分享为文章标题
 'url': 'http://mp.weixin.qq.com/s?__biz=MjM5MjM3NzQwMA==&amp;mid=2650984008&amp;idx=1&amp;sn=4839f594584ed03f2abbca996b231c00&amp;chksm=bd51511f8a26d809852552ba535494f2e2f81620cfa760e4801e27daf65eb255746021992e87&amp;mpshare=1&amp;scene=1&amp;srcid=0811vsKfCAiDojl7eE0Im3VE#rd'}  # 卡券等该参数为https://support.weixin.qq.com/cgi-bin*****
 
 # 输出群文本
{'NickName': 'ooo',
 'chatroomName': 'ooo',
 'chatroomQuanPin': 'ooo',
 'fromDisplayName': '1111',
 'fromNickName': '老衲有理了',
 'fromUserName': '@d91be73042eeabac073d993c489161f0',
 'atSelf': False,
 'isgroup': False,
 'msgId': '6911839328836778181',
 'msgType': 'Text',
 'pyQuanPin': 'ooo',
 't': 1502441016,
 'text': '1',
 'username': '@@32f23df58fb1da775c1c7811e43b848b3faacb5c144fc3ca2425530327f8368f'}
 
 # 输出群图片
 {'NickName': 'ooo',
 'atSelf': False,
 'chatroomName': 'ooo',
 'chatroomQuanPin': 'ooo',
 'filename': '170811-170804.png',
 'fromDisplayName': '1111',
 'fromNickName': '老衲有理了',
 'fromUserName': '@d91be73042eeabac073d993c489161f0',
 'imgHeight': 150,
 'imgWidth': 125,
 'isgroup': True,
 'msgId': '8714147927345821492',
 'msgType': 'Picture',
 'pyQuanPin': 'ooo',
 't': 1502442484,
 'text': '',
 'username': '@@32f23df58fb1da775c1c7811e43b848b3faacb5c144fc3ca2425530327f8368f'}

 # 输出群视频
 {'NickName': 'ooo',
 'atSelf': False,
 'chatroomName': 'ooo',
 'chatroomQuanPin': 'ooo',
 'filename': '170811-171217.mp4',
 'fromDisplayName': '1111',
 'fromNickName': '老衲有理了',
 'fromUserName': '@d91be73042eeabac073d993c489161f0',
 'isgroup': True,
 'msgId': '4281900100578744004',
 'msgType': 'Video',
 'playLength': 6,
 'pyQuanPin': 'ooo',
 't': 1502442737,
 'text': '',
 'username': '@@32f23df58fb1da775c1c7811e43b848b3faacb5c144fc3ca2425530327f8368f'}
 
 # 输出群附件
 {'NickName': 'ooo',
 'atSelf': False,
 'chatroomName': 'ooo',
 'chatroomQuanPin': 'ooo',
 'fileSize': '120477',
 'filename': '项目.mp',
 'fromDisplayName': '1111',
 'fromNickName': '老衲有理了',
 'fromUserName': '@d91be73042eeabac073d993c489161f0',
 'isgroup': True,
 'msgId': '5398171740057203265',
 'msgType': 'Attachment',
 'pyQuanPin': 'ooo',
 't': 1502442826,
 'text': '',
 'username': '@@32f23df58fb1da775c1c7811e43b848b3faacb5c144fc3ca2425530327f8368f'}
 
 # 输出群语音
 {'NickName': 'ooo',
 'atSelf': False,
 'chatroomName': 'ooo',
 'chatroomQuanPin': 'ooo',
 'filename': '170811-171453.mp3',
 'fromDisplayName': '1111',
 'fromNickName': '老衲有理了',
 'fromUserName': '@d91be73042eeabac073d993c489161f0',
 'isgroup': True,
 'msgId': '4167799924484047334',
 'msgType': 'Recording',
 'pyQuanPin': 'ooo',
 't': 1502442893,
 'text': '',
 'username': '@@32f23df58fb1da775c1c7811e43b848b3faacb5c144fc3ca2425530327f8368f',
 'voiceLength': 2700}
 
 #输出群红包[通知]
 {'NickName': 'ooo',
 'atSelf': False,
 'chatroomName': 'ooo',
 'chatroomQuanPin': 'ooo',
 'fromDisplayName': 'weslack-te',
 'fromNickName': 'weslack-test',
 'fromUserName': '@2e20efade37766af8a172bf5ad6aad1135f3e79a70fd9df71f005d2ed52d9dee',
 'isgroup': True,
 'msgId': '7068968337958789656',
 'msgType': 'Note',
 'pyQuanPin': 'ooo',
 't': 1502442944,
 'text': '收到红包，请在手机上查看',
 'username': '@@32f23df58fb1da775c1c7811e43b848b3faacb5c144fc3ca2425530327f8368f'}
 
 #输出群名片
 {'NickName': 'ooo',
 'atSelf': False,
 'chatroomName': 'ooo',
 'chatroomQuanPin': 'ooo',
 'fromDisplayName': '1111',
 'fromNickName': '老衲有理了',
 'fromUserName': '@d91be73042eeabac073d993c489161f0',
 'isgroup': True,
 'msgId': '2698284051410254223',
 'msgType': 'Card',
 'pyQuanPin': 'ooo',
 't': 1502443048,
 'text': {'Alias': '',
          'AttrStatus': 32,
          'City': '中国',
          'Content': '',
          'NickName': '阿彪',
          'OpCode': 0,
          'Province': '江西',
          'QQNum': 0,
          'Scene': 17,
          'Sex': 1,
          'Signature': '',
          'Ticket': '',
          'UserName': '@ab03c03893ec0fdcc152ec5237d3350dd3b6e5cf71d15049b54a9891099e263b',
          'VerifyFlag': 0},
 'username': '@@32f23df58fb1da775c1c7811e43b848b3faacb5c144fc3ca2425530327f8368f'}
 
 #输出群分享
 {'NickName': 'ooo',
 'atSelf': False,
 'chatroomName': 'ooo',
 'chatroomQuanPin': 'ooo',
 'fromDisplayName': '1111',
 'fromNickName': '老衲有理了',
 'fromUserName': '@d91be73042eeabac073d993c489161f0',
 'isgroup': True,
 'msgId': '5587398698639216514',
 'msgType': 'Sharing',
 'pyQuanPin': 'ooo',
 'raw': '<?xml version="1.0"?>\n'
        '<msg>\n'
        '\t<appmsg appid="wx726f306f716ebe3f" sdkver="0">\n'
        '\t\t<title>一个小学生的中年危机</title>\n'
        '\t\t<des>一个小学生的中年危机</des>\n'
        '\t\t<action>view</action>\n'
        '\t\t<type>5</type>\n'
        '\t\t<showtype>0</showtype>\n'
        '\t\t<content />\n'
        '\t\t<url>http://www.pingwest.com/?p=128682</url>\n'
        '\t\t<dataurl />\n'
        '\t\t<lowurl />\n'
        '\t\t<lowdataurl />\n'
        '\t\t<recorditem><![CDATA[]]></recorditem>\n'
        '\t\t<thumburl />\n'
        '\t\t<extinfo />\n'
        '\t\t<sourceusername />\n'
        '\t\t<sourcedisplayname />\n'
        '\t\t<commenturl />\n'
        '\t\t<appattach>\n'
        '\t\t\t<totallen>0</totallen>\n'
        '\t\t\t<attachid />\n'
        '\t\t\t<emoticonmd5 />\n'
        '\t\t\t<fileext />\n'
        '\t\t\t'
        '<cdnthumburl>30580201000451304f02010002040ff7803d02033d0af50204764c977b0204598d708f042d6175706170706d73675f323430666437313736646437646165305f313530323434313631353334325f383633320201000201000400</cdnthumburl>\n'
        '\t\t\t<cdnthumblength>31649</cdnthumblength>\n'
        '\t\t\t<cdnthumbheight>150</cdnthumbheight>\n'
        '\t\t\t<cdnthumbwidth>290</cdnthumbwidth>\n'
        '\t\t\t<aeskey>ede19cf132504961ba35c04e81735199</aeskey>\n'
        '\t\t\t'
        '<cdnthumbaeskey>ede19cf132504961ba35c04e81735199</cdnthumbaeskey>\n'
        '\t\t\t<encryver>1</encryver>\n'
        '\t\t</appattach>\n'
        '\t</appmsg>\n'
        '\t<fromusername>Vicksml</fromusername>\n'
        '\t<scene>0</scene>\n'
        '\t<appinfo>\n'
        '\t\t<version>2</version>\n'
        '\t\t<appname>PingWest品玩</appname>\n'
        '\t</appinfo>\n'
        '\t<commenturl></commenturl>\n'
        '</msg>\n'
        '\n',
 't': 1502443079,
 'text': '一个小学生的中年危机',
 'url': 'http://www.pingwest.com/?p=128682',
 'username': '@@32f23df58fb1da775c1c7811e43b848b3faacb5c144fc3ca2425530327f8368f'}

    以上body数据均经过fernet加密,解密方法为
    from cryptography.fernet import Fernet
    fernet_o = Fernet(secret) # secret为weslack分配密钥
    decrypt_str = fernet_o.decrypt(body)
```

### 搜索聊天历史

```python
wx_instance.get_chatmsg(starttime=123, endtime=456, limit=100)
# starttime: 开始时间戳
# endtime: 结束时间戳
# limit: 返回数限制
# 以上默认为最近1小时,100条数据


返回格式为列表,列表内容与实时推送格式一致
```
