# 🔧 TypeScript – Utility Types

Wbudowane typy pomocnicze które eliminują powtarzanie kodu.

---

## 🏗️ Podstawowe Utility Types

### `Partial<T>` – wszystkie pola opcjonalne

```ts
interface User { id: string; name: string; email: string }

// Użycie: update payload – nie musisz podawać wszystkich pól
function updateUser(id: string, data: Partial<User>) { ... }
updateUser('1', { name: 'Jan' })  // ✅
```

### `Required<T>` – wszystkie pola wymagane

```ts
interface Config {
  host?: string;
  port?: number;
}
type StrictConfig = Required<Config>;
// { host: string; port: number }
```

### `Readonly<T>` – zablokuj mutację

```ts
const config: Readonly<Config> = { host: "localhost", port: 3000 };
config.host = "other"; // ❌ Error: Cannot assign to 'host'
```

### `Pick<T, Keys>` – wybierz tylko wybrane pola

```ts
type UserPreview = Pick<User, 'id' | 'name'>
// { id: string; name: string }

// Użycie: DTO dla listy – nie zwracaj hasła/tokenu
function getUsers(): UserPreview[] { ... }
```

### `Omit<T, Keys>` – usuń wybrane pola

```ts
type CreateUserDTO = Omit<User, "id" | "createdAt">;
// Użycie: payload do tworzenia – backend sam generuje id

type UpdateUserDTO = Partial<Omit<User, "id">>;
// Wszystkie pola opcjonalne, ale bez id
```

---

## 🔀 Typy dla Unii

### `Record<Keys, Value>` – mapa klucz-wartość

```ts
type Role = "admin" | "user" | "moderator";
const permissions: Record<Role, string[]> = {
  admin: ["read", "write", "delete"],
  user: ["read"],
  moderator: ["read", "write"],
};
```

### `Exclude<T, U>` – usuń z unii

```ts
type Status = "active" | "inactive" | "deleted";
type VisibleStatus = Exclude<Status, "deleted">;
// 'active' | 'inactive'
```

### `Extract<T, U>` – wyciągnij z unii pasujące

```ts
type HttpMethod = "GET" | "POST" | "PUT" | "DELETE" | "PATCH";
type SafeMethods = Extract<HttpMethod, "GET">;
// 'GET'
```

### `NonNullable<T>` – usuń null i undefined

```ts
type MaybeString = string | null | undefined;
type DefinitelyString = NonNullable<MaybeString>;
// string
```

---

## ⚙️ Typy dla Funkcji

### `ReturnType<T>` – typ zwracany przez funkcję

```ts
function createUser() {
  return { id: "1", name: "Jan", createdAt: new Date() };
}
type User = ReturnType<typeof createUser>;
// Automatycznie: { id: string; name: string; createdAt: Date }
```

### `Parameters<T>` – typy parametrów funkcji

```ts
function fetchData(url: string, options: RequestInit) { ... }
type FetchParams = Parameters<typeof fetchData>
// [url: string, options: RequestInit]
```

### `Awaited<T>` – typ po rozwinięciu Promise

```ts
async function fetchUser(): Promise<User> { ... }
type FetchedUser = Awaited<ReturnType<typeof fetchUser>>
// User (nie Promise<User>)
```

---

## 🔨 Własne Utility Types (przepisy)

### DeepPartial – opcjonalne zagnieżdżone obiekty

```ts
type DeepPartial<T> = {
  [P in keyof T]?: T[P] extends object ? DeepPartial<T[P]> : T[P];
};
```

### Nullable – dodaj null do każdego pola

```ts
type Nullable<T> = T | null;
const user: Nullable<User> = null; // czytelniejsze niż User | null
```

### WithId – dodaj id do dowolnego typu

```ts
type WithId<T> = T & { id: string };
type UserWithId = WithId<CreateUserDTO>;
```

### ValueOf – typ wartości obiektu

```ts
const ROUTES = { home: "/", about: "/about", users: "/users" } as const;
type Route = (typeof ROUTES)[keyof typeof ROUTES];
// '/' | '/about' | '/users'
```

---

## 🎯 Praktyczne Kombinacje

```ts
// DTO pattern – bezpieczne typy dla API
interface User {
  id: string;
  name: string;
  password: string;
  createdAt: Date;
}

type CreateUserDTO = Omit<User, "id" | "createdAt">; // wejście
type UpdateUserDTO = Partial<Omit<User, "id" | "password">>; // patch
type UserResponse = Omit<User, "password">; // wyjście (bez hasła)

// Prisma-style select
type UserSelect = Partial<Record<keyof User, boolean>>;
```

---

## 💡 Tips

- `Omit` + `Partial` to najczęstsza kombinacja przy DTO
- `ReturnType<typeof fn>` to lepsze niż ręczne definiowanie – zawsze aktualne
- `as const` + `keyof typeof` = enum bez enumów (tree-shakable)
- Unikaj `any` – jeśli musisz: `unknown` + type guard zamiast `any`
