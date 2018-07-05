������https://github.com/dreamhead/moco  (�����и���api ʹ�÷���)

1.��װjdk

2.����jar�� Standalone Moco Runner    moco-runner-0.10.0-standalone.jar

3.�༭��ص������ļ�config.json   config�ļ���jar��������ͬһ��Ŀ¼

4.��jar��·���´�cmd����
  �������� Java -jar moco-runner-0.10.0-standalone.jar http -p 12306 -c config.json
  http����ʾ��http�Ľӿ�
  https:��ʾhttps�Ľӿڣ�����httpsʱ������HTTPS�������ΪHTTPS��Ҫ֤�飨�и�����ʱ��Ȼ��֤�飩
  start��


  -p ��ʾ�󶨵Ķ˿�   �˴�Ϊ12306 
  -c ��ʾjson�ļ�


5.�в�����URL��http://localhost:5638/user/getUser?name=jack

  { 
 "request" : { 
    "uri" : "/user/getUser",
     "queries": { "name":"jack" } 
  },
  "response" : {
       "text" : "Hey. I'm jack" 
     }  
}

6.POST , GET , PUT , DELETE �ȷ���

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
 �����֧���ض���ͷ�ĺ�cookies��֧�ֵģ�������Ҫ�ӵľ���headers��cookies ��status���ԣ��ο�����
 
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

8.���ǶԷ��ص����ݣ����Զ���ΪJson�ģ���ʽ����

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


9.�ض���

{ 
  "request" : { "uri" : "/redirect" }, 
  "redirectTo" : "http://www.XXX.com" 
  }

10.�ӳ�
�Ҿ���������Ǹ���Ҫ�����ԣ���Ϊ�ƶ��ֻ������绷���ܸ��ӣ���RTT���Ǹǵģ�
�����ӳٸ���ʮ�����Ҳ�������ģ�����������Ҫһ��latency

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
  ��������������ֵ�ͷ���ֵ���ǹ̶��ģ�����Ȼ̫�����塣 
���ڴ�0.8�汾��ʼ��Moco�ṩ��template���ܣ����Զ�̬�ķ���һЩ����ֵ����Ȼ��beta�汾

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

�������ǵ�������localhost:5638/getUser2?name=nameIsJack,��ô���ص�������nameIsJack

"template": "${req.version}"
"template": "${req.method}"
"template": "${req.content}"
"template": "${req.headers['foo']}"
"template": "${req.queries['foo']}"
"template": "${req.forms['foo']}"
"template": "${req.cookies['foo']}"


12.Event�¼�
��0.9�汾��ʼ��mock�ṩ��event������ʲô��˼�� 
��ʱ����������һЩ�ض��ӿڵ�ʱ�����ǿ�����Ҫȥ�����ĵ�ַ���Ӷ������������ 
����OAuth�ȣ�ǣ�浽���������������ʱ��Event�����ϴ��ó��ˡ�

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


13.�첽���� Asynchronous
  ǰ�������Ĭ�϶���ͬ���ģ�����ζ��ֻ�еȵ������������꣬����Ż᷵�ظ��ͻ��� 
������ʵ�����������������Ҫ�����첽�ģ���ô��0.9�汾��ʼ������������ˣ������㻹�����ӳٸ�5�룬������

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


14.��ģ��
  ���ǵ��������Ȼ���кܶ�ģ�������u 
serģ���userApiҵ������ģ���chatApiҵ��ȵȣ� 
�������Щ��ͬ��ģ���ҵ��д��һ���ļ����棬�Ǿ�̫�ѿ��ˣ�Ϊ�ˣ���ҪһЩ���ǵ�Ľ������ 
������һ��TodoList����Ϊ���ܣ�

[ { "context": "/user", "include": "user.json" }, { "context": "/todo", "include": "todo.json" } ]

�����Ƿ��� 
http://localhost:12306/user/create �� http://localhost:12306/todo/getAllʱ�� 
�����������Ӧ��json�ٴ���һ��

//user
[ { "request" : { "uri" : "/create" }, "response" : { "text" : "���Ǵ����û�����" } } ]
//todo
[ { "request" : { "uri" : "/getAll" }, "response" : { "text" : "���ǻ�ȡ�û�����Todo������" } } ]




Moco֧����ȫ�ֵ������ļ����������������ļ��������Ϳ��Էַ����������ļ������ڹ��� 
���ú��ļ�����ȫ���ļ������뼴�ɣ� 
ȫ����������

 java -jar moco-runner-standalone.jar start -p 5638 -g data.json

 ע�⣬��ʱ��Ҫͨ������ -g �ڼ���ȫ�������ļ���,ʹ�õĲ���-c��


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

Since 0.9.2�汾֮��
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
















