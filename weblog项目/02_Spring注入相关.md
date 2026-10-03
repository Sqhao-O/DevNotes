

## 一、Spring 启动时创建哪些对象

容器里的 Bean 来源：

- **框架自带的基础设施 Bean**（处理注解的后置处理器等）
- **自动配置的 Bean**（引入 starter 后满足条件的，如 DispatcherServlet）
- **你的代码**：加了注解的类 + `@Bean` 方法返回值 + `@Import` 导入的类

默认**非懒加载的单例 Bean 在启动时全部创建好**，存入容器。

## 二、常用注解体系

### 注解之间的派生关系

```
@Component（根注解，通用组件）
   ├── @Service（业务层，纯语义）
   ├── @Repository（持久层，额外功能：异常转换）
   ├── @Controller（控制层，额外功能：MVC 识别）
   └── @Configuration（配置类）

@RestController = @Controller + @ResponseBody（返回JSON数据）
```

**注册 Bean 的功能完全一样，区别只在语义和特殊能力。**

## 三、Bean 的默认名称

| 定义方式 | 默认名称 |
|---------|---------|
| `@Service` 等注解 | 类名首字母小写：`UserService` → `userService` |
| `@Bean` 方法 | 方法名 |
| 特殊情况 | 前两个字母都大写则不转换：`URLService` → `URLService` |

## 四、核心结论：怎么"用"一个 Bean

> **对象不需要自己 new，声明字段即可，Spring 自动注入。**

三种注入方式：

```java
// ① 构造器注入（推荐）
private final UserService userService;
public UserController(UserService userService) { this.userService = userService; }

// ② 字段注入（最省事）
@Autowired
private UserService userService;

// ③ Lombok 简化版（项目常用）
//当类里只有一个构造器时，Spring 会隐式地把它当作注入点，不需要写 `@Autowired`。这是 Spring 4.3 之后的行为。所以 `@RequiredArgsConstructor` + 单个构造器 = 自动构造器注入，连 `@Autowired` 都省了
@RequiredArgsConstructor
private final UserService userService;
```

**关键认知：**

- 注入靠的是**类型匹配**，变量名随便起（`uS`、`abc` 都行）
- 只有**一个构造器时不需要写 @Autowired**，Spring 自动注入
- `@Autowired` 是"向仓库取货"，不是"创建对象"

## 五、一个接口多个实现怎么办

```java
// 方案1：@Primary 指定默认首选（加在实现类上）
@Service @Primary
public class AliPayService implements PayService { }

// 方案2：@Qualifier 点名
@Autowired
@Qualifier("wxPayService")
private PayService payService;

// 方案3：注入全部
@Autowired
private Map<String, PayService> payMap;   // key是Bean名
```

⚠️ 此时变量名不能随便起——字段名会被用来按名称兜底匹配。

## 六、注入的三种方式区别

| 方式 | 时机 | 特点 |
|------|------|------|
| 构造器注入 | new 对象时装好 | **对象一出生就完整**，字段可 final，推荐 |
| set 方法注入 | new 之后再调用 set | 有"空窗期" |
| 字段注入 | new 之后反射赋值 | 最简洁但难测试 |

## 七、常见错误

| 问题 | 原因 | 解决 |
|------|------|------|
| 自己 `new` 的对象内部报 NPE | 没走 Spring，其内部 @Autowired 字段是 null | 必须从容器获取（注入） |
| `NoSuchBeanDefinitionException` | 没加注解 / 不在扫描包内 | 加注解、移到启动类子包 |
| `found 2 beans` 报错 | 接口多实现 | @Primary / @Qualifier |

## 八、思想层面（为什么要 Spring）

```
1. 对象天然互相依赖（车需要发动机）—— 无法改变的事实
2. 不用 Spring：谁用谁自己组装（new + 传零件）—— 组装代码泛滥
3. 用 Spring：管家统一组装一次存仓库，谁用谁拿 —— 你只负责"用"
```

- `@Service` 等注解 = **把零件/成品交给仓库**
- `@Autowired` / 构造器 = **取货单**
- 项目越大，这套机制越划算

## 九、标准代码模板（背下来就能干活）

```java
// 提供方：定义Bean
@Service
public class UserService {
    public void query() { ... }
}

// 使用方：注入使用
@RestController
@RequiredArgsConstructor          // Lombok自动生成构造器
public class UserController {

    private final UserService userService;   // 声明即可，无需new

    @GetMapping("/test")
    public void test() {
        userService.query();       // 直接用
    }
}
```

**三句话口诀：类上加注解交出去，字段声明拿进来，多实现加 @Primary。**