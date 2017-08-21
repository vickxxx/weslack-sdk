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

wx_instance = wx(sn, secret, weslack_url)
# 实例化微信操作对象,
# sn: weslack序列号,由weslack提供
# secret: weslack密钥,由weslack提供
# weslack_url: weslack服务器http地址,weslack提供

```

### 发送消息

```python
    wx_instance.send_msg(wx_name=None, remark_name=None, isgroup=True, text=None,
                        filepath=None)
    # wx_name 接收人昵称 | 非必需
    # remark_name 备注名称,群为机器人在群内的昵称| 非必需,与wx_name至少一
    # isgroup 是否为群 | 非必需,默认为真
    # text 消息文本内容 | 非必需
    # filepath 文件路径 | 非必需, 但与text必需有一个
    # 文本消息可以和文件内容同时发送,weslack将根据文件头信息自动识别图片(gif, jpg, png),视频(mp4),和附件(其他)格式
    # 返回内容为是否发送成功"{errcode:0, errmsg:"成功"}"(str)
    # 执行成功: 
        {'BaseResponse':
          {'ErrMsg': '请求成功', 'RawMsg': '请求成功', 'Ret': 0},
          'LocalID': '15030534257827',
          'MsgID': '1769472953213047700',
           'errcode': 0,
           'errmsg': '成功'}
    # 异常:
        {"errcode": （错误码负值）-4, "errmsg": (错误原因)"text,filepath不能全部为空"}
```

### 获取好友信息

```python
    wx_instance.get_friends_lst()
    # 获取好友昵称列表,失败返回[]
    wx_instance.friends
    # 所有好友信息列表包含NickName(昵称),UserName(临时用户id),RemarkName(备注名),Signature(个性签名),Province(省份),City(城市),Sex(性别)等信息
    wx_instance.get_friend(nick_name="", remark_name="", username="")
    # 返回好友信息列表，重复昵称等列表数不为1,nick_name为昵称,remark_name为备注名称,无参数调用返回空列表
```

### 获取群聊信息

```python
    wx_instance.get_chatrooms_lst()
    # 获取机器人所在群昵称列表,失败返回[]
    wx_instance.chatrooms
    # 所有群聊信息字典,以机器人所在群昵称为key,value(字典)NickName(昵称),UserName(临时用户id),MemberList(成员列表),IsAdmin(机器人是否管理员,目测已废),MemberCount(成员数),selfDisplayName(机器人所在群昵称)
    wx_instance.get_chatroom(self, nick_name="", self_remark_name="", username="")
    # 返回群聊信息,nick_name为群昵称,
    # self_remark_name,
    # 无参数调用返回[],正常返回群信息列表,个数为1时群唯一
