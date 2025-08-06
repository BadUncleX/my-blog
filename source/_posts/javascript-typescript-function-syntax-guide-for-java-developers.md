---
title: JavaScript/TypeScript 函数语法完全指南：从 Java 开发者视角理解现代 JS 函数
date: 2025-08-06
tags: 
  - javascript
  - typescript
  - java-to-javascript
  - type-annotations
  - arrow-functions
  - this-binding
  - optional-parameters
  - function-types
  - async-await
  - generics
categories: Programming
---

作为一名 Java 开发者，当你第一次看到这样的 TypeScript 代码时，可能会感到困惑：

```typescript
export const createRouter: CreateRouterFn = (options) => {
  return new Router(options)
}
```

`CreateRouterFn` 是什么？`=>` 又是什么语法？这与 Java 的方法声明有何不同？本文将从 Java 开发者熟悉的概念出发，系统性地解析 JavaScript/TypeScript 函数的各种语法形式，帮你快速掌握现代前端开发的核心语法。

<!-- more -->

## 引言：从困惑到清晰

### 触发思考的源码片段

在分析 TanStack Router 源码时，我们遇到了这样的函数定义：

```typescript
export const createRouter: CreateRouterFn = (options) => {
  return new Router(options)
}
```

对于 Java 开发者来说，这个语法包含了多个陌生概念：
- 函数作为常量被声明？
- 类型注解的位置为什么在函数名后面？
- `=>` 是什么操作符？

### Java vs JavaScript/TypeScript 的根本差异

| 特性 | Java | JavaScript/TypeScript |
|------|------|----------------------|
| **函数地位** | 方法必须属于类 | 函数是一等公民，可独立存在 |
| **类型系统** | 静态类型，编译时检查 | JS动态类型，TS可选静态类型 |
| **函数赋值** | 不能直接将方法赋值给变量 | 函数可以赋值给变量 |
| **语法灵活性** | 相对固定的语法 | 多种函数声明方式 |

## 第一章：基础语法全对比

### 1.1 函数声明方式

#### 传统函数声明

```typescript
// TypeScript/JavaScript
function createUser(name: string, age: number): User {
  return new User(name, age);
}
```

```java
// Java 等价写法
public static User createUser(String name, int age) {
    return new User(name, age);
}
```

**关键差异**：
- **类型位置**：TypeScript 在参数后用 `:` 标注类型，Java 在参数前声明类型
- **访问修饰符**：JavaScript 没有 `public/private` 概念，通过 `export` 控制可见性
- **返回类型**：TypeScript 在参数列表后用 `:` 标注，Java 在方法名前声明

#### 函数表达式（赋值给变量）

```typescript
// TypeScript - 函数作为值赋给变量
const createUser = function(name: string, age: number): User {
  return new User(name, age);
}
```

```java
// Java 8+ 使用函数式接口实现类似效果
BiFunction<String, Integer, User> createUser = 
    (name, age) -> new User(name, age);
```

**概念映射**：
- JavaScript 的函数变量 ≈ Java 的函数式接口变量
- 但 JavaScript 更直观，不需要预先定义接口

### 1.2 参数处理对比

#### 可选参数

```typescript
// TypeScript - 原生支持可选参数
function greet(name: string, greeting?: string): string {
  return `${greeting || 'Hello'}, ${name}!`;
}

// 调用方式
greet('Alice');           // "Hello, Alice!"
greet('Bob', 'Hi');      // "Hi, Bob!"
```

```java
// Java - 需要方法重载实现
public static String greet(String name) {
    return greet(name, "Hello");
}

public static String greet(String name, String greeting) {
    return greeting + ", " + name + "!";
}
```

#### 默认参数值

```typescript
// TypeScript - 直接在参数上设置默认值
function createConfig(host: string = 'localhost', port: number = 3000) {
  return { host, port };
}
```

```java
// Java - 通过方法重载或Builder模式实现
public static Config createConfig() {
    return createConfig("localhost", 3000);
}

public static Config createConfig(String host, int port) {
    return new Config(host, port);
}
```

