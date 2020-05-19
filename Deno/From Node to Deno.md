# 从 Node 到 Deno

![](https://tva1.sinaimg.cn/large/007S8ZIlly1gexm53yyjqj30qn0c0q4e.jpg)

> 原文地址: 《[From Node to Deno](https://aralroca.com/blog/from-node-to-deno)》

上周我发表了一篇关于 Deno 的文章: [如何用 Deno 和 Preact 创建一个聊天应用](https://aralroca.com/blog/learn-deno-chat-app)。很多疑问接踵而至。期中大部分都是如何用 Done 来做 Node 之前所做的事情。

所以我尝试整理一些 Node 常见问题的 Deno 替代方案。这非必要，很多模块可以重用。可以访问 [pika.dev](https://www.pika.dev/about)来寻找在 Deno 中使用的模块。

收集列表如何下：

- Electron
- Forever / PM2
- Express / Koa
- MongoDB
- PostgresSQL
- MySQL / MariaDB
- Redis
- Nodemon
- Jest, Jasmine, Ava...
- Webpack, Parcel, Rollup...
- Prettier
- NPM Scripts
- Nvm
- Npx
- Run on a Docker
- Run as a lambda
- Conclusion

## Electron

Electron 使用 Chromium 作为界面来运行 Web 环境。可以通过 Node.js + Electron 创建桌面应用程序。Deno 替代方案么？

![Electron](https://aralroca.com/images/blog-images/55.png)

好吧，现在，Electron远不能在Deno下执行。我们必须寻找替代方案。由于Deno是用Rust制作的，因此我们可以使用Web视图锈迹绑定在Deno中运行Destkop应用程序。

这样，我们可以使用本机OS Web视图来运行任意数量的Web视图。

回购：[https://github.com/eliassjogreen/deno_webview](https://github.com/eliassjogreen/deno_webview)

``` typescript
import { WebView } from "https://deno.land/x/webview/mod.ts";

const sharedOptions = {
  width: 400,
  height: 200,
  resizable: true,
  debug: true,
  frameless: false,
};

const webview1 = new WebView({
  title: "Multiple deno_webview example",
  url: `data:text/html,
    <html>
    <body>
      <h1>1</h1>
    </body>
    </html>
    `,
  ...sharedOptions,
});

const webview2 = new WebView({
  title: "Multiple deno_webview example",
  url: `data:text/html,
    <html>
    <body>
      <h1>2</h1>
    </body>
    </html>
    `,
  ...sharedOptions,
});

await Promise.all([webview1.run(), webview2.run()]);
```

![](https://tva1.sinaimg.cn/large/007S8ZIlly1gexmx17u6jj30m80eugm6.jpg)

## Forever / PM2

[Forever](https://github.com/foreversd/forever) 和 [PM2](https://github.com/Unitech/pm2) 是用于确保给定脚本作为守护程序连续运行的CLI工具。与Forever不同，PM2更完整，还可以用作负载平衡器。两者在 Node 中都非常有用，但是我们可以在Deno 中使用它们吗？

Forever 仅用于 Node，因此使用它是不可行的。另一方面，借助 PM2，我们可以运行非节点脚本，因此我们仍可以将其用于 Deno。

建立app.sh档案

```bash
#!/bin/bash
deno run -A myCode.ts
```

和

```bash
 pm2 start ./app.sh
```

![](https://tva1.sinaimg.cn/large/007S8ZIlly1gexn4dvlfuj30j60700sx.jpg)

## Express / Koa

[Express](https://github.com/expressjs/express) 和 [Koa](https://github.com/koajs/koa) 是最著名的Node框架。他们以其强大的路由系统和HTTP帮助器（重定向，缓存等）而闻名。我们可以在Deno中使用它们吗？答案不是...但是有一些替代方法。

## Http（标准库）

Deno自己的STD库已经满足Express或Koa提供的许多需求。[https://deno.land/std/http/](https://deno.land/std/http/)。

``` typescript
import { ServerRequest } from "https://deno.land/std/http/server.ts";
import { getCookies } from "https://deno.land/std/http/cookie.ts";

let request = new ServerRequest();
request.headers = new Headers();
request.headers.set("Cookie", "full=of; tasty=chocolate");

const cookies = getCookies(request);
console.log("cookies:", cookies);
```

但是，声明路线的方法并不是很吸引人。因此，让我们看看更多替代方案。

## Oak (第三方库)

受Koa启发，这是目前最优雅的解决方案之一。[https://github.com/oakserver/oak](https://github.com/oakserver/oak)

```typescript
import { Application,  } from "https://deno.land/x/oak/mod.ts";

const app = new Application();

app.use((ctx) => {
  ctx.response.body = "Hello World!";
});

await app.listen({ port: 8000 });
```

## Abc (第三方库)

类似于 `Oak`。[https://deno.land/x/abc](https://deno.land/x/abc)。

``` typescript
import { Application } from "https://deno.land/x/abc/mod.ts";

const app = new Application();

app.static("/static", "assets");

app.get("/hello", (c) => "Hello!")
  .start({ port: 8080 });
```

## Deno-Express（第三方lib）

``` typescript
import * as exp from "https://raw.githubusercontent.com/NMathar/deno-express/master/mod.ts";

const port = 3000;
const app = new exp.App();

app.use(exp.static_("./public"));
app.use(exp.bodyParser.json());

app.get("/api/todos", async (req, res) => {
  await res.json([{ name: "Buy some milk" }]);
});

const server = await app.listen(port);
console.log(`app listening on port ${server.port}`);
```

## MongoDB

[MongoDB](https://github.com/mongodb/mongo) 是具有巨大的可扩展性和灵活性的文档数据库。在JavaScript生态系统中已被广泛使用，使用它的许多堆栈（如MEAN或MERN）。非常受欢迎

```typescript
import { init, MongoClient } from "https://deno.land/x/mongo@v0.6.0/mod.ts";

// Initialize the plugin
await init();

const client = new MongoClient();
client.connectWithUri("mongodb://localhost:27017");

const db = client.database("test");
const users = db.collection("users");

// insert
const insertId = await users.insertOne({
  username: "user1",
  password: "pass1"
});

// findOne
const user1 = await users.findOne({ _id: insertId });

// find
const users = await users.find({ username: { $ne: null } });

// aggregation
const docs = await users.aggregation([
  { $match: { username: "many" } },
  { $group: { _id: "$username", total: { $sum: 1 } } }
]);

// updateOne
const { matchedCount, modifiedCount, upsertedId } = await users.updateOne(
  username: { $ne: null },
  { $set: { username: "USERNAME" } }
);

// deleteOne
const deleteCount = await users.deleteOne({ _id: insertId });
```

## PostgresSQL

与MongoDB一样，[PostgresSQL](https://github.com/postgres/postgres/) 也有一个驱动程序。

[https://github.com/buildondata/deno-postgres。](https://github.com/buildondata/deno-postgres。)

```typescript
import { Client } from "https://deno.land/x/postgres/mod.ts";

const client = new Client({
  user: "user",
  database: "test",
  hostname: "localhost",
  port: 5432
});
await client.connect();
const result = await client.query("SELECT * FROM people;");
console.log(result.rows);
await client.end();
```

## MySQL / MariaDB

与`MongoDB`和`PostgresSQL`一样，还有[MySQL](https://github.com/mysqljs/mysql)/[MariaDB](https://github.com/mariadb-corporation/mariadb-connector-nodejs)的驱动程序。

[https://github.com/manyuanrong/deno_mysql](https://github.com/manyuanrong/deno_mysql)

```typescript
import { Client } from "https://deno.land/x/mysql/mod.ts";

const client = await new Client().connect({
  hostname: "127.0.0.1",
  username: "root",
  db: "dbname",
  poolSize: 3, // connection limit
  password: "password",
});

let result = await client.execute(`INSERT INTO users(name) values(?)`, [
  "aralroca",
]);
console.log(result);
// { affectedRows: 1, lastInsertId: 1 }
```

## Redis

[Redis](https://github.com/NodeRedis/node-redis) 是最著名的缓存数据库，它也具有Deno驱动程序。

[https://github.com/keroxp/deno-redis](https://github.com/keroxp/deno-redis)

```typescript
import { connect } from "https://denopkg.com/keroxp/deno-redis/mod.ts";

const redis = await connect({
  hostname: "127.0.0.1",
  port: 6379
});
const ok = await redis.set("example", "this is an example");
const example = await redis.get("example");
```

## Nodemon

[Nodemon](https://github.com/remy/nodemon) 在开发环境中用于监视文件中的任何更改，从而自动重新启动服务器。这使节点开发更加有趣，而无需手动停止和重新启动服务器以查看应用的更改。可以在Deno中使用吗？

抱歉，您不能...但是仍然有另一种选择：Denon。

[https://github.com/eliassjogreen/denon](https://github.com/eliassjogreen/denon)

我们可以像`deno run`执行脚本一样使用`Denon`。

```bash
➜ denon server.ts
```

## Jest, Jasmine, Ava...

![](https://tva1.sinaimg.cn/large/007S8ZIlly1gexswm785fj30dw05kwep.jpg)

在`Node.js`生态系统中，有许多替代品可供测试跑步者使用。但是，没有一种官方方法可以测试`Node.js`代码。

在Deno中，有一种官方方法，您可以使用测试std库。

[https://deno.land/std/testing](https://deno.land/std/testing)

```typescript
import { assertStrictEq } from 'https://deno.land/std/testing/asserts.ts'

Deno.test('My first test', async () => {
  assertStrictEq(true, false)
})
```

要运行测试：

```bash
➜  deno test
```

## Webpack, Parcel, Rollup...

![](https://tva1.sinaimg.cn/large/007S8ZIlly1gexsz3vhemj30cf04u74e.jpg)

Deno的优势之一是我们可以将ESmodules与TypeScript一起使用，而无需诸如[Webpack](https://github.com/webpack/webpack)，[Parcel](https://github.com/parcel-bundler/parcel)或[Rollup](https://github.com/rollup/rollup)之类的捆绑器。

但是，您可能想知道是否给定了一棵文件树，我们可以制作一个捆绑包，将所有内容放到一个文件中以在网络上运行它。

好吧，有可能，是的。我们可以使用Deno的CLI做到这一点。因此，不需要第三方捆绑程序。

```bash
➜ deno bundle myLib.ts myLib.bundle.js
```

现在可以将其加载到浏览器中了：

```html
<script type="module">
  import * as myLib from "myLib.bundle.js";
</script>
```

## Prettier

![](https://tva1.sinaimg.cn/large/007S8ZIlly1gext2kv18hj3046046dfm.jpg)

在过去的几年中，[Prettier](https://prettier.io/)在JavaScript生态系统中已广为人知，因为有了它，您不必担心格式化文件。

事实是，它仍然可以在Deno上使用，但是失去了意义，因为Deno有自己的格式化程序。

您可以使用以下命令格式化文件：

```bash
➜  deno fmt
```

## NPM Scripts

![](https://tva1.sinaimg.cn/large/007S8ZIlly1gext47qcsmj305k00wt8h.jpg)

使用Deno，`package.json`不再存在。我真正想念的一件事是在中声明的脚本`package.json`。

一个简单的解决方案是使用`makefile`和一起执行`make`。但是，如果您错过了npm语法，那么Deno会有一个npm样式的脚本运行器：

[https://github.com/umbopepato/velociraptor](https://github.com/umbopepato/velociraptor)

您可以使用脚本定义文件：

```yaml
# scripts.yaml
scripts:
  start: deno run --allow-net server.ts
  test: deno test --allow-net server_test.ts
```

执行：

```bash
➜  vr run <SCRIPT>
```

另一个替代品是[denox](https://github.com/BentoumiTech/denox)，与Velociraptor非常相似。

Nvm

[Nvm](https://github.com/nvm-sh/nvm)是一个CLI，用于管理多个活动Node版本，以便根据项目轻松升级或降级版本。

nvm在Deno中的等效项是dvm。

[https://github.com/axetroy/dvm](https://github.com/axetroy/dvm)

```bash
➜  dvm use 1.0.0
```

## Npx

[NPX](https://github.com/npm/npx)在最近几年已经变得非常流行，而无需安装它们执行NPM包。现在，由于Deno是一个独立的生态系统，因此npm内将不存在许多项目。那么，我们如何执行Deno模块而不必将其安装`deno install https://url-of-module.ts`

以与运行项目相同的方式，而不是文件，放置模块的URL：

```bash
➜  deno run https://deno.land/std/examples/welcome.ts
```

如您所见，不仅我们必须记住模块的名称，而且还要记住整个URL，这使它的使用更加困难。另一方面，它提供了更大的灵活性，因为我们可以运行任何文件，而不仅仅是在package.jsonlike中指定为二进制文件npx。

## 在Docker上运行

要在Docker内部运行Deno，我们可以创建以下Dockerfile：

```
FROM hayd/alpine-deno:1.0.0

EXPOSE 1993  # Port.

WORKDIR /app

USER deno

COPY deps.ts .
RUN deno cache deps.ts # Cache the deps

ADD . .
RUN deno cache main.ts # main entrypoint.

CMD ["--allow-net", "main.ts"]
```

要构建并运行它：

```bash
➜  docker build -t app . && docker run -it --init -p 1993:1993 app
```

Repo: [https://github.com/hayd/deno-docker](https://github.com/hayd/deno-docker)

## Run as a lambda

要将Deno用作lambda，Deno STD库中有一个模块。[https://deno.land/x/lambda](https://deno.land/x/lambda)。

```typescript
import {
  APIGatewayProxyEvent,
  APIGatewayProxyResult,
  Context
} from "https://deno.land/x/lambda/mod.ts";

export async function handler(
  event: APIGatewayProxyEvent,
  context: Context
): Promise<APIGatewayProxyResult> {
  return {
    body: `Welcome to deno ${Deno.version.deno} 🦕`,
    headers: { "content-type": "text/html;charset=utf8" },
    statusCode: 200
  };
}
```

## 参考

- Vercel中的Deno：[https://github.com/lucacasonato/now-deno](https://github.com/lucacasonato/now-deno)
- AWS中的Deno：[https://blog.begin.com/deno-runtime-support-for-architect-805fcbaa82c3](https://blog.begin.com/deno-runtime-support-for-architect-805fcbaa82c3)

## 小结

我确定我忘记了一些Node主题以及它们的Deno替代方案，让我知道您想让我解释的内容是否有所遗漏。我希望本文能帮助您与Deno一起破冰。

要探索可与Deno一起使用的所有库：

- [https://deno.land/std](https://deno.land/std)
- [https://deno.land/x](https://deno.land/x)
- [https://www.pika.dev/](https://www.pika.dev/)
