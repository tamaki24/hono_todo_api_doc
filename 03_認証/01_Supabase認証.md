つぎに [Supabase Auth](https://supabase.com/docs/guides/auth) を使用して認証を実装していきます。
Supabase で提供されているユーザー認証の方法には 4 通りあり、今回は E メールとパスワードによる認証を使用します。

クリーンアーキテクチャを保ちながら、リポジトリとユースケースを実装していきましょう。

src/domain/repositories/IAuthRepository.ts

```ts
import type { User, Session } from "@supabase/supabase-js/dist/main/index.js";

export interface IAuthRepository {
	signUp(email: string, password: string): Promise<{ user: User }>;
	signIn(email: string, password: string): Promise<{ session: Session }>;
	signOut(): Promise<void>;
}
```

src/infrastructure/supabase/repositories/authRepository.ts

```ts
import type { Session, User } from "@supabase/auth-js";
import type { IAuthRepository } from "../../../domain/repositories/IAuthRepository.js";
import { supabase } from "../supabase.js";

export class AuthRepository implements IAuthRepository {
	async signUp(email: string, password: string): Promise<{ user: User }> {
		const { data, error } = await supabase.auth.signUp({
			email,
			password,
		});
		if (error) {
			throw new Error(error.message);
		}

		if (!data.user) {
			throw new Error("An unexpected error occurred");
		}

		return { user: data.user };
	}

	async signIn(email: string, password: string): Promise<{ session: Session }> {
		const { data, error } = await supabase.auth.signInWithPassword({
			email,
			password,
		});
		if (error) {
			throw new Error(error.message);
		}
		return { session: data.session };
	}

	async signOut(): Promise<void> {
		const { error } = await supabase.auth.signOut();
		if (error) {
			throw new Error(error.message);
		}
	}
}
```

src/application/auth/authUseCase.ts

```ts
import type { IAuthRepository } from "../../domain/repositories/IAuthRepository.js";

export class AuthUseCase {
	private authRepository: IAuthRepository;

	constructor(authRepository: IAuthRepository) {
		this.authRepository = authRepository;
	}

	async signUp(email: string, password: string) {
		return this.authRepository.signUp(email, password);
	}

	async signIn(email: string, password: string) {
		return this.authRepository.signIn(email, password);
	}

	async signOut() {
		return this.authRepository.signOut();
	}
}
```

`src/interface/routes/authRoute.ts`に認証のルーティングを定義します。

```ts
import { Hono } from "hono";
import { AuthRepository } from "../../infrastructure/supabase/repositories/authRepository.js";
import { AuthUseCase } from "../../application/auth/authUseCase.js";

const app = new Hono();

const authRepository = new AuthRepository();
const authUseCase = new AuthUseCase(authRepository);

app.post("/signin", async (c) => {
	const { email, password } = await c.req.json();

	try {
		const session = await authUseCase.signIn(email, password);
		return c.json({ session });
	} catch (error) {
		if (error instanceof Error) {
			return c.json({ error: error.message }, 400);
		}
		return c.json({ error: "An unexpected error occurred" }, 500);
	}
});

// サインアップ
app.post("/signup", async (c) => {
	const { email, password } = await c.req.json();

	try {
		const { user } = await authUseCase.signUp(email, password);
		return c.json({ user });
	} catch (error) {
		if (error instanceof Error) {
			return c.json({ error: error.message }, 400);
		}
		return c.json({ error: "An unexpected error occurred" }, 500);
	}
});

// ログアウト
app.post("/logout", async (c) => {
	const token = c.req.header("Authorization")?.replace("Bearer ", "");

	if (!token) {
		return c.json({ error: "Authorization token is missing" }, 401);
	}

	try {
		await authUseCase.signOut().then(() => {
			return c.json({ message: "Logged out successfully" });
		});
	} catch (error) {
		if (error instanceof Error) {
			return c.json({ error: error.message }, 400);
		}
		return c.json({ error: "An unexpected error occurred" }, 500);
	}
});

export default app;
```

最後に、src/index.ts で`/auth`にルーティングを設定します

```ts
+ import authRoutes from "./interface/routes/authRoute.js";

+ app.route("/auth", authRoutes);
```