## 第二章：箭头函数深度解析

### 2.1 箭头函数语法解构

让我们逐步分解那个令人困惑的语法：

```typescript
export const createRouter: CreateRouterFn = (options) => {
  return new Router(options)
}
```

**步骤分解**：

1. `export const createRouter` - 导出一个常量
2. `: CreateRouterFn` - 类型注解（这是函数类型，不是返回值类型！）
3. `=` - 赋值操作
4. `(options) =>` - 箭头函数的参数部分
5. `{ return new Router(options) }` - 箭头函数的函数体

### 2.2 箭头函数 vs Java Lambda

#### 基本语法对比

```typescript
// TypeScript 箭头函数
const add = (a: number, b: number): number => {
  return a + b;
}

// 简化版（单行返回）
const add = (a: number, b: number): number => a + b;
```

```java
// Java Lambda 表达式
BiFunction<Integer, Integer, Integer> add = 
    (a, b) -> a + b;

// 或者使用方法引用
BinaryOperator<Integer> add = Integer::sum;
```

#### 复杂场景对比

```typescript
// TypeScript - 数组处理
const users = ['Alice', 'Bob', 'Charlie'];

// 过滤和映射
const activeUsers = users
  .filter(name => name.length > 3)
  .map(name => ({ name, active: true }));
```

```java
// Java Stream API
List<String> users = Arrays.asList("Alice", "Bob", "Charlie");

List<User> activeUsers = users.stream()
    .filter(name -> name.length() > 3)
    .map(name -> new User(name, true))
    .collect(Collectors.toList());
```

### 2.3 箭头函数的特殊性

#### this 绑定差异（JavaScript 独有概念）

```typescript
class EventHandler {
  private message = "Hello";

  // 传统函数 - this 会变化
  handleClick1() {
    setTimeout(function() {
      console.log(this.message); // undefined！this 指向了 setTimeout
    }, 1000);
  }

  // 箭头函数 - this 保持不变
  handleClick2() {
    setTimeout(() => {
      console.log(this.message); // "Hello" - this 仍指向 EventHandler 实例
    }, 1000);
  }
}
```

**Java 对比**：Java 没有这个问题，因为没有 `this` 的动态绑定概念。

## 第三章：TypeScript 函数类型系统

### 3.1 函数类型定义和注解的价值

回到最初的困惑，`CreateRouterFn` 究竟是什么？为什么需要函数类型注解？

#### 关键理解：JavaScript 中函数是"值"，不是"方法"

这是 Java 开发者理解 JavaScript/TypeScript 的关键难点：

```java
// Java - 方法属于类，不能独立存在
public class Calculator {
    public static int add(int a, int b) {  // 这是方法，不是值
        return a + b;
    }
}
// 你不能这样做：Calculator.add = someOtherMethod;  // 编译错误！
```

```typescript
// JavaScript/TypeScript - 函数是值，可以被赋值和传递
function add(a: number, b: number): number {
    return a + b;
}

// 函数可以被重新赋值！
add = function(a: number, b: number): number {
    return a * b;  // 现在 add 实际上是乘法
}

// 函数可以作为变量传递
const myOperation = add;
```

#### 函数类型注解的实际价值

**1. 确保函数符合预期的"形状"**

```typescript
// 定义一个函数类型 - 这是个"模板"
type MathOperation = (a: number, b: number) => number;

// 现在我可以确保任何 MathOperation 类型的变量都有正确的签名
const add: MathOperation = (a, b) => a + b;        // ✅ 符合
const multiply: MathOperation = (a, b) => a * b;   // ✅ 符合

// 这些会报错！
const badFunction: MathOperation = (a) => a;       // ❌ 参数不匹配
const anotherBad: MathOperation = (a, b) => "hi";  // ❌ 返回类型不匹配
```

**Java 对比**：
```java
@FunctionalInterface
interface MathOperation {
    int apply(int a, int b);
}

MathOperation add = (a, b) -> a + b;
MathOperation multiply = (a, b) -> a * b;
```

**2. 函数作为参数时的类型安全**

