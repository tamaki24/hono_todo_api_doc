# Supabase と Prisma のセットアップ

Supabase と Prisma の初期設定を行います。

## Supabase クライアントの作成

まずは Supabase のアカウントを作成して、`.env`に環境変数を定義します。
アカウントの作成や接続情報の表示場所については、以下のリンクを参考に進めてください。

[Use Supabase with Hono](https://supabase.com/docs/guides/getting-started/quickstarts/hono)
[Hono × Supabase × Prisma で爆速 Server 環境構築](https://zenn.dev/manase/articles/2e0067a7a9f715)

```env
DATABASE_URL=""
DIRECT_URL=""
SUPABASE_URL=""
SUPABASE_ANON_KEY=""
```

次にアプリケーションから Supabase に接続するためのクライアントを作成します。
今回はクリーンアーキテクチャに則ってアプリケーションを作成していくため、まずは`src/infrastructure/supabase/`のようにディレクトリを作成して、`supabase.ts`に内容を記述していきます。

```ts
import { createClient } from "@supabase/supabase-js/dist/main/index.js";

if (!process.env.SUPABASE_URL) {
  throw new Error("SUPABASE_URL is not defined");
}
const supabaseUrl = process.env.SUPABASE_URL;

if (!process.env.SUPABASE_ANON_KEY) {
  throw new Error("SUPABASE_ANON_KEY is not defined");
}
const supabaseKey = process.env.SUPABASE_ANON_KEY;

export const supabase = createClient(supabaseUrl, supabaseKey);
```

環境変数から Supabase の接続情報を取得し、エラーがあれば例外をスローします。

## Prisma のセットアップ

Prisma 環境の初期化を行うためのコマンドを実行します。

```shell
npx prisma init
```

これにより`prisma/`ディレクトリが作成されるので、prisma/schema.prisma にクライアント情報と Prisma スキーマ(Entity)を定義します。

> [!TIP]
> prisma は src と同階層で独立します。
> データベース関連の設定とマイグレーションを独立して管理することができるため、アプリケーションのコアロジックに影響を与えることがなくなるためです。

```ts
generator client {
  provider        = "prisma-client-js"
  previewFeatures = ["driverAdapters"]
  engineType      = "binary"
}

datasource db {
  provider  = "postgresql"
  url       = env("DATABASE_URL")
  directUrl = env("DIRECT_URL")
}

// Todo Entity
model Todo {
  id        Int      @id @default(autoincrement())
  title     String
  completed Boolean  @default(false)
  createdAt DateTime @default(now())
  updatedAt DateTime @updatedAt
}
```

### マイグレーションの実行

以下のコマンドを実行して、スキーマをデータベースに適用します。

```shell
npx prisma migrate dev --name init
```

成功すれば、Supabase に、定義した Todo テーブルができているはずなので、Supabase の Table Editor から確認してください。

また、コマンド`npx prisma studio`を実行することで、データベースを操作するための GUI(localhost:5555)が起動するので、そちらで確認することもできます。

以降はどちらを使用してデータの確認を行なっても構いません。
