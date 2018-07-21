<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Table of Contents**  *generated with [DocToc](https://github.com/thlorenz/doctoc)*

- [【Android 库 Gson】](#android-%E5%BA%93-gson)
- [引用：](#%E5%BC%95%E7%94%A8)
- [前言](#%E5%89%8D%E8%A8%80)
  - [基本概念](#%E5%9F%BA%E6%9C%AC%E6%A6%82%E5%BF%B5)
- [使用](#%E4%BD%BF%E7%94%A8)
  - [引入](#%E5%BC%95%E5%85%A5)
  - [Gson的两个基础方法](#gson%E7%9A%84%E4%B8%A4%E4%B8%AA%E5%9F%BA%E7%A1%80%E6%96%B9%E6%B3%95)
  - [Gson的创建](#gson%E7%9A%84%E5%88%9B%E5%BB%BA)
    - [方式一：](#%E6%96%B9%E5%BC%8F%E4%B8%80)
    - [方式二：](#%E6%96%B9%E5%BC%8F%E4%BA%8C)
      - [GsonBuilder的一些配置](#gsonbuilder%E7%9A%84%E4%B8%80%E4%BA%9B%E9%85%8D%E7%BD%AE)
  - [Gson的基本用法:](#gson%E7%9A%84%E5%9F%BA%E6%9C%AC%E7%94%A8%E6%B3%95)
    - [Json字符串转JavaBean](#json%E5%AD%97%E7%AC%A6%E4%B8%B2%E8%BD%ACjavabean)
    - [Json字符串转List](#json%E5%AD%97%E7%AC%A6%E4%B8%B2%E8%BD%AClist)
    - [JavaBean转换Json字符串](#javabean%E8%BD%AC%E6%8D%A2json%E5%AD%97%E7%AC%A6%E4%B8%B2)
    - [List Map转Json字符串](#list-map%E8%BD%ACjson%E5%AD%97%E7%AC%A6%E4%B8%B2)
  - [Gson封装的类型体系](#gson%E5%B0%81%E8%A3%85%E7%9A%84%E7%B1%BB%E5%9E%8B%E4%BD%93%E7%B3%BB)
    - [JsonObject](#jsonobject)
      - [addPropert(key,value)](#addpropertkeyvalue)
    - [JsonArray](#jsonarray)
      - [JsonObject 嵌套数组或者说嵌套JsonArray](#jsonobject-%E5%B5%8C%E5%A5%97%E6%95%B0%E7%BB%84%E6%88%96%E8%80%85%E8%AF%B4%E5%B5%8C%E5%A5%97jsonarray)
    - [JsonNull](#jsonnull)
    - [JsonPrimitive](#jsonprimitive)
  - [Gson注解](#gson%E6%B3%A8%E8%A7%A3)
    - [@SerializedName 重命名](#serializedname-%E9%87%8D%E5%91%BD%E5%90%8D)
      - [作用1：转换关键字key](#%E4%BD%9C%E7%94%A81%E8%BD%AC%E6%8D%A2%E5%85%B3%E9%94%AE%E5%AD%97key)
      - [作用2:结合alternate 提供多种备用字段key来解析](#%E4%BD%9C%E7%94%A82%E7%BB%93%E5%90%88alternate-%E6%8F%90%E4%BE%9B%E5%A4%9A%E7%A7%8D%E5%A4%87%E7%94%A8%E5%AD%97%E6%AE%B5key%E6%9D%A5%E8%A7%A3%E6%9E%90)
    - [@Expose 过滤](#expose-%E8%BF%87%E6%BB%A4)
      - [情况1](#%E6%83%85%E5%86%B51)
      - [情况2](#%E6%83%85%E5%86%B52)
      - [情况3](#%E6%83%85%E5%86%B53)
      - [情况4](#%E6%83%85%E5%86%B54)
    - [@Since(float v) 版本控制](#sincefloat-v-%E7%89%88%E6%9C%AC%E6%8E%A7%E5%88%B6)
    - [@Util(float v) 版本控制](#utilfloat-v-%E7%89%88%E6%9C%AC%E6%8E%A7%E5%88%B6)
  - [Gson 高级用法](#gson-%E9%AB%98%E7%BA%A7%E7%94%A8%E6%B3%95)
    - [TypeAdapter](#typeadapter)
      - [实例：使用TypeAdapter 来序列化和反序列化](#%E5%AE%9E%E4%BE%8B%E4%BD%BF%E7%94%A8typeadapter-%E6%9D%A5%E5%BA%8F%E5%88%97%E5%8C%96%E5%92%8C%E5%8F%8D%E5%BA%8F%E5%88%97%E5%8C%96)
    - [Gson的容错机制](#gson%E7%9A%84%E5%AE%B9%E9%94%99%E6%9C%BA%E5%88%B6)
      - [1. new GsonBuilder().setLenient()](#1-new-gsonbuildersetlenient)
      - [2. 使用JsonReader](#2-%E4%BD%BF%E7%94%A8jsonreader)
      - [3. 自定义TypeAdapter](#3-%E8%87%AA%E5%AE%9A%E4%B9%89typeadapter)
      - [4. 使用注解JsonAdapter](#4-%E4%BD%BF%E7%94%A8%E6%B3%A8%E8%A7%A3jsonadapter)
      - [实例](#%E5%AE%9E%E4%BE%8B)
      - [实例：基于注解的方式](#%E5%AE%9E%E4%BE%8B%E5%9F%BA%E4%BA%8E%E6%B3%A8%E8%A7%A3%E7%9A%84%E6%96%B9%E5%BC%8F)
      - [5. JsonDeserializer](#5-jsondeserializer)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

# 【Android 库 Gson】
# 引用：
★[Gson 解析教程](https://blog.csdn.net/axuanqq/article/details/51441590)
★★★ [Gson的入门使用](https://www.cnblogs.com/majay/p/6336918.html)
[Gson全解析（上）-Gson基础](https://www.jianshu.com/p/fc5c9cdf3aab) ==RTRT==待读
[你真的会用Gson吗?Gson使用指南（一）](https://www.jianshu.com/p/e740196225a4)==RTRT==待读

# 前言
[Gson](https://github.com/google/gson) （又称Google Gson）是Google公司发布的一个开放源代码的Java库，主要用途为序列化Java对象为JSON字符串，或反序列化JSON字符串成Java对象。而JSON(JavaScript Object Notation) 是一种轻量级的数据交换格式，易于人阅读和编写，同时也易于机器解析和生成，广泛应用于各种数据的交互中，尤其是服务器与客户端的交互。

## 基本概念

- **Serialization**:序列化，使Java对象到Json字符串的过程。
- **Deserialization**：反序列化，字符串转换成Java对象。
- JSON数据中的**`JsonElement`**有下面这四种类型：
  **JsonPrimitive** —— 例如一个字符串或整型
  **JsonObject**—— 一个以 JsonElement 名字（类型为 String）作为索引的集合。也就是说可以把 JsonObject 看作值为 JsonElement 的键值对集合。
  **JsonArray**—— JsonElement 的集合。注意数组的元素可以是四种类型中的任意一种，或者混合类型都支持。
  **JsonNull**—— 值为null


# 使用

## 引入

app的build.gradle中引入：

```
dependencies {
    implementation 'com.google.code.gson:gson:2.8.5'
}
```

## Gson的两个基础方法

```java
public String toJson(Object src)
public <T> T fromJson(String json, Class<T> classOfT)
```

## Gson的创建

### 方式一：

**直接new Gson**对象：

```java
// 使用new方法
Gson gson = new Gson();

// toJson 将bean对象转换为json字符串
String jsonStr = gson.toJson(user, User.class);

// fromJson 将json字符串转为bean对象
Student user= gson.fromJson(jsonStr, User.class);

// **序列化List**
String jsonStr2 = gson.toJson(list);

// **反序列化成List时需要使用到TypeToken getType()**
List<User> retList = gson.fromJson(jsonStr2,new TypeToken<List<User>>(){}.getType());
```

### 方式二：

使用**GsonBuilder**：

使用**new Gson**()，此时会创建一个带有**默认配置**选项的Gson实例，如果不想使用默认配置，那么就可以使用GsonBuilder。

```java
//serializeNulls()是GsonBuilder提供的一种配置，当字段值为空或null时，依然对该字段进行转换
Gson gson = new GsonBuilder().serializeNulls().create(); 
```

使用GsonBuilder创建Gson实例的步骤：

- 首先创建GsonBuilder,然后调用GsonBuilder提供的各种配置方法进行配置，
- 最后调用GsonBuilder的create方法，将基于当前的配置创建一个Gson实例。



#### GsonBuilder的一些配置

```java
 Gson gson = new GsonBuilder()
         .excludeFieldsWithoutExposeAnnotation() //不对没有用@Expose注解的属性进行操作
         .enableComplexMapKeySerialization() //当Map的key为复杂对象时,需要开启该方法
         .serializeNulls() //当字段值为空或null时，依然对该字段进行转换
         .setDateFormat("yyyy-MM-dd HH:mm:ss:SSS") //时间转化为特定格式
         .setPrettyPrinting() //对结果进行格式化，增加换行
         .disableHtmlEscaping() //防止特殊字符出现乱码
         .registerTypeAdapter(User.class,new UserAdapter()) //为某特定对象设置固定的序列或反序列方式，自定义Adapter需实现JsonSerializer或者JsonDeserializer接口
         .create();
```

例如：Gosn对复杂Map的处理时需要用到其中的 enableComplexMapKeySerialization() 配置：

```
Gson gson = new GsonBuilder().enableComplexMapKeySerialization().create(); //开启复杂处理Map方法
Map<List<User>, String> map = new HashMap<List<User>, String>();
// TODO 向map中添加数据

String jsonStr = gson.toJson(map);  //toJson

Map<List<User>, String> resultMap = gson.fromJson(jsonStr, new TypeToken<Map<List<User>, String>>() {}.getType()); //fromJson
```

注意：如果Map的key为String，则可以不使用GsonBuilder的enableComplexMapKeySerialization()方法，或者直接new Gson();


## Gson的基本用法: 

### Json字符串转JavaBean

```java
String studentJsonStr="{\"name\":\"xuanyouwu\",\"age\":26}";
Student student = gson.fromJson(studentJsonStr, Student.class);
```

运行结果:
```xml
------->json convert JavaBean:Student{name='xuanyouwu', age=26}
```

### Json字符串转List
反序列化成List时需要使用到**`TypeToken`** getType()
```java
String listJsonStr="[\"1\",\"a\",\"3\",\"rt\",\"5\"]";

Type type = new TypeToken<ArrayList<String>>() {}.getType();
ArrayList<String> sList=gson.fromJson(listJsonStr, type);
```

运行结果: 
```xml
------->json convert List:[1, a, 3, rt, 5]
```

### JavaBean转换Json字符串

 gson提供 public String **toJson**(Object src) 方法可以将对象转换成jsonStr
```java
public class GsonTest1 {
    public static void main(String[] args) throws Exception {
        Student student = new Student();
        student.setName("xuanyouwu");
        student.setAge(26);
        
        Gson gson = new Gson();
        String jsonStr = gson.toJson(student);
        
        System.out.println("---->javabean convert jsonStr:" + jsonStr);
    }
    public static class Student {
        private String name;
        private int age;
        //省略setter getter equals
    }
}
```

运行结果:
```xml
---->javabean convert jsonStr:{"name":"xuanyouwu","age":26}
```

### List Map转Json字符串

```java
Gson gson = new Gson();

List<String> list = Arrays.asList("1", "a", "3", "rt", "5");
String toJson = gson.toJson(list);        

Map<String, Object> content = new HashMap<String, Object>();
content.put("name", "xuanyouwu");
content.put("age", "26");
String toJson1 = gson.toJson(content);
```

运行结果:
```xml
---->list convert jsonStr:["1","a","3","rt","5"]
---->map convert jsonStr:{"name":"xuanyouwu","age":"26"}
```



## Gson封装的类型体系

Gson里面有一个非常有意思的抽象基类JsonElement,他的继承体系:
![](https://upload-images.jianshu.io/upload_images/9028834-b0ad6ebdcb4cf9d2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### JsonObject
JsonObject等同于org的JSONObject,JsonArray也类似, 

#### addPropert(key,value)
通过`addPropert(key,value)`可以向jsonObject中添加字段 跟hashMap类似

```java
JsonObject jsonObject = new JsonObject();
jsonObject.addProperty("name", "xuanyouwu");
jsonObject.addProperty("age", 26);
System.out.println("------>create jsonObject:" + jsonObject);
```

运行结果:
```xml
------>create jsonObject:{"name":"xuanyouwu","age":26}
```



### JsonArray

```java
JsonArray jsonElements = new JsonArray();
jsonElements.add("a");
jsonElements.add("b");
jsonElements.add("c");
jsonElements.add("d");
System.out.println("------>create jsonArray:" + jsonElements);
```

运行结果:

```xml
------>create jsonArray:["a","b","c","d"]
```

#### JsonObject 嵌套数组或者说嵌套JsonArray
通过JsonObject的add(key,JsonElement)可以为jsonObject 添加一个数组的字段

```java
JsonObject jsonObject2 = new JsonObject();
jsonObject2.addProperty("name", "xuanyouwu");
jsonObject2.addProperty("age", 26);

JsonArray jsonElements2 = new JsonArray();
jsonElements2.add("骑车");
jsonElements2.add("打游戏");
jsonElements2.add("看电视");
jsonObject2.add("hobby", jsonElements2);

System.out.println("------>create jsonObject inner JsonArray:" + jsonObject2);
```
运行结果:
```
------>create jsonObject inner JsonArray:{"name":"xuanyouwu","age":26,"hobby":["骑车","打游戏","看电视"]}
```


### JsonNull
JsonNull 其实就是null 字段
```java
JsonNull jsonNull=new JsonNull();//构造方法过时，推荐INSTANCE
```
```java
JsonNull jsonNull=JsonNull.INSTANCE;
System.out.println("-----"+jsonNull);
```

运行结果: 
```xml
-----null
```

### JsonPrimitive
JsonPrimitive非常有意思,我们知道如果json转换成字符串可能包含引号的转义，但是通过JsonPrimative我们可以获得为转义的字符串，看实例：     

```java
String studentJsonStr = "{\"name\":\"xuanyouwu\",\"age\":26}";
System.out.println("------>studentJsonStr:" + studentJsonStr);


JsonPrimitive jsonPrimitive = new JsonPrimitive(studentJsonStr);
System.out.println("------>jsonPrimitive:" + jsonPrimitive);
System.out.println("------>jsonPrimitive:" + jsonPrimitive.toString());
System.out.println("------>jsonPrimitive:" + jsonPrimitive.getAsString());

JsonPrimitive jsonPrimitive2 = new JsonPrimitive("this is String");
System.out.println("------>jsonPrimitive2:" + jsonPrimitive2);
System.out.println("------>jsonPrimitive2:" + jsonPrimitive2.toString());
System.out.println("------>jsonPrimitive2:" + jsonPrimitive2.getAsString());
```

运行结果:

```xml
------>studentJsonStr:{"name":"xuanyouwu","age":26}
------>jsonPrimitive:"{\"name\":\"xuanyouwu\",\"age\":26}"
------>jsonPrimitive:"{\"name\":\"xuanyouwu\",\"age\":26}"
------>jsonPrimitive:{"name":"xuanyouwu","age":26}
------>jsonPrimitive2:"this is String"
------>jsonPrimitive2:"this is String"
------>jsonPrimitive2:this is String
```
![](https://upload-images.jianshu.io/upload_images/9028834-8566106d31e07ae2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



## Gson注解
在Gson中有五类注解
![](https://upload-images.jianshu.io/upload_images/9028834-932e35ccbdb89466.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### @SerializedName 重命名

#### 作用1：转换关键字key
场景1：json转换成JavaBean时，json字段的key 默认必须和我们声明类的字段名称一样，当服务器端返回了关键字怎么办，比如key 为new switch这样，我们在声明类的时候不能写这样的字段。这时候重命名注解都排上用场了。
场景2：服务器端返回的json 的key 简直太丑，或者太长,你想简化，my_parent_name,可以简化成mpn 比较优雅简介

实例:
```java
public class MainActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
    }

    public static class User {
        public String name;
        public int age;

        @SerializedName("new")
        public int isnew;

        @Override
        public String toString() {
            return "User{" +
                    "name='" + name + '\'' +
                    ", age=" + age +
                    ", isnew=" + isnew +
                    '}';
        }
    }

    public static void main(String[] args) throws Exception {
        String jsonFromServer = "{\n" +
                "    \"age\": 26,\n" +
                "    \"name\": \"zhangsan\",\n" +
                "    \"new\": 1\n" +
                "}";


        Gson gson = new Gson();
        User user = gson.fromJson(jsonFromServer, User.class);
        System.out.println("------>user:" + user);

    }
}
```

运行结果: 
```
------>user:User{name='zhangsan', age=26, isnew=1}
```

```java
public class GsonTest {

    public static class User2 {
        public String name;
        public int age;

        @SerializedName("my_parent_name")
        public String pn;

        @Override
        public String toString() {
            return "User2{" +
                    "name='" + name + '\'' +
                    ", age=" + age +
                    ", pn='" + pn + '\'' +
                    '}';
        }
    }


    public static void main(String[] args) throws Exception {
        String jsonFromServer2 = "{\n" +
                "    \"age\": 26,\n" +
                "    \"my_parent_name\": \"zhangsanf\",\n" +
                "    \"name\": \"zhangsan\"\n" +
                "}";


        Gson gson2 = new Gson();
        User2 user2 = gson2.fromJson(jsonFromServer2, User2.class);
        System.out.println("------>user2:" + user2);
    }
}
```

 

运行结果: 

```
------>user2:User2{name='zhangsan', age=26, pn='zhangsanf'}
```

#### 作用2:结合alternate 提供多种备用字段key来解析
@SerializedName(value =**"desc"**,alternate = {**"other"**,**"note"**})  如果json中有other就会解析成desc 如果有note也会解析成desc。
注意1：value中的值不能出现在alternate中；
注意2：alternate的备选字段，会后面的替换前面的。

实例：
```java
public class GsonTest {
    public static class User {
        public String name;
        public int age;
        @SerializedName(value = "desc", alternate = {"other", "note"})
        public String desc;

        @Override
        public String toString() {
            return "User{" +
                    "name='" + name + '\'' +
                    ", age=" + age +
                    ", desc='" + desc + '\'' +
                    '}';
        }
    }

    public static void main(String[] args) throws Exception {
        String jsonFromServer = "{\n" +
                "    \"age\": 26,\n" +
                "    \"other\": \"成都人\",\n" +
                "    \"name\": \"zhangsan\"\n" +
                "}";
        Gson gson = new Gson();
        User user = gson.fromJson(jsonFromServer, User.class);
        System.out.println("------>user:" + user);


        String jsonFromServer2 = "{\n" +
                "    \"age\": 26,\n" +
                "    \"note\": \"成都人\",\n" +
                "    \"name\": \"zhangsan\"\n" +
                "}";
        User user2 = gson.fromJson(jsonFromServer2, User.class);
        System.out.println("------>user:" + user2);

        //包括desc与note，note在后面
        String jsonFromServer3 = "{\n" +
                "    \"age\": 26,\n" +
                "    \"desc\": \"desc成都人\",\n" +
                "    \"name\": \"zhangsan\",\n" +
                "    \"note\": \"note成都人\"\n" +
                "}";
        User user3 = gson.fromJson(jsonFromServer3, User.class);
        System.out.println("------>user:" + user3);

        //包括desc与note，desc在后面
        String jsonFromServer4 = "{\n" +
                "    \"age\": 26,\n" +
                "    \"note\": \"note成都人\",\n" +
                "    \"name\": \"zhangsan\",\n" +
                "    \"desc\": \"desc成都人\"\n" +
                "}";
        User user4 = gson.fromJson(jsonFromServer4, User.class);
        System.out.println("------>user:" + user4);
    }
}
```

运行结果:
```
------>user:User{name='zhangsan', age=26, desc='成都人'}
------>user:User{name='zhangsan', age=26, desc='成都人'}
------>user:User{name='zhangsan', age=26, desc='note成都人'}
------>user:User{name='zhangsan', age=26, desc='desc成都人'}
```

### @Expose 过滤

可以**排除**不需要序列化的**字段**，默认既可以序列化又可以反序列化

```java
/* Expose 源码 */
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.FIELD)
public @interface Expose {  
  public boolean serialize() default true; 
  public boolean deserialize() default true;
}
```
需要**配合GsonBuilder**使用
```java
Gson gson = new GsonBuilder()
                .excludeFieldsWithoutExposeAnnotation()
                .create();
```
如上设置后，不添加@Expose注解的字段将不会解析:
分为以下几种情况:
1：**不添加**`@Expose`注解等同于`@Expose(deserialize = false,serialize = false)` **不**做任何**解析**
2：`@Expose(deserialize = true,serialize = false)` 只**可解析**用用，也就是**可**以**反序列化**，不可以序列化
3：`@Expose(deserialize = false,serialize = true)` 序列化可以，反序列化不行
4：`@Expose(deserialize = true,serialize = true)` 既可以序列化，也可以反序列化

#### 情况1
不添加@Expose注解，等同于@Expose(deserialize = false,serialize = false)
```java
public class GsonTest5 {
    public static class User {
        public String name;
        @Override
        public String toString() {
            return "User{" +
                    "name='" + name + '\'' +
                    '}';
        }
    }

    public static void main(String[] args) throws Exception {
        String jsonFromServer = "{\"name\": \"zhangsan\"}";
        
        Gson gson = new GsonBuilder()
                .excludeFieldsWithoutExposeAnnotation()
                .create();
        User user = gson.fromJson(jsonFromServer, User.class);
        System.out.println("------>反序列化:" + user);

        User user1 = new User();
        user1.name = "zhangsan2";
        String userStr = gson.toJson(user1);
        System.out.println("------>序列化:" + userStr);
    }
}
```

运行结果:
```
------>反序列化:User{name='null'}
------>序列化:{}
```
#### 情况2
添加@Expose注解，等同于 @Expose(deserialize = true,serialize = true)
```java
public class GsonTest5 {
    public static class User {
        @Expose //等同于 @Expose(deserialize = true,serialize = true)
        public String name;
 
        @Override
        public String toString() {
            return "User{" +
                    "name='" + name + '\'' +
                    '}';
        }
    }
 
    public static void main(String[] args) throws Exception {
        String jsonFromServer = "{\"name\": \"zhangsan\"}";
        Gson gson = new GsonBuilder()
                .excludeFieldsWithoutExposeAnnotation()
                .create();
        User user = gson.fromJson(jsonFromServer, User.class);
        System.out.println("------>反序列化:" + user);
 
        User user1 = new User();
        user1.name = "zhangsan2";
        String userStr = gson.toJson(user1);
        System.out.println("------>序列化:" + userStr);
    }
}
```

运行结果:
```
------>反序列化:User{name='zhangsan'}
------>序列化:{"name":"zhangsan2"}
```

#### 情况3
只可序列化：@Expose(deserialize = false,serialize = true)
```java
public class GsonTest5 {
    public static class User {
        @Expose(deserialize = false,serialize = true)
        public String name;
 
        @Override
        public String toString() {
            return "User{" +
                    "name='" + name + '\'' +
                    '}';
        }
    }
 
    public static void main(String[] args) throws Exception {
        String jsonFromServer = "{\"name\": \"zhangsan\"}";
        Gson gson = new GsonBuilder()
                .excludeFieldsWithoutExposeAnnotation()
                .create();
        User user = gson.fromJson(jsonFromServer, User.class);
        System.out.println("------>反序列化:" + user);
 
        User user1 = new User();
        user1.name = "zhangsan2";
        String userStr = gson.toJson(user1);
        System.out.println("------>序列化:" + userStr);
    }
}
```
运行结果:
```
------>反序列化:User{name='null'}
------>序列化:{"name":"zhangsan2"}
```

#### 情况4
只可反序列化 @Expose(deserialize = true, serialize = false)
```java
public class GsonTest5 { 
    public static class User {
        @Expose(deserialize = true, serialize = false)
        public String name;
 
        @Override
        public String toString() {
            return "User{" +
                    "name='" + name + '\'' +
                    '}';
        }
    }
 
    public static void main(String[] args) throws Exception {
        String jsonFromServer = "{\"name\": \"zhangsan\"}";
        Gson gson = new GsonBuilder()
                .excludeFieldsWithoutExposeAnnotation()
                .create();
        User user = gson.fromJson(jsonFromServer, User.class);
        System.out.println("------>反序列化:" + user);
 
        User user1 = new User();
        user1.name = "zhangsan2";
        String userStr = gson.toJson(user1);
        System.out.println("------>序列化:" + userStr);
    }
}
```
运行结果:
```
------>反序列化:User{name='zhangsan'}
------>序列化:{}
```

### @Since(float v) 版本控制
**结合GsonBuilder.setVersion(n)**使用，当n>=v时 才会序列化解析

```java
public class GsonTest6 {
    public static class User {
        @Since(2)
        public String name;
 
        @Override
        public String toString() {
            return "User{" +
                    "name='" + name + '\'' +
                    '}';
        }
    }
 
    public static void main(String[] args) throws Exception {
        String jsonFromServer = "{\"name\": \"zhangsan\"}";
        Gson gson = new GsonBuilder()
                .setVersion(1)//版本为1
                .create();
        User user1 = gson.fromJson(jsonFromServer, User.class);
        System.out.println("------>反序列化v=1:" + user1);
 
        User user1_1 = new User();
        user1_1.name = "zhangsan2";
        String userStr = gson.toJson(user1_1);
        System.out.println("------>序列化v=1:" + userStr);
 
 
        Gson gson2 = new GsonBuilder()
                .setVersion(2)//版本为2
                .create();
        User user2 = gson2.fromJson(jsonFromServer, User.class);
        System.out.println("------>反序列化v=2:" + user2);
 
        User user2_1 = new User();
        user2_1.name = "zhangsan2";
        String userStr2_1 = gson2.toJson(user2_1);
        System.out.println("------>序列化v=2:" + userStr2_1);
 
        Gson gson3 = new GsonBuilder()
                .setVersion(3)//版本为3
                .create();
        User user3 = gson3.fromJson(jsonFromServer, User.class);
        System.out.println("------>反序列化v=3:" + user3);
 
        User user3_1 = new User();
        user3_1.name = "zhangsan2";
        String userStr3_1 = gson3.toJson(user3_1);
        System.out.println("------>序列化v=3:" + userStr3_1);
    }
}
```
运行结果:
```
------>反序列化v=1:User{name='null'}
------>序列化v=1:{}
------>反序列化v=2:User{name='zhangsan'}
------>序列化v=2:{"name":"zhangsan2"}
------>反序列化v=3:User{name='zhangsan'}
------>序列化v=3:{"name":"zhangsan2"}
```

### @Util(float v) 版本控制
**结合GsonBuilder.setVersion(n)**使用，当n<时 才会序列化解析
```java
public class GsonTest6 { 
    public static class User {
        @Until(2)
        public String name;
 
        @Override
        public String toString() {
            return "User{" +
                    "name='" + name + '\'' +
                    '}';
        }
    }
 
    public static void main(String[] args) throws Exception {
        String jsonFromServer = "{\"name\": \"zhangsan\"}";
        Gson gson = new GsonBuilder()
                .setVersion(1)//版本为1
                .create();
        User user1 = gson.fromJson(jsonFromServer, User.class);
        System.out.println("------>反序列化v=1:" + user1);
 
        User user1_1 = new User();
        user1_1.name = "zhangsan2";
        String userStr = gson.toJson(user1_1);
        System.out.println("------>序列化v=1:" + userStr);
 
 
        Gson gson2 = new GsonBuilder()
                .setVersion(2)//版本为2
                .create();
        User user2 = gson2.fromJson(jsonFromServer, User.class);
        System.out.println("------>反序列化v=2:" + user2);
 
        User user2_1 = new User();
        user2_1.name = "zhangsan2";
        String userStr2_1 = gson2.toJson(user2_1);
        System.out.println("------>序列化v=2:" + userStr2_1);
 
        Gson gson3 = new GsonBuilder()
                .setVersion(3)//版本为3
                .create();
        User user3 = gson3.fromJson(jsonFromServer, User.class);
        System.out.println("------>反序列化v=3:" + user3);
 
        User user3_1 = new User();
        user3_1.name = "zhangsan2";
        String userStr3_1 = gson3.toJson(user3_1);
        System.out.println("------>序列化v=3:" + userStr3_1);
    }
}
```

运行结果:

```
------>反序列化v=1:User{name='zhangsan'}
------>序列化v=1:{"name":"zhangsan2"}
------>反序列化v=2:User{name='null'}
------>序列化v=2:{}
------>反序列化v=3:User{name='null'}
------>序列化v=3:{}
```

## Gson 高级用法

### TypeAdapter
据说使用TypeAdapter 效率更高。
在TypeAdapter源码中备注了一句 Converts Java objects to and from JSON 就是对象json之间的互相转换，接替了T 泛型类的序列化和反序列化的逻辑。

从源码中我们看到区分了2.1版本之前和之后的用法：

2.1版本之前可以自定义adapter：
```java
public class PointAdapter extends TypeAdapter<Point> {
    public Point read(JsonReader reader) throws IOException {
        if (reader.peek() == JsonToken.NULL) {
            reader.nextNull();
            return null;
        }
        String xy = reader.nextString();
        String[] parts = xy.split(",");
        int x = Integer.parseInt(parts[0]);
        int y = Integer.parseInt(parts[1]);
        return new Point(x, y);
    }

    public void write(JsonWriter writer, Point value) throws IOException {
        if (value == null) {
            writer.nullValue();
            return;
        }
        String xy = value.getX() + "," + value.getY();
        writer.value(xy);
    }
}
```

使用  
```java
GsonBuilder builder = new GsonBuilder();
builder.registerTypeAdapter(Point.class, new PointAdapter());
// if PointAdapter didn't check for nulls in its read/write methods, you should instead use
// builder.registerTypeAdapter(Point.class, new PointAdapter().nullSafe());
...
Gson gson = builder.create();
```

在2.1版本之后更推荐直接插入泛型就使用
```java
//   String json = "{'origin':'0,0','points':['1,2','3,4']}";
//   TypeAdapter<Graph> graphAdapter = gson.getAdapter(Graph.class);
//   Graph graph = graphAdapter.fromJson(json);
// }</pre>
// And an example for serialization: <pre>   {@code
//
//   Graph graph = new Graph(...);
//   TypeAdapter<Graph> graphAdapter = gson.getAdapter(Graph.class);
//   String json = graphAdapter.toJson(graph);
// }</pre>
```


#### 实例：使用TypeAdapter 来序列化和反序列化
```java
User user = new User();
user.name = "xuanyouwu";
user.age = 26;

Gson gson = new Gson();
TypeAdapter<User> userTypeAdapter = gson.getAdapter(User.class);

String userJsonStr = userTypeAdapter.toJson(user);
System.out.println("------>序列化:" + userJsonStr);

User user1 = userTypeAdapter.fromJson(userJsonStr);
System.out.println("------>反序列化:" + user1);
```
运行结果:
```
------>序列化:{"name":"xuanyouwu","age":26}
------>反序列化:User{name='xuanyouwu', age=26}
```


### Gson的容错机制
为什么要容错了?
比如在javaBean中声明了int类型的age，如果服务端返回的是"" 空字符串怎么办呢？
如果json格式不规范如 {name=zhangsan,age:26,hobby=null}  发现不是普通的key value。
#### 1. new GsonBuilder().setLenient()
创建Gson的方式
```java
  gson = new GsonBuilder()
                    .setLenient()// json宽松
                    .create();
```
#### 2. 使用JsonReader 
```java
   JsonReader jsonReader = gson.newJsonReader(value.charStream());
   jsonReader.setLenient(true)
```
#### 3. 自定义TypeAdapter
#### 4. 使用注解JsonAdapter
其实也是自定义Adapter

 1、2归为一类：
 由框架实现，基本json大格式规范,键值对不标准,多引号的问题等等,而不报错停止解析,但是功能相对较弱
 能解决如下bug： 
```
com.google.gson.JsonSyntaxException: com.google.gson.stream.MalformedJsonException: Use JsonReader.setLenient(true) to accept malformed JSON 
```
3、4归为一类：
都属于自定义adapter，但是3与gson绑定，4使用注解和字段绑定。
#### 实例

```java
public class GsonTest {

    public static class UserTypeAdapter extends TypeAdapter<User> {

        @Override
        public void write(JsonWriter out, User value) throws IOException {
            out.beginObject();
            out.name("name").value(value.name);
            out.name("age").value(value.age);
            out.endObject();
        }

        @Override
        public User read(JsonReader in) throws IOException {
            User user = new User();
            in.beginObject();
            while (in.hasNext()) {
                switch (in.nextName()) {
                    case "name":
                        user.name = in.nextString();
                        break;
                    case "age":
                        try {
                            String str = in.nextString();
                            user.age = Integer.valueOf(str);
                        } catch (Exception e) {
                        }
                        break;
                }
            }
            in.endObject();
            return user;
        }
    }

    public static void main(String[] args) throws Exception {
        String jsonStrFromServer = "{\n" +
                "    \"age\": \"\",\n" +
                "    \"name\": \"zhangsan\"\n" +
                "}";
        System.out.println("------->jsonFromServer:" + jsonStrFromServer);

        Gson gson = new Gson();
        try {
            User user = gson.fromJson(jsonStrFromServer, User.class);
            System.out.println("------>默认Gson 解析:" + user);
        } catch (JsonParseException e) {//java.lang.NumberFormatException: empty String
            System.out.println("------>默认Gson 解析 异常:" + e);
        }

        Gson gson2 = new GsonBuilder()
                .registerTypeAdapter(User.class, new UserTypeAdapter()).create();
        try {
            User user2 = gson2.fromJson(jsonStrFromServer, User.class);
            System.out.println("------>自定义adapter 解析:" + user2);
        } catch (JsonParseException e) {//java.lang.NumberFormatException: empty String
            System.out.println("------>自定义adapter 异常:" + e);
        }

        UserTypeAdapter userTypeAdapter = new UserTypeAdapter();
        try {
            User user3 = userTypeAdapter.fromJson(jsonStrFromServer);
            System.out.println("------>自定义adapter 解析2:" + user3);
        } catch (Exception e) {
            System.out.println("------>自定义adapter 异常2:" + e);
        }
    }

    public static class User {
        public String name;
        public int age;

        @Override
        public String toString() {
            return "User{" +
                    "name='" + name + '\'' +
                    ", age=" + age +
                    '}';
        }
    }
}
```
运行结果:

```
------->jsonFromServer:{
    "age": "",
    "name": "zhangsan"
}
------>默认Gson 解析 异常:com.google.gson.JsonSyntaxException: java.lang.NumberFormatException: empty String
------>自定义adapter 解析:User{name='zhangsan', age=0}
------>自定义adapter 解析2:User{name='zhangsan', age=0}
```

可以看到 age是空字符串 但是不影响整体的解析流程,这对客户端是十分友好的

#### 实例：基于注解的方式
上面的方式倾向于整体，注解的方式倾向于字段。


```java
public class GsonTest {

    public static class User {
        public String name;
        
        @JsonAdapter(IntegerTypeAdapter.class)
        public int age;

        @Override
        public String toString() {
            return "User{" +
                    "name='" + name + '\'' +
                    ", age=" + age +
                    '}';
        }
    }

    public static class IntegerTypeAdapter extends TypeAdapter<Integer> {

        @Override
        public void write(JsonWriter out, Integer value) throws IOException {
            out.value(value);
        }

        @Override
        public Integer read(JsonReader in) throws IOException {
            int i = 0;
            try {
                String str = in.nextString();
                i = Integer.valueOf(str);
            } catch (Exception e) {
            }
            return i;
        }
    }

    public static class User2 {
        public String name;
        public int age;

        @Override
        public String toString() {
            return "User{" +
                    "name='" + name + '\'' +
                    ", age=" + age +
                    '}';
        }
    }


    public static void main(String[] args) throws Exception {
        String jsonStrFromServer = "{\n" +
                "    \"age\": \"\",\n" +
                "    \"name\": \"zhangsan\"\n" +
                "}";
        System.out.println("------->jsonFromServer:" + jsonStrFromServer);

        Gson gson = new Gson();
        try {
            User2 user2 = gson.fromJson(jsonStrFromServer, User2.class);
            System.out.println("------>gson 解析:" + user2);
        } catch (Exception e) {
            System.out.println("------>gson 解析异常:" + e);
        }


        try {
            User user = gson.fromJson(jsonStrFromServer, User.class);
            System.out.println("------>JsonAdapter 注解 解析:" + user);
        } catch (JsonParseException e) {//java.lang.NumberFormatException: empty String
            System.out.println("------>JsonAdapter 注解 异常:" + e);
        }
    }
}
```

运行结果:
```
------->jsonFromServer:{
    "age": "",
    "name": "zhangsan"
}
------>gson 解析异常:com.google.gson.JsonSyntaxException: java.lang.NumberFormatException: empty String
------>JsonAdapter 注解 解析:User{name='zhangsan', age=0}
```

可以看到我们成功地跳过服务端返回json不合理的坑了吧。

#### 5. JsonDeserializer
其实上面的方式保险是很保险，但是需要维护的地方多，代码量大，我们介绍另外一种方式:JsonSerializer与JsonDeserializer 之关系单方面的处理，可以**全局注册某个类型的处理**：
看实例：
```java
public class GsonTest {

    public static void main(String[] args) throws Exception {
        JsonDeserializer<Integer> jsonDeserializer = new JsonDeserializer<Integer>() {
            @Override
            public Integer deserialize(JsonElement json, Type typeOfT, JsonDeserializationContext context)
                    throws JsonParseException {
                try {
                    return json.getAsInt();
                } catch (NumberFormatException e) {
                    return 0;
                }
            }
        };

        String jsonStrFromServer = "{\n" +
                "    \"age\": \"\",\n" +
                "    \"name\": \"zhangsan\"\n" +
                "}";
        System.out.println("------->jsonFromServer:" + jsonStrFromServer);

        Gson gson = new GsonBuilder()
                .registerTypeAdapter(int.class, jsonDeserializer)
                .create();
        try {
            User user = gson.fromJson(jsonStrFromServer, User.class);
            System.out.println("------>  JsonDeserializer<Integer> 解析:" + user);
        } catch (Exception e) {
            System.out.println("------>  JsonDeserializer<Integer> 解析异常:" + e);
        }

        Gson gson1 = new Gson();
        try {
            User user1 = gson1.fromJson(jsonStrFromServer, User.class);
            System.out.println("------> 默认gson 解析:" + user1);
        } catch (Exception e) {
            System.out.println("------>  默认gson 解析异常:" + e);
        }


    }

    public static class User {
        public String name;
        public int age;

        @Override
        public String toString() {
            return "User{" +
                    "name='" + name + '\'' +
                    ", age=" + age +
                    '}';
        }
    }
}
```
运行结果:

```
------->jsonFromServer:{
    "age": "",
    "name": "zhangsan"
}
------>  JsonDeserializer<Integer> 解析:User{name='zhangsan', age=0}
------>  默认gson 解析异常:com.google.gson.JsonSyntaxException: java.lang.NumberFormatException: empty String
```

 这样定义全局的反序列化工具就能避免解析异常