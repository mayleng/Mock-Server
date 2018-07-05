官网：https://github.com/dreamhead/moco  (官网有更多api 使用方法)

1.安装jdk

2.下载jar包 Standalone Moco Runner    moco-runner-0.10.0-standalone.jar

3.编辑相关的配置文件config.json   config文件和jar包必须在同一级目录

4.在jar包路径下打开cmd窗口
  运行命令 Java -jar moco-runner-0.10.0-standalone.jar http -p 12306 -c config.json
  http：表示是http的接口
  https:表示https的接口（定义https时，访问HTTPS会出错）因为HTTPS需要证书（有该需求时必然有证书）
  start：


  -p 表示绑定的端口   此处为12306 
  -c 表示json文件


5.有参数的URL：http://localhost:5638/user/getUser?name=jack

  { 
 "request" : { 
    "uri" : "/user/getUser",
     "queries": { "name":"jack" } 
  },
  "response" : {
       "text" : "Hey. I'm jack" 
     }  
}

6.POST , GET , PUT , DELETE 等方法

 {
  "request" :{
      "method" : "post",
       "uri": "/getUser",
      "forms" :{
          "name" : "jack"
       }
    },
  "response" : {
      "text" : "Hi. you get jack data"
    }
} 

7.Headers , Cookies , StatusCode
 这个是支持特定的头的和cookies是支持的，我们需要加的就是headers，cookies 和status属性，参考如下
 
{
  "request" :{
      "method" : "post",
      "headers" : {
        "content-type" : "application/json"
      }
    },
  "response" :{
      "status" : 200,
      "text" : "bar",
       "cookies" :{
          "login" : "true"
      },
       "headers" :{
          "content-type" : "application/json"
       }
    }
}

8.我们对返回的数据，可以定义为Json的，格式如下

{
      "request" :{
              "uri": "/getJson",
              "method" : "post",                  
       },
      "response" :{
          "status" : 200,
           "headers" :{
              "content-type" : "application/json"
           },
          "json": {
                "foo": "bar"
               }               
      }
}


9.重定向

{ 
  "request" : { "uri" : "/redirect" }, 
  "redirectTo" : "http://www.XXX.com" 
  }

10.延迟
我觉得这个还是个重要的属性，因为移动手机的网络环境很复杂，高RTT不是盖的，
网络延迟个几十秒的那也是正常的，所以我们需要一个latency

{
  "request" :{
      "text" : "foo"
    },
  "response" :{
      "latency": {
          "duration": 1,
          "unit": "second"
        }
    }
}

11.template
  上面的请求参数的值和返回值都是固定的，这自然太过死板。 
好在从0.8版本开始，Moco提供了template功能，可以动态的返回一些参数值，虽然是beta版本

{
    "request": {
        "uri": "/getUser2"
        },
    "response": {
          "text": {
             "template": "${req.queries['name']}"
                 }
        }
 }

但当我们的请求是localhost:5638/getUser2?name=nameIsJack,那么返回的数据是nameIsJack

"template": "${req.version}"
"template": "${req.method}"
"template": "${req.content}"
"template": "${req.headers['foo']}"
"template": "${req.queries['foo']}"
"template": "${req.forms['foo']}"
"template": "${req.cookies['foo']}"


12.Event事件
从0.9版本开始，mock提供了event方法，什么意思呢 
有时候，我们请求一些特定接口的时候，我们可能需要去请求别的地址，从而才能完成请求。 
例如OAuth等，牵涉到第三方的情况。这时候，Event就派上大用场了。

{
    "request": {
        "uri" : "/event"
    },
    "response": {
        "text": "event"
    },
    "on": {
        "complete": {
            "get" : {
                "url" : "http://another_site/OAuth?xxx=xxxx"
            }
        }
    }
}


13.异步请求 Asynchronous
  前面的请求默认都是同步的，这意味着只有等到服务器处理完，结果才会返回给客户端 
如果你的实际情况不是这样，需要来点异步的，那么从0.9版本开始，有这个功能了，另外你还可以延迟个5秒，像这样

{
        "request": {
            "uri" : "/event"
        },
        "response": {
            "text": "event"
        },
        "on": {
            "complete": {
            "async" : "true",
            "latency" : 5000,
                "post" : {
                    "url" : "http://www.baidu.com",
                    "content": "content"
                }
            }
        }
    }


14.分模块
  我们的请求很显然是有很多的，例如有u 
ser模块的userApi业务，聊天模块的chatApi业务等等， 
如果把这些不同的模块的业务都写在一个文件里面，那就太难看了，为此，需要一些明智点的解决方案 
下面以一个TodoList的作为介绍：

[ { "context": "/user", "include": "user.json" }, { "context": "/todo", "include": "todo.json" } ]

当我们访问 
http://localhost:12306/user/create 和 http://localhost:12306/todo/getAll时候， 
会跳到后面对应的json再处理一遍

//user
[ { "request" : { "uri" : "/create" }, "response" : { "text" : "这是创建用户请求" } } ]
//todo
[ { "request" : { "uri" : "/getAll" }, "response" : { "text" : "这是获取用户所有Todo的请求" } } ]




Moco支持在全局的配置文件中引入其他配置文件，这样就可以分服务定义配置文件，便于管理。 
配置好文件，在全局文件中引入即可： 
全局配置如下

 java -jar moco-runner-standalone.jar start -p 5638 -g data.json

 注意，此时需要通过参数 -g 在加载全局配置文件。,使用的不是-c了


15.json request
{
  "request":
    {
      "text":
        {
          "json": "{\"foo\":\"bar\"}"
        }
    },
  "response":
    {
      "text": "foo"
    }
}

Since 0.9.2版本之后：
{
    "request": {
        "json": {
            "foo": "bar"
        }
    },
    "response": {
        "text": "foo"
    }
}
















