node-renren
===========

nodejs版人人网oauth认证和api sdk

开发中，不可用，可用后会删除此警告

#特色

* 对接口分组。
* 无需关心scope，传入你要使用的接口分组，自动生成scope。
* 集成oauth2.0认证
* 错误处理
* 规范的代码
* 支持图片上传

#支持的api

![支持的api](http://ww3.sinaimg.cn/large/6663ae3cjw1dy5f22a3jsj.jpg)

#人人网api说明：

http://wiki.dev.renren.com/wiki/API

#参与者致谢：

https://github.com/willerce

#捐助

如果觉得有用，请捐几块钱支持下作者，点这里捐助：https://me.alipay.com/mier

如果有问题，请提交issues，或者联系我：676588498。偶会尽量快速解决，谢啦。

#示例：

具体跟express的配合见：test/test.js

```

 var config = {
        app_key:"99754adae54e49bc826da1144ad2659d",
        app_secret:"ddf05a79792a4a0aac6cfb42755e25c9",
        redirect_uri:"http://localhost:8080/sina_auth_cb",
        api_group: ["blog","photos"],
        access_token:req.cookies.token
    }
var api = new RenRen(config);
//发送一篇日志
api.blog.addBlog({
        title:("hello nodejs !"),
        content:"this blog is create by nodejs :https://github.com/xinyu198736/node-renren",
        access_token:(req.cookies.token)
    }, function (error,data) {
        console.log(data); //人人网的api错误信息不统一，不能统一处理，这里只是返回一个字符串，自己处理。
    });
//获取两个好友的共同好友并发送一条状态
api.friends.getSameFriends({
        uid1:83838506,
        uid2:230901848,
        fields:"uid,name",
        access_token:(req.cookies.token)
    }, function (error,data) {
        console.log(data);
        var data = JSON.parse(data);
        var text="系统判断，我和@孙歌(230901848) 的共同好友有："
        data.friends.forEach(function(person){
            text+="@"+person.name+"("+person.uid+")"+" ";
        })
        api.status.set({
            status:text,
            access_token:(req.cookies.token)
        })
    }); 
//上传一个照片
api.photos.upload({
        upload:path.join(__dirname, "/test.jpg"),
        caption:"upload by nodejs"
    },function(error,data){
        console.log(data)
    })
```

oauth认证方法：

```
var config = {
        app_key:"99754adae54e49bc826da1144ad2659d",
        app_secret:"ddf05a79792a4a0aac6cfb42755e25c9",
        redirect_uri:"http://localhost:8080/sina_auth_cb",
        api_group: ["blog","photos"],
        access_token:req.cookies.token
    }
var app_auth = {
    auth:function (req, res) {
        var api = new RenRen(config);
        var auth_url = api.oauth.authorize();
        res.redirect(auth_url);
        res.end();
    },
    sina_auth_cb:function (req, res) {
        var code = req.query.code;
        var api = new RenRen(config);
        api.oauth.accesstoken({code:code}, function (data) {
            res.cookie("token", data.access_token);
            res.redirect('oauth');
            res.end();
        })

    }
}
```

