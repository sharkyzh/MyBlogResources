---
title: WebApi接口参数传参详解
date: 2018-01-12 17:34:42
category:
    - .net
    - webapi
tags:
    - .net
    - webapi
---

#### 一、Get请求

##### 基础类型参数


```
[HttpGet]
public string GetAllChargingData(int id, string name)
{
    return "ChargingData" + id;
}

---

$.ajax({
    type: "get",
    url: "http://localhost:27221/api/Charging/GetAllChargingData",
    data: { id: 1, name: "Jim", bir: "1988-09-11"},
    success: function (data, status) {
        if (status == "success") {
            $("#div_test").html(data);
        }
    }
});
```

##### 实体作为参数
Get请求的时候可以在参数里面加上[FromUri]即可直接得到对象

 <!-- more -->
```
var postdata = { ID: "1", NAME: "Jim", CREATETIME: "1988-09-11" };
$.ajax({
    type: "get",
    url: "http://localhost:27221/api/Charging/GetAllChargingData",
    data: postdata,
    success: function (data, status) { }
});

---

[HttpGet]
public string GetAllChargingData([FromUri]TB_CHARGING obj)
{
    return "ChargingData" + obj.ID;
}
```

如果不想使用[FromUri]这些在参数里面加特性的这种“怪异”写法，也可以采用先序列化，再在后台反序列的方式。


```
$.ajax({
    type: "get",
    url: "http://localhost:27221/api/Charging/GetByModel",
    contentType: "application/json",
    data: { strQuery: JSON.stringify({ ID: "1", NAME: "Jim", CREATETIME: "1988-09-11" }) },
    success: function (data, status) {
        if (status == "success") {
            $("#div_test").html(data);
        }
    }
});

---

[HttpGet]
public string GetByModel(string strQuery)
{
    TB_CHARGING oData = Newtonsoft.Json.JsonConvert.DeserializeObject<TB_CHARGING>(strQuery);
    return "ChargingData" + oData.ID;
}
```
##### 数组作为参数
一般get请求不建议将数组作为参数，因为我们知道get请求传递参数的大小是有限制的，最大1024字节，数组里面内容较多时，将其作为参数传递可能会发生参数超限丢失的情况。

##### “怪异”的get请求(其实是RESETful风格API方法名)

方法名以Get开头，WebApi会自动默认这个请求就是get请求，而如果你以其他名称开头而又不标注方法的请求方式，那么这个时候服务器虽然找到了这个方法，但是由于请求方式不确定，所以直接返回给你405——方法不被允许的错误。

最后结论：所有的WebApi方法最好是加上请求的方式（[HttpGet]/[HttpPost]/[HttpPut]/[HttpDelete]），不要偷懒，这样既能防止类似的错误，也有利于方法的维护，别人一看就知道这个方法是什么请求。


---

#### 二、Post请求

##### 基础类型参数

###### 传递多个基础类型的数据
 post请求的基础类型的参数和get请求有点不一样，我们知道get请求的参数是通过url来传递的，而post请求则是通过http的请求体中传过来的，WebApi的post请求也需要从http的请求体里面去取参数。
 

```
$.ajax({
    type: "post",
    url: "http://localhost:27221/api/Charging/SaveData",
    data: { "": "Jim" },
    success: function (data, status) {}
});

---

[HttpPost]
public bool SaveData([FromBody]string NAME)
{
    return true;
}
```
FromBody和我们一般通过url取参数的机制则不同，它的机制是=value，没有key的概念，并且如果你写了key(比如你的ajax参数写的{NAME:"Jim"})，后台反而得到的NAME等于null。不信你可以试试。

###### 传递多个基础类型的数据

```
$.ajax({
    type: "post",
    url: "http://localhost:27221/api/Charging/SaveData",
    contentType: 'application/json',
    data: JSON.stringify({ NAME: "Jim",DES:"备注" }),
    success: function (data, status) {}
});

---

[HttpPost]
public object SaveData(dynamic obj)
{
    var strName = Convert.ToString(obj.NAME);
    return strName;
}
```
通过dynamic动态类型能顺利得到多个参数，省掉了[FromBody]这个累赘，并且ajax参数的传递不用使用"无厘头"的{"":"value"}这种写法，有没有一种小清新的感觉~~有一点需要注意的是这里在ajax的请求里面需要加上参数类型为Json，即 **contentType: 'application/json'**, 这个属性。

##### 实体作为参数

###### 单个实体作为参数


