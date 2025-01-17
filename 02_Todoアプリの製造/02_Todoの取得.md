# Todo の取得

初期データが準備できたので、Prisma を使用して Todo データを取得する簡単な API を作成します。
`src/index.ts`にデータの取得処理を記述していきます。

```ts
import { serve } from "@hono/node-server";
import { Hono } from "hono";
import { PrismaClient } from "@prisma/client";

// HonoとPrismaClientのインスタンスを作成
const app = new Hono();
const prisma = new PrismaClient();

app.get("/", (c) => {
	return c.text("Hello Hono!");
});

// 非同期処理でprismaのfindManyメソッドを使用
app.get("/todos", async (c) => {
	const todo = await prisma.todo.findMany();
	return c.json(todo);
});

// 指定されたIDに対応するTodoを取得
app.get("/todos/:id", async (c) => {
	const id = Number.parseInt(c.req.param("id"), 10); // IDを整数として取得
	const todo = await prisma.todo.findUnique({ where: { id } });
	if (!todo) return c.json({ error: "Todo not found" }, 404);
	return c.json(todo);
});
const port = 3000;
console.log(`Server is running on http://localhost:${port}`);

serve({
	fetch: app.fetch,
	port,
});
```

サーバーを起動し、Postman 等のツールを使用して、データが取得できるかテストしてみましょう。
