
## 前言


在 Java 开发中，解析 JSON 是一个非常常见的需求。


不管是和前端交互、调用第三方接口，还是处理配置文件，几乎都绕不开 JSON。


这篇文章总结了6种主流的 JSON 解析方法，希望对你会有所帮助。


(我最近开源了一个基于 SpringBoot\+Vue\+uniapp 的商城项目，欢迎访问和star。)\[[https://gitee.com/dvsusan/susan\_mall](https://github.com)]


## 1\. 使用 Jackson：业界标配


#### **功能特点**


* **强大的序列化和反序列化**：支持将 JSON 字符串转为 Java 对象，也支持将 Java 对象转换为 JSON。
* **支持复杂结构**：处理嵌套对象、数组、泛型等场景非常轻松。
* **支持注解**：如 `@JsonIgnore`、`@JsonProperty` 等，能精细控制序列化与反序列化的行为。
* **性能高**：Jackson 的性能非常出色，是很多企业级项目的首选。


#### **代码示例**


**1\. JSON 转对象（反序列化）**



```
import com.fasterxml.jackson.databind.ObjectMapper;

public class JacksonExample {
    public static void main(String[] args) throws Exception {
        String json = "{\"id\":1,\"name\":\"张三\"}";

        ObjectMapper objectMapper = new ObjectMapper();
        User user = objectMapper.readValue(json, User.class);
        System.out.println(user.getName()); // 输出：张三
    }
}

class User {
    private int id;
    private String name;

    // Getters 和 Setters 省略
}

```

**2\. 对象转 JSON（序列化）**



```
User user = new User();
user.setId(1);
user.setName("李四");

ObjectMapper objectMapper = new ObjectMapper();
String json = objectMapper.writeValueAsString(user);
System.out.println(json); // 输出：{"id":1,"name":"李四"}

```

#### **高级功能**


* **日期格式化**：`@JsonFormat(pattern = "yyyy-MM-dd")`
* **忽略字段**：`@JsonIgnore`
* **重命名字段**：`@JsonProperty("custom_name")`


#### **优缺点**




| 优点 | 缺点 |
| --- | --- |
| 功能全面，支持复杂场景 | 配置较多，学习成本稍高 |
| 性能高，社区活跃，企业级项目首选 | 过于强大，部分功能用不上 |
| 丰富的注解支持，便于控制序列化行为 | 库体积较大，对于小型项目略显笨重 |


## 2\. 使用 Gson：轻量好用


#### **功能特点**


* **轻量级**：Gson 的设计非常简洁，代码量少，适合中小型项目。
* **支持泛型**：可以轻松解析带泛型的 JSON。
* **注解控制**：支持通过注解控制序列化行为，如 `@Expose`。
* **易扩展**：通过自定义序列化器和反序列化器，可以处理复杂的场景。


#### **代码示例**


**1\. JSON 转对象**



```
import com.google.gson.Gson;

public class GsonExample {
    public static void main(String[] args) {
        String json = "{\"id\":1,\"name\":\"王五\"}";

        Gson gson = new Gson();
        User user = gson.fromJson(json, User.class);
        System.out.println(user.getName()); // 输出：王五
    }
}

```

**2\. 对象转 JSON**



```
User user = new User();
user.setId(2);
user.setName("赵六");

Gson gson = new Gson();
String json = gson.toJson(user);
System.out.println(json); // 输出：{"id":2,"name":"赵六"}

```

#### **高级功能**


* **忽略字段**：`@Expose`
```
@Expose
private String name;

```
* **自定义序列化器/反序列化器**：
```
Gson gson = new GsonBuilder()
        .registerTypeAdapter(CustomClass.class, new CustomSerializer())
        .create();

```


#### **优缺点**




| 优点 | 缺点 |
| --- | --- |
| 轻量级，简单易用，适合中小型项目 | 性能稍逊于 Jackson |
| 学习曲线平滑，新手容易上手 | 功能不如 Jackson 丰富 |
| 提供良好的扩展能力 | 复杂对象处理起来较为麻烦 |


## 3\. 使用 FastJSON：高性能


#### **功能特点**


* **性能优异**：FastJSON 的解析速度非常快，适合大数据量场景。
* **支持动态字段**：可以轻松处理动态 JSON 数据。
* **强大的类型支持**：支持嵌套对象、泛型、数组等复杂结构。
* **注解控制**：类似 Jackson 和 Gson，支持注解控制字段的序列化和反序列化。


#### **代码示例**


**1\. JSON 转对象**



```
import com.alibaba.fastjson.JSON;

public class FastJsonExample {
    public static void main(String[] args) {
        String json = "{\"id\":1,\"name\":\"小明\"}";

        User user = JSON.parseObject(json, User.class);
        System.out.println(user.getName()); // 输出：小明
    }
}

```

**2\. 对象转 JSON**



```
User user = new User();
user.setId(3);
user.setName("小红");

String json = JSON.toJSONString(user);
System.out.println(json); // 输出：{"id":3,"name":"小红"}

```

#### **高级功能**


* **自动驼峰转下划线**：
```
JSON.DEFFAULT_DATE_FORMAT = "yyyy-MM-dd HH:mm:ss";

```
* **动态字段解析**：
```
Map map = JSON.parseObject(json, Map.class);

```


#### **优缺点**




| 优点 | 缺点 |
| --- | --- |
| 性能极高，解析速度快 | 曾有历史安全漏洞的争议 |
| 支持复杂的动态字段解析 | 社区活跃度稍逊于 Jackson 和 Gson |
| 功能全面，适合大规模数据处理场景 | 配置选项和 API 比较多，稍显复杂 |


## 4\. 使用 JsonPath：快速提取嵌套字段


#### **功能特点**


* **高效字段提取**：通过路径表达式（类似 XPath）快速提取嵌套字段。
* **灵活性强**：支持动态字段和条件过滤。
* **轻量级**：专注于字段提取，功能简单明确。


#### **代码示例**



```
import com.jayway.jsonpath.JsonPath;

public class JsonPathExample {
    public static void main(String[] args) {
        String json = """
            {
                "store": {
                    "book": [
                        {"title": "书1", "price": 10},
                        {"title": "书2", "price": 20}
                    ]
                }
            }
        """;

        // 提取第一个书籍的标题
        String title = JsonPath.read(json, "$.store.book[0].title");
        System.out.println(title); // 输出：书1

        // 提取所有书籍价格
        List prices = JsonPath.read(json, "$.store.book[*].price");
        System.out.println(prices); // 输出：[10, 20]
    }
}

```

#### **优缺点**




| 优点 | 缺点 |
| --- | --- |
| 字段提取简洁高效 | 不支持序列化和反序列化 |
| 动态字段处理能力强 | 依赖 JsonPath 语法 |
| 适合快速提取嵌套字段 | 不适合全量 JSON 转换 |


## 5\. 使用 org.json：轻量工具类


#### **功能特点**


* **轻量级**：核心是一个工具类，适合简单场景。
* **构造和解析简单**：适合快速创建 JSON 或提取字段。
* **灵活性一般**：不支持复杂对象映射。


#### **代码示例**



```
import org.json.JSONObject;

public class OrgJsonExample {
    public static void main(String[] args) {
        String json = "{\"id\":1,\"name\":\"张三\"}";

        // 提取字段
        JSONObject jsonObject = new JSONObject(json);
        System.out.println(jsonObject.getString("name")); // 输出：张三

        // 构造 JSON
        JSONObject newJson = new JSONObject();
        newJson.put("id", 2);
        newJson.put("name", "李四");
        System.out.println(newJson.toString()); // 输出：{"id":2,"name":"李四"}
    }
}

```

#### **优缺点**




| 优点 | 缺点 |
| --- | --- |
| 轻量级，适合简单场景 | 不支持复杂嵌套对象 |
| 使用简单，学习成本低 | 功能简单，扩展性差 |


## 6\. 手动解析 JSON：灵活度最高


#### **功能特点**


* **完全自由**：不依赖第三方库，自己解析 JSON。
* **动态处理**：适合不规则字段结构的 JSON。
* **代码复杂度高**：适合特殊场景。


#### **代码示例**



```
import com.fasterxml.jackson.core.type.TypeReference;
import com.fasterxml.jackson.databind.ObjectMapper;

import java.util.Map;

public class ManualParsing {
    public static void main(String[] args) throws Exception {
        String json = "{\"id\":1,\"name\":\"动态字段\"}";

        ObjectMapper objectMapper = new ObjectMapper();
        Map map = objectMapper.readValue(json, new TypeReference>() {});
        System.out.println(map.get("name")); // 输出：动态字段
    }
}

```

#### **优缺点**




| 优点 | 缺点 |
| --- | --- |
| 灵活性高，适合动态字段 | 代码复杂度高，不易维护 |
| 不依赖第三方库 | 性能和效率低于专业 JSON 库 |


## 总结


最后给大家对比一下文章中提到的6种方法各自的优缺点：




| 方法 | 适用场景 | 优点 | 缺点 |
| --- | --- | --- | --- |
| **Jackson** | 企业级项目，复杂序列化和反序列化场景 | 功能强大，性能优异，支持复杂结构 | 配置复杂，学习曲线高 |
| **Gson** | 中小型项目，简单的 JSON 转换场景 | 轻量级，简单易用 | 功能有限，性能略逊 |
| **FastJSON** | 高性能需求，大数据量的动态解析 | 性能极高，功能丰富 | 曾有安全漏洞争议，社区支持稍逊 Jackson |
| **JsonPath** | 嵌套结构复杂、动态字段提取场景 | 字段提取语法简单，灵活性强 | 不支持序列化和反序列化 |
| **org.json** | 快速解析或构造 JSON 场景 | 轻量级，适合简单场景 | 功能单一，扩展性差 |
| **手动解析** | 动态 JSON 或字段不固定的场景 | 自由度高，灵活性强 | 代码复杂，效率低于专业工具 |


工具千千万，场景最重要！


选对了工具，才能省时省力，少踩坑多摸鱼。


## 最后说一句(求关注，别白嫖我)


如果这篇文章对您有所帮助，或者有所启发的话，帮忙关注一下我的同名公众号：苏三说技术，您的支持是我坚持写作最大的动力。


求一键三连：点赞、转发、在看。


关注公众号：【苏三说技术】，在公众号中回复：进大厂，可以免费获取我最近整理的10万字的面试宝典，好多小伙伴靠这个宝典拿到了多家大厂的offer。


 本博客参考[Flowercloud 机场订阅加速](https://flowercloud6.com)。转载请注明出处！