```typescript
// 处理数组的函数，要求传入特定形状的操作函数
function processNumbers(
    numbers: number[], 
    operation: (a: number, b: number) => number  // 函数类型注解
): number {
    return numbers.reduce(operation);
}

// 使用时，TypeScript 会检查传入的函数是否符合要求
processNumbers([1, 2, 3, 4], (a, b) => a + b);     // ✅ 求和
processNumbers([1, 2, 3, 4], (a, b) => a * b);     // ✅ 求积

// 这些会报错！
processNumbers([1, 2, 3, 4], (a) => a);            // ❌ 参数不匹配
processNumbers([1, 2, 3, 4], (a, b) => "hello");   // ❌ 返回类型不匹配
```

**3. 创建可配置的函数工厂**

回到最初让人困惑的代码：

```typescript
// 这定义了"什么样的函数可以创建 Router"
type CreateRouterFn = (options: RouterOptions) => Router;

// 现在我可以有多种创建 Router 的方式，但都必须符合这个"形状"
export const createRouter: CreateRouterFn = (options) => {
    return new Router(options);
}

// 我还可以创建其他符合同样类型的函数
export const createTestRouter: CreateRouterFn = (options) => {
    return new Router({...options, testMode: true});
}

export const createCachedRouter: CreateRouterFn = (options) => {
    // 先检查缓存
    if (routerCache.has(options.id)) {
        return routerCache.get(options.id);
    }
    const router = new Router(options);
    routerCache.set(options.id, router);
    return router;
}

// 根据环境动态切换函数
const createRouter: CreateRouterFn = 
    process.env.NODE_ENV === 'development' 
        ? createTestRouter      // 开发环境用测试版本
        : createProductionRouter; // 生产环境用正式版本
```

**4. 插件系统的类型安全**

```typescript
// 定义插件必须实现的函数类型
type PluginInitializer = (config: PluginConfig) => PluginInstance;

class PluginManager {
    private plugins: Map<string, PluginInitializer> = new Map();

    // 注册插件时确保类型正确
    register(name: string, initializer: PluginInitializer) {
        this.plugins.set(name, initializer);
    }

    // 使用插件 - TypeScript 确保调用是安全的
    createPlugin(name: string, config: PluginConfig): PluginInstance {
        const initializer = this.plugins.get(name);
        if (!initializer) throw new Error(`Plugin ${name} not found`);
        return initializer(config);
    }
}
```

#### 函数类型注解的价值总结

1. **确保一致性**：所有符合某种用途的函数都有相同的签名
2. **类型安全**：传递函数时不会出现参数不匹配的问题  
3. **代码提示**：IDE 可以准确提示函数的参数和返回值
4. **重构安全**：修改函数签名时，所有使用的地方会自动报错
5. **文档作用**：类型就是最好的文档，说明函数的用途和约束

**Java 开发者的理解方式**：
- 函数类型 ≈ Java 的函数式接口
- 但 JavaScript 的函数类型更灵活，可以直接定义而不需要先创建接口

#### 等价的接口定义方式

```typescript
// 类型别名方式
type CreateRouterFn = (options: RouterOptions) => Router;

// 接口方式
interface CreateRouterFunction {
  (options: RouterOptions): Router;
}
```

### 3.2 高阶函数类型注解

#### 函数作为参数

```typescript
// TypeScript - 函数作为参数
function processData(
  data: string[], 
  processor: (item: string) => string
): string[] {
  return data.map(processor);
}

// 使用
const result = processData(['a', 'b'], item => item.toUpperCase());
```

```java
// Java - 使用 Function 接口
public static List<String> processData(
    List<String> data, 
    Function<String, String> processor
) {
    return data.stream()
        .map(processor)
        .collect(Collectors.toList());
}

// 使用
List<String> result = processData(
    Arrays.asList("a", "b"), 
    String::toUpperCase
);
```

#### 函数作为返回值

```typescript
// TypeScript - 工厂函数
function createValidator(minLength: number): (input: string) => boolean {
  return (input: string) => input.length >= minLength;
}

// 使用
const validateEmail = createValidator(5);
const isValid = validateEmail("test@example.com"); // true
```