```
$.ajax({
    type: "post",
    url: "http://localhost:27221/api/Charging/SaveData",
    data: { ID: "1", NAME: "Jim", CREATETIME: "1988-09-11" },
    success: function (data, status) {}
});
[HttpPost]
public bool SaveData(TB_CHARGING oData)
{
    return true;
}
```
原理解释：使用实体作为参数的时候，前端直接传递普通json，后台直接使用对应的类型去接收即可，不用FromBody。但是这里需要注意的一点就是，这里不能指定contentType为appplication/json，否则，参数无法传递到后台。

post请求默认是将表单里面的数据的key/value形式发送到服务，而我们的服务器只需要有对应的key/value属性值的对象就可以接收到。而如果使用application/json，则表示将前端的数据以序列化过的json传递到后端，后端要把它变成实体对象，还需要一个反序列化的过程。


```
var postdata = { ID: "1", NAME: "Jim", CREATETIME: "1988-09-11" };
$.ajax({
    type: "post",
    url: "http://localhost:27221/api/Charging/SaveData",
    contentType: 'application/json',
    data: JSON.stringify(postdata),
    success: function (data, status) {}
});

---

[HttpPost]
public bool SaveData(TB_CHARGING lstCharging)
{
    return true;
}
```

**如果指定了contentType为application/json，则必须要传递序列化过的对象；如果使用post请求的默认参数类型，则前端直接传递json类型的对象即可。**

###### 实体和基础类型一起作为参数传递


```
var postdata = { ID: "1", NAME: "Jim", CREATETIME: "1988-09-11" };
$.ajax({
    type: "post",
    url: "http://localhost:27221/api/Charging/SaveData",
    contentType: 'application/json',
    data: JSON.stringify({ NAME:"Lilei", Charging:postdata }),
    success: function (data, status) {}
});

---

[HttpPost]
public object SaveData(dynamic obj)
{
    var strName = Convert.ToString(obj.NAME);
    var oCharging = Newtonsoft.Json.JsonConvert.DeserializeObject<TB_CHARGING>(Convert.ToString(obj.Charging));
    return strName;
}
```
###### 数组作为参数

**（1）基础类型数组**

```
var arr = ["1", "2", "3", "4"];
$.ajax({
    type: "post",
    url: "http://localhost:27221/api/Charging/SaveData",
    contentType: 'application/json',
    data: JSON.stringify(arr),
    success: function (data, status) { }
});

--

[HttpPost]
public bool SaveData(string[] ids)
{
    return true;
}
```
**（2）实体集合**


```
var arr = [
    { ID: "1", NAME: "Jim", CREATETIME: "1988-09-11" },
    { ID: "2", NAME: "Lilei", CREATETIME: "1990-12-11" },
    { ID: "3", NAME: "Lucy", CREATETIME: "1986-01-10" }
];
$.ajax({
    type: "post",
    url: "http://localhost:27221/api/Charging/SaveData",
    contentType: 'application/json',
    data: JSON.stringify(arr),
    success: function (data, status) {}
});

---

[HttpPost]
public bool SaveData(List<TB_CHARGING> lstCharging)
{
    return true;
}
```

###### 后台发送请求参数的传递

如果我们通过后台去发送请求是否也是可行的呢？我们以实体对象作为参数来传递写写代码试一把。


```
public void TestReques()
{
　　　　 //请求路径
    string url = "http://localhost:27221/api/Charging/SaveData";

    //定义request并设置request的路径
    WebRequest request = WebRequest.Create(url);
    request.Method = "post";

    //初始化request参数
    string postData = "{ ID: \"1\", NAME: \"Jim\", CREATETIME: \"1988-09-11\" }";

    //设置参数的编码格式，解决中文乱码
    byte[] byteArray = Encoding.UTF8.GetBytes(postData);

    //设置request的MIME类型及内容长度
    request.ContentType = "application/json";
    request.ContentLength = byteArray.Length;

    //打开request字符流
    Stream dataStream = request.GetRequestStream();
    dataStream.Write(byteArray, 0, byteArray.Length);
    dataStream.Close();

    //定义response为前面的request响应
    WebResponse response = request.GetResponse();

    //获取相应的状态代码
    Console.WriteLine(((HttpWebResponse)response).StatusDescription);

    //定义response字符流
    dataStream = response.GetResponseStream();
    StreamReader reader = new StreamReader(dataStream);
    string responseFromServer = reader.ReadToEnd();//读取所有
    Console.WriteLine(responseFromServer);
}
```
---
> 本文出处：博客园 懒得安分 [ http://www.cnblogs.com/landeanfen/](http://www.cnblogs.com/landeanfen/)

本博客只对原文进行了摘要与抄录，若有侵权，请告知！