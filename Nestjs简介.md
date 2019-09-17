# Nestjs简介

<a name="d5ec6c90"></a>
## 1. 这是个什么东西

按照它官方的说法 ： Nest 是一个用于构建高效，可扩展的 [Node.js](http://nodejs.cn/) 服务器端应用程序的框架。<br />所以它的一个 nodejs 服务端框架。

我的天啊！！！，这种日子什么时候是个头啊。 一天一个框架，你看我刚做了 egg 的企业版定制，你现在又来跟我说这个啥 Nest，你看看隔壁 Java， 人家的 Spring 用了这么多年都是一个 ‘好’ 字，为啥我们 node 就得折腾来折腾去。

emmm，  其实这个问题我也很想知道，求大神解惑。

但其实换个角度想，JavaScript 的发展路线本就与 Java 不同， 对其而言，正是因为层出不穷的新东西，才造就了如此活跃的社区氛围，不是吗？

<a name="c9705776"></a>
## 2. 那么这到底是个什么东西呢

提起 `Nest`  就必须要说另一个 比较新的东西 `TypeScript` ,  传送门 [https://www.typescriptlang.org/](https://www.typescriptlang.org/) <br />简而言之， 从名字上来看， TypeScript  就是 强类型的 JavaScript， 以我的理解， Nest 就是乘着它的东风流行起来的。由于 Nest 使用 TypeScript 原生开发，是故在对 TS 的支持上要好于市面上现有框架。 而 TS 在中大型项目上的发挥空间，个人感觉会比 原生Js 要大，在这种背景下，Nest 又有其独特的定位与存在意义。

Nestjs 不是一个新轮子，它在HTTP服务上是基于 Express、Socket.io 封装的 一个框架后端开发框架，对 Typescript 开发者提供类型支持，也能优雅降级供 Js 使用。

其次这是一个MVC框架，后面有许多地方会体现出它的MVC思想。

<a name="58b03e88"></a>
### Nest使用入口main.ts
```javascript
import { NestFactory } from '@nestjs/core';
import { ApplicationModule } from './app.module';
async function bootstrap() {
  const app = await NestFactory.create(ApplicationModule);
  await app.listen(3000);
}
bootstrap();
```

上述代码解析
```javascript
exports.NestFactory = new NestFactoryStatic();

// ----------------------
async create(module, serverOrOptions, options) {
  const isHttpServer = serverOrOptions && serverOrOptions.patch;
  // tslint:disable-next-line:prefer-const
  let [httpServer, appOptions] = isHttpServer
  ? [serverOrOptions, options]
  : [express_factory_1.ExpressFactory.create(), serverOrOptions];
  const applicationConfig = new application_config_1.ApplicationConfig();
  const container = new container_1.NestContainer(applicationConfig);
  httpServer = this.applyExpressAdapter(httpServer);
  this.applyLogger(appOptions);
  await this.initialize(module, container, applicationConfig, httpServer);
  return this.createNestInstance(new nest_application_1.NestApplication(container, httpServer, applicationConfig, appOptions));
}
//--------------------------

applyExpressAdapter(httpAdapter) {
  const isAdapter = httpAdapter.getHttpServer;
  if (isAdapter) {
    return httpAdapter;
  }
  return new express_adapter_1.ExpressAdapter(httpAdapter);
}

```

这里的httpServer 最终还是使用 ExpressAdapter 来构建服务。

所以我们就来关注一下，nestjs 特别的地方。

这个框架能说的东西很多， 这里主要说一下几点东西。

<a name="bb2b48b4"></a>
#### 1. 路由 
路由定义通过装饰器方式实现，在Controller 层定义路由规则， 路由去中心化。

```
@Controller('v1')
export class UsersController {
    @Get('users')
    getAllUsers() {}

    @Get('users/:id')
    getUser() {}

    @Post('users')
    addUser() {}
}
```
那边请求 `getAllUsers()` 这个方法的路由为 `/v1/user` .<br />而装饰器提供 赋能， PS： 什么是装饰器 ，传送      门 [http://www.typescriptlang.org/docs/handbook/decorators.html](http://www.typescriptlang.org/docs/handbook/decorators.html)<br />
<br />什么是赋能，装饰器赋能指的是利用装饰器附加一些额外的属性给特定的类、方法或者属性，举个例子假如你有一把普通宝剑，这把宝剑的攻击力只有10，不过这把宝剑上有三个宝石镶嵌孔，可以将得到的宝石镶嵌上去增加宝剑的威力，如果此时主人翁有红、绿、蓝三个宝石分别可以增加攻击力10、20、30，如果将这些宝石都镶嵌上去那么这把宝剑就不是一把普通的宝剑，而是攻击力达到70的绝世好剑了。<br />如上所示，  `UsersController` 本是一个普普通通的平民类， 但是 `@Controller('v1')`  给这个类赋能后， 通过 `/v1` 这个路由 就可以进入这个controller，它就不再平凡。 但至于 该装饰器是如何实现该功能的，就不在此讨论了。<br />
<br />而这跟 egg 的这种定义方式不同，在 egg 中是统一在 router.js 中定义路由 实现路由中心化。<br />

```javascript
// app/router.js 里面定义 URL 路由规则
// app/router.js
module.exports = app => {
  const { router, controller } = app;
  router.get('/user/:id', controller.user.info);
};
```

<br />对于这两种方式那种更好，这很难下结论，这个还是由具体应用场景来决定吧


<a name="f047a546"></a>
#### 2. IoC
**依赖倒置原则（DIP， ****Dependence Inversion Principle****）**：一种软件架构设计的原则（抽象概念）。<br />
**控制反转（IoC**，**Inversion of Control****）：**一种反转流、依赖和接口的方式（DIP的具体实现方式）。<br />
**依赖注入（DI，Dependency Injection）**：IoC的一种实现方式，用来反转依赖（IoC的具体实现方式）。<br />
**IoC容器：**依赖注入的框架，用来映射依赖，管理对象创建和生存周期（DI框架）。

Bob Martins对DIP的定义：<br />高层模块不应依赖于低层模块，两者应该依赖于抽象<br />抽象不应该依赖于实现，实现应该依赖于抽象。

在后端开发中，主要专注于业务逻辑，所以后台开发非常需要一种编程范式。目前比较成熟的是面向对象思想（OOP），在这个前提下，对象的依赖、创建、生命周期管理等就成了一个问题，依赖注入正是提供了一种标准的方式来解决此问题，它将依赖的创建和销毁交给 ‘容器’ ，去管理.

举个例子： <br />比如我们现在需要 写一个订单入库，一开始我们使用 SQLServer ，我们的代码可能如下

```javascript
class Order {
 
  Add(){
    const sqlObj = new SqlServer();//添加一个私有变量保存数据库操作的对象
  	sqlObj.Add();
  }
}
```

<br />但是万一哪一天，我们又需要使用 mysql ， 那么我们又需要去修改代码<br /> 
```javascript
class Order {
 
  Add(){
    const mysqlObj = new Mysql();//添加一个私有变量保存数据库操作的对象
  	mysqlObj.Add();
  }
}
```

如果我们有非常多的这个类似的业务， 那么这个过程将会非常痛苦。

依赖注入（DI）就是提供这样一种机制，可以把底层的依赖对象的引用直接传递到上层使用，而不是在上层自己创建这个对象引用。

介绍一种实现如下：<br />通过构造函数注入。

首先，我们需要定义一个 数据库对象 抽象类 IDataBaseDao

```javascript
export interface IDataBaseDao {
  add : Function；
}
```

接着我们创建 一个 sqlserver 类，去实现数据库 add 操作
```javascript
import { IDataBaseDao } from './IDataBaseDao';
class SqlServerDaoImpl implements IDataBaseDao{
    add(){  }
}
```

再创建一个 mysql 类，同样实现该操作

```javascript
import { IDataBaseDao } from './IDataBaseDao';
class MysqlDaoImpl implements IDataBaseDao{
    add(){  }
}
```


那么，在 order 类中我们就需要通过构造函数来注入 数据库引用

```
class Order {
    
    private sqlObj;

    constructor(sqlObj : IDataBaseDao ){
        this.sqlObj = sqlObj;
    }
  
    Add(){
      this.sqlObj.add();
    }
}
```

我们在实现 订单插入时就可以这样实现

```javascript
const sqlServerObj = new SqlServerDaoImpl();
const order = new Order(sqlServerObj);
order.add();
```

这样可以保证order 类中的逻辑不需要修改。

但是 `const sqlServerObj = new SqlServerDaoImpl();`  以上的这个引用还是通过手动的方式创建的，但是对于大型项目，这个效率就很低了。正因如此， IoC 容器诞生了，所以 IoC容器实际上是一个 DI 框架，它能简化我们的工作量。

下面来看 nest 是如何实现 Ioc 容器的呢, 

```javascript
private async initialize(
    module: any,
    container: NestContainer,
    config = new ApplicationConfig(),
    httpServer: HttpServer = null,
  ) {
    const instanceLoader = new InstanceLoader(container);
    const dependenciesScanner = new DependenciesScanner(
      container,
      new MetadataScanner(),
      config,
    );
    container.setHttpAdapter(httpServer);
    try {
      this.logger.log(MESSAGES.APPLICATION_START);
      await ExceptionsZone.asyncRun(async () => {
        await dependenciesScanner.scan(module);
        await instanceLoader.createInstancesOfDependencies();
        dependenciesScanner.applyApplicationProviders();
      });
    } catch (e) {
      process.abort();
    }
  }

```

首先来看这个 初始化 方法，生成实例加载器  `InstanceLoader`  和 依赖扫描器 `DependenciesScanner` , <br />依赖扫描器 开始 扫描当前文件的依赖模块，编译 、加载，当所有的模块都注册完成后，依赖注入的准备工作就已经完成了。

接着开始创建依赖注入的实例，

```javascript
 await instanceLoader.createInstancesOfDependencies();
```

```javascript

  public async createInstancesOfDependencies() {
    const modules = this.container.getModules();

    this.createPrototypes(modules);
    await this.createInstances(modules);
  }

  private createPrototypes(modules: Map<string, Module>) {
    modules.forEach(module => {
      this.createPrototypesOfProviders(module);
      this.createPrototypesOfInjectables(module);
      this.createPrototypesOfControllers(module);
    });
  }

  private async createInstances(modules: Map<string, Module>) {
    await Promise.all(
      [...modules.values()].map(async module => {
        await this.createInstancesOfProviders(module);
        await this.createInstancesOfInjectables(module);
        await this.createInstancesOfControllers(module);

        const { name } = module.metatype;
        this.isModuleWhitelisted(name) &&
          this.logger.log(MODULE_INIT_MESSAGE`${name}`);
      }),
    );
  }
```

先通过 `createPrototypes` 创建 原型对象， 然后正式 通过 `createInstances` 开始 实例化 。

但是 nest 又是怎么去判断哪些模块是提供者 ， 哪些有是需要注入依赖的。 同样也是基于一个 @Injectable() <br />注解实现的，

```javascript
import { Injectable } from '@nestjs/common';

@Injectable()
export class AppService {
  getHello(): string {
    return 'Hello World!';
  }
}
```

当一个类使用了 @Injectable 注解 装饰，那么就可以认为这个类是一个提供者，然后我们还需要在nest 的module 中 注册该提供者。

```javascript
import { Module } from '@nestjs/common';
import { AppController } from './app.controller';
import { AppService } from './app.service';

@Module({
  imports: [],
  controllers: [AppController],
  providers: [AppService],
})
export class AppModule {}
```

注册后，上文提到的实例化就可以通过不同的 标签，来分别对 不同的角色进行实例化

```
await this.createInstancesOfProviders(module);
await this.createInstancesOfInjectables(module);
await this.createInstancesOfControllers(module);
```

最后， 使用依赖的类又是如何实现依赖注入的呢，<br />在 nest 中 可以使用下面这种方式

```javascript
constructor(private readonly appService: AppService) {}
```

在构造函数中，声明依赖项，这种做法其实就相当于 上面例子提到的，<br />通过构造函数传入依赖实例，在将该实例保存到 该类的私有属性上,  但是这个过程由nest来实现。

```javascript
private appService;
constructor(appService: AppService){
   this.appService = appService;
}
```

<a name="b33be830"></a>
#### 3. 中间件
中间件是一个在路由处理器**之前**被调用的函数。中间件函数可以访问请求和响应对象， 以及应用程序请求响应周期中的下一个中间件函数。<br />Nest 中间件实际上等价于express 中间件。<br />Express 文档中描述的中间件功能<br />![屏幕快照 2019-04-16 下午9.03.38.png](https://cdn.nlark.com/yuque/18/2019/png/289125/1555419839230-28a153cd-4e08-4696-8df3-19f99fb2fde1.png#align=left&display=inline&height=238&name=%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202019-04-16%20%E4%B8%8B%E5%8D%889.03.38.png&originHeight=251&originWidth=762&size=37880&status=done&width=724)

在 Nest 中 中间件可以是一个函数，也可以是一个带有 `@Injectable()`  装饰器的类。 如果是函数则没有特殊要求，如果是类 则需要实现 `NestMiddleware` 接口。<br />
<br />首先来看函数中间件， 格式上与express 中间件类似：

```javascript
export function testMiddleware2(req, res, next){

    console.log('Request....Test222');
    next();
}
```

类中间件：

```javascript
import { Injectable, NestMiddleware, MiddlewareFunction } from '@nestjs/common';

@Injectable()
export class TestMiddleware1 implements NestMiddleware {
  resolve(...args: any[]): MiddlewareFunction {
    return (req, res, next) => {
      console.log('Request... Test111');
      next();
    };
  }
}
```

这里 `TestMiddleware1`  需要实现 接口类的 `NestMiddleware` 的 `resolve` 方法，并返回一个中间件函数。<br />如果在中间件中有 其他依赖时， 使用类中间件会更加符合OOP思想。 如果中间件比较纯净的话，函数中间件也是不错的选择。

用法如下：

```javascript
@Module({
  imports: [],
  controllers: [AppController],
  providers: [AppService],
})
export class AppModule  implements NestModule{
  configure(consumer: import("@nestjs/common").MiddlewareConsumer): void | import("@nestjs/common").MiddlewareConsumer {
    consumer
      .apply(TestMiddleware1, testMiddleware2)
      .forRoutes(AppController);
  }
}
```

 apply() 函数中依次添加需要的插入的中间件，使用逗号分割， forRoutes() 函数可以指定中间件作用的路由范围。

<a name="25f9c7fa"></a>
### 总结
    在当前_ TypeScript _盛行的情况下，_ Nest _的出现为_web_服务开发者提供了更多的选择，同时_ Nest _本身的一些特性，也使得这个框架格外的优雅，但是一些例如_OOP_等服务端思想也会与传统的_JS_函数式编程产生碰撞。框架的出现本身就是提高开发效率，如果过于追求新事物，而降低了开发效率的话，就得不偿失了。

 分享PPT ：[Nest 分享.key](https://souche.yuque.com/attachments/yuque/18/2019/key/289125/1555665208266-566cecd9-5015-4f7a-9a0a-9153405e64d1.key?_lake_card=%7B%22uid%22%3A%22rc-upload-1555665177546-4%22%2C%22src%22%3A%22https%3A%2F%2Fsouche.yuque.com%2Fattachments%2Fyuque%2F18%2F2019%2Fkey%2F289125%2F1555665208266-566cecd9-5015-4f7a-9a0a-9153405e64d1.key%22%2C%22name%22%3A%22Nest+%E5%88%86%E4%BA%AB.key%22%2C%22size%22%3A3369154%2C%22type%22%3A%22application%2Fx-iwork-keynote-sffkey%22%2C%22ext%22%3A%22key%22%2C%22progress%22%3A%7B%22percent%22%3A0%7D%2C%22status%22%3A%22done%22%2C%22percent%22%3A0%2C%22card%22%3A%22file%22%7D)