```java
// Java - 返回函数式接口
public static Function<String, Boolean> createValidator(int minLength) {
    return input -> input.length() >= minLength;
}

// 使用
Function<String, Boolean> validateEmail = createValidator(5);
Boolean isValid = validateEmail.apply("test@example.com"); // true
```

### 3.3 复杂类型组合

```typescript
// 复杂的函数类型组合
type AsyncProcessor<T, R> = (input: T) => Promise<R>;
type ErrorHandler = (error: Error) => void;

interface DataPipeline<T, R> {
  process: AsyncProcessor<T, R>;
  onError: ErrorHandler;
  retryCount?: number;
}

// 实现
const userPipeline: DataPipeline<UserInput, User> = {
  process: async (input) => {
    // 异步处理逻辑
    return new User(input.name, input.age);
  },
  onError: (error) => {
    console.error('Processing failed:', error);
  },
  retryCount: 3
};
```

## 第四章：现代 JavaScript/TypeScript 函数特性

### 4.1 异步函数（async/await）

这是 Java 开发者需要重新学习的重要概念。

#### 基本异步函数

```typescript
// TypeScript 异步函数
async function fetchUserData(userId: string): Promise<User> {
  try {
    const response = await fetch(`/api/users/${userId}`);
    const userData = await response.json();
    return new User(userData);
  } catch (error) {
    throw new Error(`Failed to fetch user: ${error.message}`);
  }
}

// 箭头函数版本
const fetchUserData = async (userId: string): Promise<User> => {
  const response = await fetch(`/api/users/${userId}`);
  return new User(await response.json());
}
```

**Java 对比**：
```java
// Java - 使用 CompletableFuture
public static CompletableFuture<User> fetchUserData(String userId) {
    return CompletableFuture.supplyAsync(() -> {
        try {
            // HTTP 请求逻辑（伪代码）
            String response = httpClient.get("/api/users/" + userId);
            UserData userData = objectMapper.readValue(response, UserData.class);
            return new User(userData);
        } catch (Exception e) {
            throw new RuntimeException("Failed to fetch user: " + e.getMessage());
        }
    });
}
```

#### 并行异步处理

```typescript
// TypeScript - 并行处理
async function loadUserProfile(userId: string) {
  const [user, posts, friends] = await Promise.all([
    fetchUser(userId),
    fetchUserPosts(userId),
    fetchUserFriends(userId)
  ]);
  
  return { user, posts, friends };
}
```

```java
// Java - CompletableFuture 并行处理
public static CompletableFuture<UserProfile> loadUserProfile(String userId) {
    CompletableFuture<User> userFuture = fetchUser(userId);
    CompletableFuture<List<Post>> postsFuture = fetchUserPosts(userId);
    CompletableFuture<List<User>> friendsFuture = fetchUserFriends(userId);
    
    return CompletableFuture.allOf(userFuture, postsFuture, friendsFuture)
        .thenApply(ignored -> new UserProfile(
            userFuture.join(),
            postsFuture.join(),
            friendsFuture.join()
        ));
}
```

### 4.2 泛型函数

#### TypeScript 泛型函数

```typescript
// 基本泛型函数
function identity<T>(arg: T): T {
  return arg;
}

// 箭头函数泛型
const identity = <T>(arg: T): T => arg;

// 约束泛型
interface Lengthwise {
  length: number;
}

function getLength<T extends Lengthwise>(arg: T): number {
  return arg.length;
}

// 使用
const stringLength = getLength("hello"); // 5
const arrayLength = getLength([1, 2, 3]); // 3
```

**Java 对比**：
```java
// Java 泛型方法
public static <T> T identity(T arg) {
    return arg;
}

// 有界泛型
public static <T extends Lengthwise> int getLength(T arg) {
    return arg.getLength();
}

// 使用
String result1 = identity("hello");
Integer result2 = identity(42);
```

#### 复杂泛型场景