```

### 消息webhook

```python
    wx_instance.get_chatroom_callback()
    # 群事件注册回调地址,未注册返回""
    wx_instance.set_chatroom_callback(url)
    # 设置群事件回调地址,成功返回0(str)
    wx_instance.get_chat_callback()
    # 实时聊天推送地址,未设置为空
    wx_instance.set_chat_callback(url)
    # 设置实时聊天推送地址

    群事件webhook请求信息:
    方法: POST
    body: {
        "isgroup": true, # 是否群消息
        "atSelf": false,
        "msgId": "8758474135318225246", # 消息id
        "text": "\"rrrrr\"邀请\"这一年，十八岁\"加入了群聊",
        "event": "NewInRoom", # 事件,新人加入(NewInRoom)|机器人被加入(selfInNewRoom)
        "selfRemarkName": "", # 机器人号群内昵称
        "Username": "@@a39be3d32b32a7a4abc11ac365aeaadbf26ff4823db280750dacfcc30b5597d5",
        "inviter": "rrrrr", # 邀请人群内昵称(优先)或昵称
        "chatroomQuanPin": "oooq", # 聊天群全拼
        "fromDisplayName": "",
        "t": 1503283124,
        "fromUserName": "@599a093641f6e16e3e159d13f20700d8f0e5ded99007f341497e15a2aa7b1677",
        "chatroomName": "oooq",  # 聊天名称
        "fromNickName": "POC Bot",
        "msgType": "Note",
        "newNicknames": [  # 新加入人昵称,列表
            "这一年，十八岁"
        ]
    }

    实时聊天推送:
    方法: POST
    body: 
        # 文本
        {
        "isgroup": true,  # 是否群
        "atSelf": false, # 是否at自己
        "msgId": "787689431542809558",
        "text": "vickxxx\n－\n😈 在 [weslack-sdk]项目中推送了:\n[修改发送消息描述]\n新增了[0]个文件\n删除了[0]个文件\n修改了[1]个文件\n详情请访问【Github】:http://t.cn/R9uMeId", # 文本内容 包括系统默认表情 'text': '[微笑]'
        "selfRemarkName": "", # 机器号所在群备注
        "chatroomName": "Ein+WeSlack", # 群昵称
        "msgType": "Text", # 消息类型
        "fromDisplayName": "",
        "fromUserName": "@8b224d5aaf6a765250f1a9dc3910ddd2aacc3937982502aaf780088a0ccfc402",
        "t": 1503282480, # 消息时间戳
        "chatroomQuanPin": "EinWeSlack",
        "fromNickName": "weslack",  # 消息发送者
        "Username": "@@4c728297d3d6aa9666f9f0cc2322e2f6397520782fea49f51d4c1e5eaca65440" # 群聊Username(临时id)
    }
	

    # 群语音,除文本消息外附加字段以及区别字段
    
    {
    'filename': '170811-113344.mp3', # 音频文件名称
    'msgType': 'Recording', # 类型
    'text': '', # 为空
    'voiceLength': 3180} # 声音长度,单位ms
 
    # 图片,除文本消息外附加字段以及区别字段
    {
    'filename': '170811-114541.png',
    'imgHeight': 150, # 图片高
    'imgWidth': 125, # 图片宽
    'msgType': 'Picture',
    'text': ''} # 包括系统购买表情包,该文件无法成功下载, 用户自定义表情,可以下载
 
    # 视频,除文本消息外附加字段以及区别字段
 
    {
    'filename': '170811-122407.mp4',
    'msgType': 'Video',
    'playLength': 6, # 视频长度 单位s
    'text': ''}
 
    # 附件,除文本消息外附加字段以及区别字段
    
    {
    'fileSize': '120477', # 文件大小
    'filename': '项目.mp', # 文件名称
    'msgType': 'Attachment',
    'text': ''}
 
    # 输出通知[红包，转账]
    {
    'msgType': 'Note',
    'text': '收到红包，请在手机上查看'} # 转账：老衲有理了向你转账0.01元
    
    # 输出名片
    {
    'msgType': 'Card',
    'text': {'Alias': '',  # 内容为名片内容
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
        {
        'msgType': 'Map',
        'text': '北京市朝阳区四惠京通商务楼(京通快速公路北)',
        'url': 'http://apis.map.qq.com/uri/v1/geocoder?coord=39.910968,116.508298'}
        
        
        # 输出分享
        {
        'msgType': 'Sharing',
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
        'text': '女子编程真的不如男？真相告诉我们这不过是直男癌的自嗨', # 链接分享为文章标题
        'url': 'http://mp.weixin.qq.com/s?__biz=MjM5MjM3NzQwMA==&amp;mid=2650984008&amp;idx=1&amp;sn=4839f594584ed03f2abbca996b231c00&amp;chksm=bd51511f8a26d809852552ba535494f2e2f81620cfa760e4801e27daf65eb255746021992e87&amp;mpshare=1&amp;scene=1&amp;srcid=0811vsKfCAiDojl7eE0Im3VE#rd'}  # 卡券等该参数为https://support.weixin.qq.com/cgi-bin*****
 
    私聊文本:
            {
            "msgId": "6974765583220228797",
            "Username": "@c3441b7e42ec52d5eef0a44c1101818e",
            "text": "33333",
            "RemarkName": "rrrrr",
            "t": 1503284971,
            "NickName": "老衲有理了",
            "msgType": "Text",
            "pyQuanPin": "laonayoulile"
        }
    私聊其他类型,同群聊

    以上body数据均经过fernet加密,解密方法为
    from cryptography.fernet import Fernet
    fernet_o = Fernet(secret) # secret为weslack分配密钥
    decrypt_str = fernet_o.decrypt(body)
```

### 搜索聊天历史

```python
wx_instance.get_chatmsg(starttime=None, endtime=None, limit=100):
# starttime: 开始时间戳
# endtime: 结束时间戳
# limit: 返回数限制
# 以上默认为最近1小时,100条数据

返回格式为列表,列表内容与实时推送格式一致
```

### 文件下载

```python
    dl_file(group_name, b64_filename, local_filename="", local_dir="tmp",
            base_url="http://xxx"
            ):
    # group_name:消息体中username(临时id,@@开头)
    # b64_filename: urlsafe_b64encode编码过的消息体中filenaem
    # local_filename: 本地将要保存的文件名
    # local_dir: 本地文件保存路径
    # base_url: 下载服务器地址
    demo:
    # 以下代码见callback_server.py
    # msg_d为json解码后的msg消息字典
        if msg_d.get("msgType", "") in ["Picture", "Video", "Attachment"]:
            dl_file(msg_d.get("Username").replace("@", ""),
                    base64.urlsafe_b64encode(msg_d.get("filename", "").encode()).decode()
                    )
```

