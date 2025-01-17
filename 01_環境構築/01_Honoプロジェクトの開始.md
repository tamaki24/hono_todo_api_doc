# Hono

[Hono](https://hono.dev)は軽量かつ高速で、Web 標準に基づいて構成されている TypeScript のフレームワークです。

## Hono プロジェクトの開始

以下のコマンドを実行して Hono のセットアップを行いましょう。

```shell
npm create hono@latest
```

いくつかの質問に答えて、プロジェクトを開始します。

```shell
> npx
> create-hono

create-hono version 0.14.3
? Target directory hono_todo
? Which template do you want to use? nodejs
? Do you want to install project dependencies? yes
? Which package manager do you want to use? npm
✔ Cloning the template
✔ Installing project dependencies
🎉 Copied project files
Get started with: cd hono_todo
```

## サーバーの起動

プロジェクトのディレクトリに移動し、コマンドを実行してサーバーを起動してみます。

```shell
npm run dev
```

localhost:3000 にアクセスして、Hello Hono!と表示されたら Hono のセットアップは完了です。
