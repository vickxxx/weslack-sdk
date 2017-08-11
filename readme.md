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
        "atWho": "dwe", # 被@人昵称
        "atWhoRemark": "sss", # 被@人备注
        "isgroup": True, # 是否为群聊
        "selfDisplayName": "", # 机器人在群内昵称
        "chatroomName":"", # 群聊名称
    }

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
