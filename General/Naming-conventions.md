# 📛 Naming Conventions

Spójne nazewnictwo w całym projekcie – zmienne, pliki, bazy danych i więcej.

---

## 🟦 TypeScript / JavaScript

### Zmienne i Funkcje – `camelCase`

```ts
// Zmienne
const userName = 'Jan'
const isLoading = false
const hasPermission = true       // boolean: is/has/can/should
const userCount = 42

// Funkcje
function fetchUserById(id: string) { ... }
function calculateTotalPrice(items: CartItem[]) { ... }
const handleSubmit = () => { ... }  // event handler: handle + zdarzenie
const formatDate = (date: Date) => { ... }
```

### Klasy i Typy – `PascalCase`

```ts
class UserService { ... }
class ApiClient { ... }

type UserId = string
type CreateUserDTO = { name: string; email: string }

interface UserRepository { ... }
interface ApiResponse<T> { data: T; status: number }
```

### Stałe – `SCREAMING_SNAKE_CASE`

```ts
const MAX_RETRY_COUNT = 3;
const API_BASE_URL = "https://api.example.com";
const DEFAULT_PAGE_SIZE = 20;

// Wyjątek: obiekty konfiguracyjne używają camelCase
const defaultConfig = { timeout: 5000, retries: 3 };
```

### Enumy

```ts
// Wartości PascalCase (zalecane w TS)
enum UserRole {
  Admin = "Admin",
  Moderator = "Moderator",
  User = "User",
}

// Alternatywa: const object (lepszy tree-shaking)
const USER_ROLE = {
  Admin: "Admin",
  Moderator: "Moderator",
  User: "User",
} as const;
type UserRole = (typeof USER_ROLE)[keyof typeof USER_ROLE];
```

---

## ⚛️ React

### Komponenty – `PascalCase`

```tsx
// Pliki i nazwy komponentów zawsze PascalCase
UserProfile.tsx       → function UserProfile() { ... }
AuthGuard.tsx         → function AuthGuard() { ... }
ProductCard.tsx       → function ProductCard() { ... }

// Props interface: NazwaKomponentu + Props
interface UserProfileProps {
  userId: string
  onEdit: () => void
}
```

### Hooki – `use` + `camelCase`

```ts
useAuth.ts            → function useAuth() { ... }
useLocalStorage.ts    → function useLocalStorage() { ... }
useDebounce.ts        → function useDebounce() { ... }
```

### Pliki i Foldery

```
components/
  UserProfile/
    UserProfile.tsx       ← komponent
    UserProfile.test.tsx  ← test przy komponencie
    UserProfile.types.ts  ← typy (jeśli duże)
    index.ts              ← re-export: export { UserProfile } from './UserProfile'

hooks/
  useAuth.ts
  useLocalStorage.ts

lib/
  utils.ts                ← funkcje pomocnicze
  validators.ts

types/
  user.types.ts
  api.types.ts
```

### Event Handlery

```tsx
// Prefiks: handle (definicja) / on (prop)
const handleClick = () => { ... }
const handleSubmit = (e: FormEvent) => { ... }
const handleUserDelete = (id: string) => { ... }

<Button onClick={handleClick}>
<UserCard onDelete={handleUserDelete}>  // prop = on + zdarzenie
```

---

## 📁 Pliki i Foldery

```
Komponenty React:       PascalCase       UserProfile.tsx
Hooki:                  camelCase        useAuth.ts
Serwisy / utils:        camelCase        userService.ts, formatDate.ts
Konfiguracja:           kebab-case       tailwind.config.ts, next.config.ts
Strony (Next.js):       kebab-case       /user-profile, /order-history
Testy:                  jak oryginał     UserProfile.test.tsx, userService.spec.ts
Typy:                   camelCase        user.types.ts, api.types.ts
Stałe:                  camelCase        routes.ts, config.ts
```

---

## 🗄️ Bazy Danych

### Tabele i Kolumny – `snake_case`

