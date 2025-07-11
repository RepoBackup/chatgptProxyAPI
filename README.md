*经测试有些ip访问时会显示下图*

<img src="https://image.3001.net/images/20231116/1700131811_6555f3e30a1efc2db761f.png" height="50%" width="50%" /></a>


---
### Demo

[https://chatai.451024.xyz](https://chatai.451024.xyz)

> api

```url
https://openai.451024.xyz
```


```url
https://openai-proxy-api.pages.dev/api
```
***

>  新项目 [基于OpenAI的微信机器人](https://github.com/x-dr/wechat-bot)

***

### 演示站为公共服务，如有大规模使用需求请自行部署，演示站有点不堪重负

![worker](./docs/img/worker.png)


## 利用Cloudflare Worker中转api.openai.com

1. 新建一个 Cloudflare Worker
2. 复制 [cf_worker.js](https://cdn.jsdelivr.net/gh/x-dr/chatgptProxyAPI@main/cf_worker.js)  里的代码粘贴到 Worker 中并部署
3. 给 Worker 绑定一个没有被墙的域名
4. 使用自己的域名代替 api.openai.com


**[详细教程](./docs/cloudflare_workers.md)**



***



## 使用CloudFlare Pages进行中转

### 1、部署中转API+ Openai API余额查询 (使用sess-xxxx的Authorization查询，有效时间未知)

> [官方文档](https://developers.cloudflare.com/pages)

1. ~~Fork本项目~~ 点击[Use this template](https://github.com/x-dr/chatgptProxyAPI/generate)按钮创建一个新的代码库。
2. 登录到[Cloudflare](https://dash.cloudflare.com/)控制台.
3. 在帐户主页中，选择`pages`> ` Create a project` > `Connect to Git`
4. 选择你 Fork 的项目存储库，在`Set up builds and deployments`部分中，全部默认即可。


5. 点击`Save and Deploy`部署，然后点`Continue to project`即可看到访问域名


> 把官方接口的`https://api.openai.com`替换为`https://xxx.pages.dev` 即可

**Demo**

[https://chatai.451024.xyz](https://chatai.451024.xyz)

**[详细教程](./docs/cloudflare_pages.md)**


### 2、只部署中转API


**[详细教程](./docs/cloudflare_proxy_pages.md)**




## docker 部署（要境外vps） 

> 好像不支持sse 所以不建议

<details>

<summary>e.g.</summary>

```bash
docker run -itd --name openaiproxy \
            -p 3000:3000 \
            --restart=always \
           gindex/openaiproxy:latest
```

#### 使用

*api : http://vpsip:3000/proxy/v1/chat/completions*

```bash
curl --location 'http://vpsip:3000/proxy/v1/chat/completions' \
--header 'Authorization: Bearer sk-xxxxxxxxxxxxxxx' \
--header 'Content-Type: application/json' \
--data '{
   "model": "gpt-3.5-turbo",
  "messages": [{"role": "user", "content": "Hello!"}]
 }'

```

</details>



## 用法


<details>

<summary>JavaScript用fetch</summary>

```javascript
const requestOptions = {
    method: 'POST',
    headers: {
        "Authorization": "Bearer sk-xxxxxxxxxxxx",
        "Content-Type": "application/json"
    },
    body: JSON.stringify({
        "model": "gpt-3.5-turbo",
        "messages": [
            {
                "role": "user",
                "content": "hello word"
            }
        ]
    })
};

fetch("https://openai.1rmb.tk/v1/chat/completions", requestOptions)
    .then(response => response.text())
    .then(result => console.log(result))
    .catch(error => console.log('error', error));
  
```

</details>



<details>

<summary>用python</summary>

```python
import requests

url = "https://openai.1rmb.tk/v1/chat/completions"
api_key = 'sk-xxxxxxxxxxxxxxxxxxxx'

headers = {
  'Authorization': f'Bearer {api_key}',
  'Content-Type': 'application/json'
}

payload = {
  "model": "gpt-3.5-turbo",
  "messages": [
    {
      "role": "user",
      "content": "hello word"
    }
  ]
}

try:
    response = requests.post(url, headers=headers, json=payload)
    response.raise_for_status() # 抛出异常，如果响应码不是200
    data = response.json()
    print(data)
except requests.exceptions.RequestException as e:
    print(f"请求错误: {e}")
except json.JSONDecodeError as e:
    print(f"无效的 JSON 响应: {e}")
```

</details>



<details>
<summary>用nodejs chatgpt库</summary>

[transitive-bullshit/chatgpt-api](https://github.com/transitive-bullshit/chatgpt-api)

```javascript
import { ChatGPTAPI } from 'chatgpt'

async function example() {
  const api = new ChatGPTAPI({
    apiKey: "sk-xxxxxxxxxxxxxx",
  // proxy+/v1
    apiBaseUrl:"https://openai.1rmb.tk/v1"


  })

  const res = await api.sendMessage('Hello World!')
  console.log(res.text)
}

example()

```

</details>



<details>

<summary>查询余额</summary>

```javascript
    const headers = {
      'content-type': 'application/json',
      'Authorization': `Bearer sk-xxxxxxxxxxxxxxxxx`
    }
    // 查是否订阅
    const subscription = await fetch("https://openai.1rmb.tk/v1/dashboard/billing/subscription", {
      method: 'get',
      headers: headers
    })
    if (!subscription.ok) {
      const data = await subscription.json()
      // console.log(data);
      return data
      // throw new Error('API request failed')
    } else {
      const subscriptionData = await subscription.json()
      const endDate = subscriptionData.access_until
      const startDate = new Date(endDate - 90 * 24 * 60 * 60);
      console.log(formatDate(endDate, "YYYY-MM-DD"));
      console.log(formatDate(startDate, "YYYY-MM-DD"));
      const response = await fetch(`https://openai.1rmb.tk/v1/dashboard/billing/usage?start_date=${formatDate(startDate, "YYYY-MM-DD")}&end_date=${formatDate(endDate, "YYYY-MM-DD")}`, {
        method: 'get',
        headers: headers
      })
      
      const usageData = await response.json();
      // 账号类型
      const plan = subscriptionData.plan.id
      console.log(usageData);
      }

```

</details>

## Star History

[![Star History Chart](https://api.star-history.com/svg?repos=x-dr/chatgptProxyAPI&type=Date)](https://star-history.com/#x-dr/chatgptProxyAPI&Date)



[![Powered by DartNode](https://dartnode.com/branding/DN-Open-Source-sm.png)](https://dartnode.com "Powered by DartNode - Free VPS for Open Source")

