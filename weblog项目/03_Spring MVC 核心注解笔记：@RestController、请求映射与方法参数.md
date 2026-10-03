
## 第一章 @RestController 注解

### 1.1 它是什么

`@RestController` 是 Spring 用来**快速构建 RESTful API** 的注解。标注在类上，表示这个类是一个"返回数据"的控制器。

### 1.2 本质：一个组合注解

```java
@Controller      // ① 标记为 Spring 组件，能被扫描识别
@ResponseBody    // ② 返回值直接写入 HTTP 响应体，而不是去找视图
public @interface RestController { }
```

**@RestController = @Controller + @ResponseBody**

| 注解 | 职责 |
|------|------|
| `@Controller` | 把类注册为 Spring 容器中的组件，标识为 MVC 控制器 |
| `@ResponseBody` | 让方法返回值**直接作为响应数据**返回给客户端 |

### 1.3 @Controller vs @RestController（核心区别）

| | `@Controller` | `@RestController` |
|---|---|---|
| 返回值的含义 | **视图名**（去找 HTML 模板渲染） | **数据**（直接写入响应体） |
| 返回 `"index"` 的结果 | 渲染 `templates/index.html` 页面 | 浏览器收到纯文本 `"index"` |
| 适用场景 | 服务端渲染页面（传统 MVC） | 前后端分离的 API 接口 |

```java
@Controller
public class PageController {
    @GetMapping("/index")
    public String index() {
        return "index";  // 找 index.html 模板渲染，找不到会报错
    }
}

@RestController
public class ApiController {
    @GetMapping("/index")
    public String index() {
        return "index";  // 浏览器直接收到字符串 "index"
    }
}
```

### 1.4 对象为什么能自动变成 JSON？

```java
@GetMapping("/user/{id}")
public User getUser(@PathVariable Long id) {
    return new User(id, "张三", 25);   // 客户端收到 JSON
}
```

**幕后机制：`HttpMessageConverter`（消息转换器）**

```
方法返回 User 对象
    ↓
发现 @ResponseBody 生效（@RestController 隐含）
    ↓
MappingJackson2HttpMessageConverter 接手
    ↓
Jackson 库把对象序列化为 JSON 字符串
    ↓
写入响应体，Content-Type 设为 application/json
```

只要引入 `spring-boot-starter-web`（自带 Jackson），转换全自动，无需手写。

> ⚠️ 实体类必须有 getter/setter（或用 Lombok `@Data`），否则 Jackson 无法序列化。

---

## 第二章 请求映射注解：@GetMapping 及其家族

### 2.1 背景知识：HTTP 请求方法

浏览器发起请求时，请求行中包含**方法 + 路径**：

```http
GET /hello HTTP/1.1
Host: localhost:8080
```

| HTTP 方法 | 语义 | 典型场景 |
|-----------|------|----------|
| GET | 获取资源（只读） | 查询用户、获取列表 |
| POST | 创建资源 | 注册用户、提交表单 |
| PUT | 全量更新 | 修改对象全部字段 |
| DELETE | 删除资源 | 删除用户 |
| PATCH | 局部更新 | 只改手机号 |

### 2.2 @GetMapping 的作用

```java
@GetMapping("/hello")
public String hello() {
    return "Hello, World!";
}
```

含义：**"当收到 GET 方法、路径为 /hello 的请求时，调用此方法处理。"**

本质是在 Spring 的映射表中登记了一条路由规则：

```
GET /hello  →  UserController.hello()
```

### 2.3 注解家族对比

```java
@RestController
@RequestMapping("/users")        // 类级路径前缀，所有方法自动带上
public class UserController {

    @GetMapping("/{id}")         // GET    /users/1   查询
    public User get(@PathVariable Long id) { ... }

    @PostMapping                 // POST   /users     新增
    public User create(@RequestBody User user) { ... }

    @PutMapping("/{id}")         // PUT    /users/1   全量更新
    public User update(@PathVariable Long id, @RequestBody User user) { ... }

    @DeleteMapping("/{id}")      // DELETE /users/1   删除
    public void delete(@PathVariable Long id) { ... }

    @PatchMapping("/{id}")       // PATCH  /users/1   局部更新
    public User patch(@PathVariable Long id, @RequestBody User user) { ... }
}
```