```typescript
// 高级泛型函数
type KeyValuePair<K, V> = {
  key: K;
  value: V;
}

function createMap<K extends string | number, V>(
  pairs: KeyValuePair<K, V>[]
): Map<K, V> {
  const map = new Map<K, V>();
  pairs.forEach(pair => map.set(pair.key, pair.value));
  return map;
}

// 使用
const userMap = createMap([
  { key: 'alice', value: new User('Alice') },
  { key: 'bob', value: new User('Bob') }
]);
```

### 4.3 函数重载

TypeScript 独有的特性，Java 通过方法重载实现类似效果。

```typescript
// TypeScript 函数重载声明
function process(input: string): string;
function process(input: number): number;
function process(input: boolean): string;

// 实际实现
function process(input: string | number | boolean): string | number {
  if (typeof input === 'string') {
    return input.toUpperCase();
  } else if (typeof input === 'number') {
    return input * 2;
  } else {
    return input.toString();
  }
}

// 使用 - TypeScript 会根据参数类型推断返回值类型
const stringResult = process("hello");    // 类型: string
const numberResult = process(42);         // 类型: number
const booleanResult = process(true);      // 类型: string
```

**Java 对比**：
```java
// Java 方法重载
public static String process(String input) {
    return input.toUpperCase();
}

public static int process(int input) {
    return input * 2;
}

public static String process(boolean input) {
    return String.valueOf(input);
}
```

## 第五章：实际应用场景

### 5.1 事件处理函数

#### DOM 事件处理

```typescript
// TypeScript 事件处理
class ButtonHandler {
  private clickCount = 0;

  // 箭头函数确保 this 绑定正确
  handleClick = (event: MouseEvent) => {
    this.clickCount++;
    console.log(`Button clicked ${this.clickCount} times`);
  }

  // 传统函数需要手动绑定
  handleHover(event: MouseEvent) {
    console.log('Button hovered');
  }

  setupEventListeners() {
    const button = document.getElementById('myButton');
    if (button) {
      button.addEventListener('click', this.handleClick);
      // 传统函数需要 bind
      button.addEventListener('mouseenter', this.handleHover.bind(this));
    }
  }
}
```

#### React 组件中的事件处理

```typescript
// React 函数组件
interface UserFormProps {
  onSubmit: (user: User) => void;
  onCancel: () => void;
}

const UserForm: React.FC<UserFormProps> = ({ onSubmit, onCancel }) => {
  const [name, setName] = useState('');
  const [email, setEmail] = useState('');

  // 表单提交处理
  const handleSubmit = (e: React.FormEvent) => {
    e.preventDefault();
    if (name && email) {
      onSubmit(new User(name, email));
    }
  };

  // 输入变化处理
  const handleNameChange = (e: React.ChangeEvent<HTMLInputElement>) => {
    setName(e.target.value);
  };

  return (
    <form onSubmit={handleSubmit}>
      <input 
        value={name} 
        onChange={handleNameChange}
        placeholder="Name" 
      />
      <input 
        value={email} 
        onChange={(e) => setEmail(e.target.value)}
        placeholder="Email" 
      />
      <button type="submit">Submit</button>
      <button type="button" onClick={onCancel}>Cancel</button>
    </form>
  );
};
```

### 5.2 函数式编程模式

#### 数据处理管道

```typescript
// 函数式数据处理
interface Product {
  id: string;
  name: string;
  price: number;
  category: string;
}

class ProductService {
  // 纯函数 - 无副作用
  private static filterByCategory = (category: string) => 
    (product: Product) => product.category === category;

  private static filterByPrice = (maxPrice: number) => 
    (product: Product) => product.price <= maxPrice;

  private static sortByPrice = (ascending: boolean = true) => 
    (a: Product, b: Product) => 
      ascending ? a.price - b.price : b.price - a.price;

  // 组合函数创建处理管道
  static searchProducts(
    products: Product[],
    category?: string,
    maxPrice?: number,
    sortAscending = true
  ): Product[] {
    let result = products;

    if (category) {
      result = result.filter(this.filterByCategory(category));
    }

    if (maxPrice) {
      result = result.filter(this.filterByPrice(maxPrice));
    }

    return result.sort(this.sortByPrice(sortAscending));
  }
}

// 使用
const products = [
  { id: '1', name: 'Laptop', price: 1000, category: 'Electronics' },
  { id: '2', name: 'Book', price: 20, category: 'Education' },
  { id: '3', name: 'Phone', price: 800, category: 'Electronics' }
];

const cheapElectronics = ProductService.searchProducts(
  products,
  'Electronics',
  900,
  true
);
```

