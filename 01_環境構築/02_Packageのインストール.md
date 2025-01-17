# パッケージのインストール

今回使用するパッケージをインストールしていきます。

## Prisma

[Prisma](https://www.prisma.io)は型安全なデータベースアクセスを提供する ORM です。
TypeScript と相性がよく、スキーマ駆動型のデータベース操作が可能になります。

```shell
# prisma
npm install @prisma/client
npm install -D prisma
```

> [!TIP]
> -D は開発環境用インストールを意味します

## Zod

[Zod](https://zod.dev)は TypeScirpt 向けのスキーマ宣言とデータ検証のためのライブラリです。
データ構造を定義し、それに基づいてバリデーションを行うことができます。

```shell
#zod
npm install zod @hono/zod-validator
```

## Supabase

[Supabase](https://supabase.com)は PostgreSQL を基盤としたリアルタイムデータベースの他、アプリケーション開発に必要な機能を包括的に提供するフルスタックバックエンドサービスです。

今回作成するアプリケーションでは DB と認証を使用します。

```shell
# supabase
npm install @supabase/supabase-js
```
