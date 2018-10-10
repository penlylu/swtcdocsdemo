# 井通公链API使用教程

## 一、非代码调用API接口
- 第一步：安装PostmanAPI调试工具或者chrome浏览器的restlet插件或者其他的一些调试工具。

- 第二步：使用调试工具对API接口进行调用，接口调用分为get请求和post请求（请求地址、请求参数、请求返回结果参照网站“API说明v2”）。

以chrome浏览器的restlet为例：

- 1、GET请求
  - Method: “GET”
  - SCHEME: “请求地址”
 ![avatar](https://github.com/penlylu/swtcdocsdemo/blob/master/docs/tutorial/pictures/API_GET.png)
 
- 2、POST请求
  - Method: “POST”
  - SCHEME: “请求地址”
  - HEADERS: “CONTENT-TYPE：APPLICATION/JSON”
  - BODY: “请求的JSON字符串”
  ![avatar](https://github.com/penlylu/swtcdocsdemo/blob/master/docs/tutorial/pictures/API_POST.png)

## 二、代码调用API接口
- 第一步：选用代码语言，如JAVA、PHP、C、GO等后端开发语言。

- 第二步：使用开发语言对API进行调用，接口调用分为get请求和post请求（请求地址、请求参数、请求返回结果参照网站“API说明v2”。

### 以JAVA语言为例：

- 1、GET请求
```
  package com.jingtum.api.test;
  import java.io.BufferedReader;
  import java.io.IOException;
  import java.io.InputStream;
  import java.io.InputStreamReader;
  import java.net.HttpURLConnection;
  import java.net.URL;
  public class TestApi {
    public static void main(String[] args) {
      //创建账号
      String  url = "https://tapi.jingtum.com/v2/wallet/new";
      String result = get(url);
      System.out.println("支付请求结果："+result);
    }
    public static String get(String location) {
      HttpURLConnection conn = null;
      InputStream in = null;
      BufferedReader br = null;
      try {
        URL url = new URL(location);
        conn = (HttpURLConnection) url.openConnection();
        conn.setRequestMethod("GET");
        conn.setRequestProperty("contentType", "UTF-8");
        in = conn.getInputStream();
        br = new BufferedReader(new InputStreamReader(in, "UTF-8"));
        String lines;
        StringBuffer sb = new StringBuffer();
        while ((lines = br.readLine()) != null)
          sb.append(lines);
        return sb.toString();
      } catch (Exception e) {
        throw new RuntimeException(e);
      } finally {
        if (conn != null)
          conn.disconnect();
        if (in != null)
          try {
            in.close();
          } catch (IOException e) {
            e.printStackTrace();
          }
        if (br != null)
          try {
            br.close();
          } catch (IOException e) {
            e.printStackTrace();
          }
      }
    }
  }
  ```
- 2、POST请求

**注意**：POST请求时，头部为`application/json; charset=UTF-8`；

```
package com.jingtum.api.test;
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStream;
import java.io.InputStreamReader;
import java.io.OutputStreamWriter;
import java.io.PrintWriter;
import java.net.HttpURLConnection;
import java.net.URL;
import java.util.UUID;
import com.alibaba.fastjson.JSONObject;
import com.jingtum.api.util.HttpClient;
import com.jingtum.api.util.HttpClient.ContentType;

public class TestApi {
	
	public static void main(String[] args) {
		//创建账号
		String  url = "https://tapi.jingtum.com/v2/accounts/jNn89aY84G23onFXupUd7bkMode6aKYMt8/payments";
		JSONObject payment_item = new JSONObject();
		payment_item.put("secret", "spvFsSWaD1BmNk7h3Zvo98YRi1NxX");
		payment_item.put("client_id", UUID.randomUUID().toString());
			JSONObject payment = new JSONObject();
			payment.put("source", "jNn89aY84G23onFXupUd7bkMode6aKYMt8");
			payment.put("destination", "j9U7YWAHF7ksFLZn2keD5e6ckoKf4nxdZY");
				JSONObject destination_amount = new JSONObject();
				destination_amount.put("value", "25");
				destination_amount.put("currency", "SWT");
				destination_amount.put("issuer", "");
			payment.put("amount", destination_amount);
			String[] memos = {"String","账户激活"};
			payment.put("memos", memos);
		payment_item.put("payment", payment);
		System.out.println("激活请求参数："+payment_item.toJSONString());
		String result = post(url, payment_item.toJSONString());
		System.out.println("激活请求结果："+result);
	}
	
	public static String post(String location, String data) {
		HttpURLConnection conn = null;
		PrintWriter out = null;
		BufferedReader br = null;
		try {
			URL url = new URL(location);
			conn = (HttpURLConnection) url.openConnection();
			conn.setDoOutput(true);
			conn.setDoInput(true);
			conn.setRequestMethod("POST");
			conn.setUseCaches(false);
			conn.setInstanceFollowRedirects(true);
			conn.setRequestProperty("Content-Type", "application/json; charset=UTF-8");
			conn.connect();
			out = new PrintWriter(new OutputStreamWriter(conn.getOutputStream(), "UTF-8"));
			out.write(data);
			out.flush();
			br = new BufferedReader(new InputStreamReader(conn.getInputStream(), "UTF-8"));
			String lines;
			StringBuffer sb = new StringBuffer();
			while ((lines = br.readLine()) != null)
				sb.append(lines);
			return sb.toString();
		} catch (Exception e) {
			throw new RuntimeException(e);
		} finally {
			if (conn != null)
				conn.disconnect();
			if (br != null)
				try {
					br.close();
				} catch (IOException e) {
					e.printStackTrace();
				}
		}
	}
}
```

### 以Python语言为例：
```
import urllib.request
import urllib.parse
import json
import time
def http_get():
    url='https://tapi.jingtum.com/v2/wallet/new'
    response = urllib.request.urlopen(url)
    print(response.read().decode('utf-8'))
def http_post():	
	serverurl='https://tapi.jingtum.com/v2/accounts/jNn89aY84G23onFXupUd7bkMode6aKYMt8/payments'
	client_id = "id"+(str(time.time()))
	values ={
		"client_id" : client_id,
		"payment" : {
			"amount" : {
				"currency" : "SWT",
				"issuer" : "",
				"value" : "1"
			},
			"destination" : "j3UcBBbes7HFgmTLmGkEQQShM2jdHbdGAe",
			"memos" : [ "" ],
			"source" : "jNn89aY84G23onFXupUd7bkMode6aKYMt8"
		},
		"secret" : "spvFsSWaD1BmNk7h3Zvo98YRi1NxX"
	}
	jdata = json.dumps(values).encode()
	request = urllib.request.Request(serverurl, jdata)
	request.add_header('Content-Type','application/json')
	request.get_method = lambda:'POST'
	response = urllib.request.urlopen(request)
	print(response.read().decode('utf-8'))
	
http_get()  #get请求demo
#http_post() #post请求demo
```
