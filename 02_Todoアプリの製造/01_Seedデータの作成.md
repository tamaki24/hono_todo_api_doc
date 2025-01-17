# Seed データを作成

Prisma の機能を使って、開発用の初期データを準備しましょう。

Prisma では、Seed データをスクリプトとして記述し、データベースへデータを投入することが出来ます。以下のように `prisma/seed.ts` を記述します。

```ts
import { type Prisma, PrismaClient } from "@prisma/client";

const prisma = new PrismaClient();

// サンプルデータを生成
const todoData: Prisma.TodoCreateInput[] = [...Array(3)].map((_, i) => ({
  title: `SampleTask${i + 1}`,
}));

const main = async () => {
  console.log(todoData);
  const result = await prisma.todo.createMany({
    data: todoData,
    skipDuplicates: true, // 重複データの投入をスキップ
  });
  console.log({ result });
};

// スクリプトの実行とエラー処理
main()
  .catch((e) => {
    console.error(e);
    process.exit(1);
  })
  .finally(async () => {
    await prisma.$disconnect(); // Prisma クライアントの接続を切断
  });
```

次に package.json の script に Seed スクリプトを実行するコマンドを追記します。

```json
{
  "scripts": {
    "seed": "tsx prisma/seed.ts"
  }
}
```

最後にコマンドを実行してデータが投入されることを確認します。

```shell
npm run seed
npx prisma studio
```

![スクリーンショット 2025-01-17 16 05 58](https://github.com/user-attachments/assets/2cf83145-2d9a-4ada-9e3b-d7605f4821c9)