### 2.4 追根溯源：@RequestMapping 是"爸爸"

```java
@RequestMapping(method = RequestMethod.GET)   // @GetMapping 源码里就是它
public @interface GetMapping { }
```

| 快捷注解 | 等价写法 |
|---------|---------|
| `@GetMapping` | `@RequestMapping(method = GET)` |
| `@PostMapping` | `@RequestMapping(method = POST)` |
| `@PutMapping` | `@RequestMapping(method = PUT)` |
| `@DeleteMapping` | `@RequestMapping(method = DELETE)` |

> 💡 推荐快捷写法：HTTP 方法直接体现在注解名里，语义一目了然。
> ⚠️ `@RequestMapping` 不写 method 时，**匹配所有 HTTP 方法**，一般不推荐。

---

## 第三章 核心原理：方法为什么会"自动执行"？

### 3.1 一个请求的完整旅程

```
浏览器发起 GET /hello
    ↓
① 内嵌 Tomcat 接收请求，解析出：方法=GET，路径=/hello
    ↓
② DispatcherServlet（总调度员，所有请求的入口）
    ↓
③ HandlerMapping 查映射表：GET + /hello → hello() 方法
    ↓
④ 通过 Java 反射调用方法：method.invoke(controller实例)
    ↓
⑤ @ResponseBody 生效，返回值写入响应体
    ↓
浏览器收到 Hello, World!
```

### 3.2 映射表何时建立？—— 应用启动时

```
Spring Boot 启动
    ↓
扫描所有 @RestController / @Controller 类
    ↓
发现方法上的 @GetMapping("/hello")
    ↓
登记映射表：key = GET + /hello，value = hello 方法
    ↓
启动完成，等待请求
```

### 3.3 反射调用（用代码模拟 Spring 底层）

```java
// 启动时：登记
Method helloMethod = UserController.class.getMethod("hello");
mappingTable.put("GET /hello", helloMethod);

// 请求到来时：查表 + 反射调用
Method target = mappingTable.get("GET /hello");
Object result = target.invoke(controller实例);   // 方法在此被执行
```

**结论：方法不是"自动执行"，而是 Spring 启动时登记、请求到来时查表、通过反射替你调用。**

---

## 第四章 方法参数：代表"想从请求中取什么数据"

### 4.1 核心认知

> **方法的每个参数 = 向 Spring 声明"我要这个 HTTP 请求中的哪一部分数据"。**
> 注解指定取数位置，Spring 在调用前负责提取、类型转换、塞进参数。

### 4.2 HTTP 请求的四个数据位置

```http
POST /users/42?notify=true HTTP/1.1     ← 路径 + 查询参数
Authorization: Bearer abc123             ← 请求头
Content-Type: application/json

{"name": "张三", "age": 25}              ← 请求体
```

### 4.3 四大参数注解对照

```java
@PostMapping("/users/{id}")
public String update(
        @PathVariable Long id,                        // ← 路径中的 42
        @RequestParam boolean notify,                 // ← 查询参数 notify=true
        @RequestHeader("Authorization") String token, // ← 请求头
        @RequestBody User user                        // ← 请求体 JSON 转对象
) { ... }
```

| 注解 | 数据来源 | 请求示例 | 典型用途 |
|------|---------|---------|---------|
| `@PathVariable` | URL 路径占位符 | `/users/42` | 定位具体资源 |
| `@RequestParam` | URL `?` 后的键值对 | `?page=2` | 查询条件、分页 |
| `@RequestBody` | 请求体（JSON） | POST + body | 提交复杂对象 |
| `@RequestHeader` | 请求头 | `Authorization: xxx` | 获取令牌等 |
| `@CookieValue` | Cookie | `Cookie: sid=abc` | 获取 Cookie 值 |

### 4.4 易混淆点辨析

**@PathVariable vs @RequestParam（都在 URL 中）**

```
GET /users/42?status=active
         ↑         ↑
   @PathVariable  @RequestParam
   定位"哪个资源"   附加"筛选条件"
```

**@RequestParam vs @RequestBody（都是提交数据）**