```sql
-- Tabele: liczba mnoga, snake_case
CREATE TABLE users ( ... )
CREATE TABLE order_items ( ... )
CREATE TABLE refresh_tokens ( ... )

-- Kolumny: snake_case
id              UUID PRIMARY KEY
user_id         UUID REFERENCES users(id)
first_name      VARCHAR(100)
is_active       BOOLEAN DEFAULT true
created_at      TIMESTAMP DEFAULT NOW()
updated_at      TIMESTAMP
deleted_at      TIMESTAMP    -- soft delete
```

### Prisma Schema

```prisma
// Model: PascalCase (singular)
model User {
  id        String   @id @default(cuid())
  firstName String            // camelCase w Prisma → snake_case w DB via @map
  lastName  String
  isActive  Boolean  @default(true)
  createdAt DateTime @default(now())
  updatedAt DateTime @updatedAt

  orders    Order[]           // relacja: camelCase, liczba mnoga

  @@map("users")              // nazwa tabeli w DB
}
```

### Indeksy i Klucze Obce

```sql
-- Indeksy: idx_tabela_kolumna
CREATE INDEX idx_users_email ON users(email);
CREATE INDEX idx_orders_user_id ON orders(user_id);

-- Klucze obce: fk_tabela_referowana
CONSTRAINT fk_orders_user FOREIGN KEY (user_id) REFERENCES users(id)
```

---

## 🌐 API – REST Endpoints

```
Zasoby: rzeczowniki, liczba mnoga, kebab-case

GET    /api/users                  lista użytkowników
GET    /api/users/:id              pojedynczy użytkownik
POST   /api/users                  utwórz użytkownika
PATCH  /api/users/:id              aktualizuj (częściowo)
PUT    /api/users/:id              zastąp (całkowicie)
DELETE /api/users/:id              usuń

GET    /api/users/:id/orders       zagnieżdżony zasób
GET    /api/order-items            kebab-case dla multi-word

# Akcje (gdy nie pasuje CRUD):
POST   /api/users/:id/activate
POST   /api/auth/refresh-token
POST   /api/payments/:id/refund
```

### Query Params

```
?page=1&limit=20                   paginacja
?sort=created_at&order=desc        sortowanie
?filter[status]=active             filtrowanie
?search=jan                        wyszukiwanie
?include=orders,profile            eager loading
```

---

## 🌿 Git – Branche

```
Formaty:
  type/opis-w-kebab-case

Typy:
  feat/       → nowa funkcja
  fix/        → naprawa buga
  refactor/   → refactoring bez zmiany funkcji
  chore/      → konfiguracja, dependencje
  docs/       → dokumentacja

Przykłady:
  feat/user-authentication
  feat/payment-integration
  fix/login-redirect-loop
  fix/cart-total-calculation
  refactor/user-service-cleanup
  chore/update-dependencies
  docs/api-endpoints
```

---

## 🔑 Zmienne Środowiskowe

```bash
# Format: SCREAMING_SNAKE_CASE
# Prefiks według kontekstu

# Baza danych
DATABASE_URL=
DATABASE_HOST=
DATABASE_PORT=

# Auth
JWT_SECRET=
JWT_EXPIRES_IN=

# Zewnętrzne API
STRIPE_SECRET_KEY=
STRIPE_WEBHOOK_SECRET=
RESEND_API_KEY=

# Aplikacja
NEXT_PUBLIC_APP_URL=      # NEXT_PUBLIC_ → dostępne w przeglądarce
NODE_ENV=                 # development | production | test
PORT=

# Prefiks według środowiska (rzadziej, częściej osobne pliki .env)
PROD_DATABASE_URL=
DEV_DATABASE_URL=
```

---

## 💡 Tips

- **Booleans** zawsze zaczynaj od `is`, `has`, `can`, `should`: `isLoading`, `hasError`, `canEdit`
- **Unikaj skrótów** – `usr` zamiast `user` to oszczędność 2 znaków za cenę czytelności
- **Bądź spójny** – zły styl w całym projekcie jest lepszy niż mieszanina różnych stylów
- **Indeksy tablicy** – `i, j` są ok w krótkich pętlach, ale `userIndex` jest czytelniejsze
- **Negatywne nazwy** sprawiają problemy: `isNotValid` → `isInvalid`, `!isDisabled` → `isEnabled`