### 5.3 异步数据获取和缓存

```typescript
// 高级异步模式
class DataCache<T> {
  private cache = new Map<string, { data: T; timestamp: number }>();
  private readonly TTL = 5 * 60 * 1000; // 5分钟

  // 通用获取函数，支持缓存
  async fetchWithCache<R>(
    key: string,
    fetcher: () => Promise<R>,
    ttl = this.TTL
  ): Promise<R> {
    const cached = this.cache.get(key);
    
    if (cached && Date.now() - cached.timestamp < ttl) {
      return cached.data as R;
    }

    try {
      const data = await fetcher();
      this.cache.set(key, { data: data as T, timestamp: Date.now() });
      return data;
    } catch (error) {
      // 如果有缓存数据，在网络错误时返回缓存
      if (cached) {
        console.warn('Using stale cache due to fetch error:', error);
        return cached.data as R;
      }
      throw error;
    }
  }

  // 批量获取
  async fetchBatch<R>(
    requests: Array<{
      key: string;
      fetcher: () => Promise<R>;
    }>
  ): Promise<R[]> {
    return Promise.all(
      requests.map(req => this.fetchWithCache(req.key, req.fetcher))
    );
  }
}

// 使用示例
const userCache = new DataCache<User>();

async function loadUserDashboard(userId: string) {
  const [user, posts, notifications] = await userCache.fetchBatch([
    {
      key: `user:${userId}`,
      fetcher: () => fetch(`/api/users/${userId}`).then(r => r.json())
    },
    {
      key: `posts:${userId}`,
      fetcher: () => fetch(`/api/users/${userId}/posts`).then(r => r.json())
    },
    {
      key: `notifications:${userId}`,
      fetcher: () => fetch(`/api/users/${userId}/notifications`).then(r => r.json())
    }
  ]);

  return { user, posts, notifications };
}
```

## 第六章：最佳实践和注意事项

### 6.1 何时使用不同的函数声明方式

#### 选择指南

```typescript
// 1. 普通函数声明 - 适用于顶层函数、需要提升的场合
function calculateTax(amount: number): number {
  return amount * 0.1;
}

// 2. 函数表达式 - 适用于条件性创建函数
const calculator = isAdvancedMode 
  ? function(a: number, b: number) { return a * b * 1.2; }
  : function(a: number, b: number) { return a * b; };

// 3. 箭头函数 - 适用于回调、简短逻辑、需要保持 this 绑定
const numbers = [1, 2, 3, 4, 5];
const doubled = numbers.map(n => n * 2);

// 4. 方法定义 - 适用于对象/类的方法
class Calculator {
  // 普通方法 - 可以被继承和覆盖
  calculate(a: number, b: number): number {
    return a + b;
  }

  // 箭头函数属性 - 绑定 this，但不能被覆盖
  multiply = (a: number, b: number): number => {
    return a * b;
  }
}
```

#### 性能考虑

```typescript
// ❌ 避免 - 每次渲染都创建新函数
const Component = () => {
  return (
    <button onClick={() => console.log('clicked')}>
      Click me
    </button>
  );
};

// ✅ 推荐 - 函数复用
const Component = () => {
  const handleClick = useCallback(() => {
    console.log('clicked');
  }, []);

  return <button onClick={handleClick}>Click me</button>;
};

// ✅ 推荐 - 类方法中使用箭头函数属性
class Component extends React.Component {
  handleClick = () => {
    console.log('clicked');
  }

  render() {
    return <button onClick={this.handleClick}>Click me</button>;
  }
}
```