| | @RequestParam | @RequestBody |
|---|---|---|
| 数据位置 | URL `?` 后 | 请求体内 |
| 格式 | 键值对 | JSON |
| 适合 | 少量简单参数 | 复杂对象 |
| 每方法数量 | 可多个 | **只能一个**（请求体只有一份） |

### 4.5 不加注解的参数

- **简单类型**（String、int、Long）：默认按 `@RequestParam` 处理（建议显式加注解，可读性好且能配置 `required`、`defaultValue`）
- **特殊类型**：Spring 直接注入框架对象，与请求数据无关

```java
@GetMapping("/demo")
public String demo(HttpServletRequest request,   // 原生请求对象
                   HttpSession session) {         // 会话对象
    String ip = request.getRemoteAddr();          // 客户端 IP
    session.setAttribute("user", "张三");
    return "ok";
}
```

### 4.6 底层机制：参数解析器

Spring 在反射调用前，由 `HandlerMethodArgumentResolver` 逐个准备参数：

```
遍历方法的每个参数
    ├─ @PathVariable → PathVariableMethodArgumentResolver → URL 提取 "42" → 转 Long
    ├─ @RequestParam → RequestParamMethodArgumentResolver → 查询串提取 "true" → 转 boolean
    └─ @RequestBody  → RequestResponseBodyMethodProcessor → Jackson 反序列化 → User 对象
    ↓
method.invoke(controller, 42L, true, user对象)
```

### 4.7 常见坑点

| 坑 | 现象 | 解决 |
|---|---|---|
| 参数名与占位符不一致 | `Missing URI template variable 'id'` | 参数名一致，或 `@PathVariable("id")` 显式指定 |
| 类型转换失败 | `/users/abc` 传给了 `Long id` | 保证 URL 值能转成目标类型 |
| GET 请求用 @RequestBody | 拿到 null（body 被丢弃） | 提交数据用 POST/PUT |
| @RequestParam 默认必传 | 缺参数报 400 错误 | `required = false` 或 `defaultValue = "1"` |

---

## 第五章 全局知识体系图

```
                        @RestController
                       (= @Controller + @ResponseBody)
                       返回值 → HttpMessageConverter → JSON
                              │
        ┌─────────────────────┼─────────────────────┐
        ▼                     ▼                     ▼
   @GetMapping          @PostMapping          @DeleteMapping ...
   （绑定 GET）          （绑定 POST）          （绑定 DELETE）
        │                     │                     │
        └─────────────────────┴─────────────────────┘
                              │
                   都是 @RequestMapping 的快捷写法
                              │
        ┌─────────────────────┼─────────────────────┐
        ▼                     ▼                     ▼
   @PathVariable         @RequestParam          @RequestBody
   （路径参数）           （查询参数）            （请求体）
```

### 核心结论三句话

1. **@RestController** = @Controller + @ResponseBody，方法返回值是"数据"而非"页面"，自动转 JSON。
2. **映射注解**（@GetMapping 等）把"HTTP 方法 + 路径"绑定到 Java 方法；方法由 Spring **启动时建表、请求时查表、反射调用**。
3. **方法参数**代表"从请求中取哪部分数据"，注解声明取数位置，Spring 的参数解析器负责提取、转换、注入。

---

## 附：标准 RESTful 接口模板

```java
@RestController
@RequestMapping("/api/users")
public class UserController {

    @GetMapping("/{id}")                                     // 查单个
    public User getById(@PathVariable Long id) { ... }

    @GetMapping                                              // 条件查询
    public List<User> list(@RequestParam(required = false) String name) { ... }

    @PostMapping                                             // 新增
    public ResponseEntity<User> create(@RequestBody @Valid User user) {
        User saved = userService.save(user);
        return ResponseEntity.status(201).body(saved);       // 可控制状态码
    }

    @PutMapping("/{id}")                                     // 更新
    public User update(@PathVariable Long id, @RequestBody User user) { ... }

    @DeleteMapping("/{id}")                                  // 删除
    @ResponseStatus(HttpStatus.NO_CONTENT)                   // 返回 204
    public void delete(@PathVariable Long id) { ... }
}
```

> 📝 **返回值选择**：直接返回对象（默认 200）｜`ResponseEntity`（需控制状态码/响应头）｜`void` + `@ResponseStatus`（无响应体）