### 6.2 类型安全的函数设计

#### 严格的类型注解

```typescript
// 完整的类型注解
type UserValidator = {
  (user: User): boolean;
  errorMessage?: string;
}

const createUserValidator = (
  rules: Array<(user: User) => boolean>,
  errorMessage = 'Invalid user'
): UserValidator => {
  const validator: UserValidator = (user: User): boolean => {
    return rules.every(rule => rule(user));
  };
  
  validator.errorMessage = errorMessage;
  return validator;
};

// 使用
const isValidUser = createUserValidator([
  (user) => user.name.length > 0,
  (user) => user.email.includes('@'),
  (user) => user.age >= 18
], 'User must have name, valid email, and be at least 18');

if (!isValidUser(someUser)) {
  console.error(isValidUser.errorMessage);
}
```

#### 泛型约束和条件类型

```typescript
// 高级类型安全设计
type ExtractReturnType<T> = T extends (...args: any[]) => infer R ? R : never;

type AsyncFunction<T extends any[], R> = (...args: T) => Promise<R>;

// 创建类型安全的异步函数包装器
function createAsyncWrapper<T extends any[], R>(
  syncFn: (...args: T) => R
): AsyncFunction<T, R> {
  return async (...args: T): Promise<R> => {
    return syncFn(...args);
  };
}

// 使用 - 完全类型安全
const syncAdd = (a: number, b: number): number => a + b;
const asyncAdd = createAsyncWrapper(syncAdd);

// TypeScript 会推断出正确的类型
const result = await asyncAdd(1, 2); // result 类型为 number
```

### 6.3 错误处理最佳实践

```typescript
// 结果类型模式（类似 Rust 的 Result）
type Result<T, E = Error> = 
  | { success: true; data: T }
  | { success: false; error: E };

// 安全的异步函数包装器
async function safeAsync<T>(
  promise: Promise<T>
): Promise<Result<T>> {
  try {
    const data = await promise;
    return { success: true, data };
  } catch (error) {
    return { 
      success: false, 
      error: error instanceof Error ? error : new Error(String(error))
    };
  }
}

// 使用示例
async function handleUserCreation(userData: UserInput) {
  const result = await safeAsync(createUser(userData));
  
  if (result.success) {
    console.log('User created:', result.data);
    return result.data;
  } else {
    console.error('Failed to create user:', result.error.message);
    throw result.error;
  }
}
```

## 总结

通过本文的深入对比和解析，我们系统性地了解了 JavaScript/TypeScript 函数语法与 Java 的差异和联系：

### 核心概念映射

| JavaScript/TypeScript | Java 对应概念 | 主要差异 |
|----------------------|--------------|---------|
| 函数声明 | 静态方法 | JS 函数独立存在，不需要类 |
| 箭头函数 | Lambda 表达式 | JS 箭头函数有 this 绑定特性 |
| 函数类型 | 函数式接口 | TS 类型系统更灵活 |
| async/await | CompletableFuture | JS 原生支持，语法更简洁 |
| 函数重载 | 方法重载 | TS 是声明式重载 |

### 学习要点总结

1. **函数是一等公民**：JavaScript 中函数可以像变量一样被传递和操作
2. **类型注解位置**：TypeScript 在参数后用 `:` 标注类型
3. **箭头函数的 this 绑定**：这是 Java 开发者需要特别注意的概念
4. **异步编程模式**：`async/await` 比 Java 的 `CompletableFuture` 更直观
5. **类型安全**：TypeScript 提供了比 Java 更灵活的类型系统

### 实践建议

- **从简单开始**：先掌握基本的函数声明和箭头函数
- **理解 this 绑定**：这是与 Java 最大的差异点
- **善用类型注解**：充分利用 TypeScript 的类型检查能力
- **拥抱异步编程**：掌握 Promise 和 async/await 模式
- **学习函数式编程**：JavaScript 对函数式编程有很好的支持

掌握了这些概念，你就能够自信地阅读和编写现代 JavaScript/TypeScript 代码，从 Java 开发者成功转型到前端开发者！