# 🏗️ Project Structure

Sprawdzone struktury folderów dla projektów fullstack.

---

## ⚛️ Next.js (App Router)

```
my-app/
├── app/                          # App Router – routing oparty na plikach
│   ├── (auth)/                   # Route Group – bez wpływu na URL
│   │   ├── login/
│   │   │   └── page.tsx
│   │   └── register/
│   │       └── page.tsx
│   ├── (dashboard)/
│   │   ├── layout.tsx            # Shared layout dla dashboard
│   │   ├── page.tsx              # /dashboard
│   │   └── users/
│   │       ├── page.tsx          # /dashboard/users
│   │       └── [id]/
│   │           └── page.tsx      # /dashboard/users/:id
│   ├── api/                      # Route Handlers (API endpoints)
│   │   └── users/
│   │       └── route.ts
│   ├── layout.tsx                # Root layout
│   └── globals.css
│
├── components/
│   ├── ui/                       # Shadcn/ui – generowane komponenty bazowe
│   │   ├── button.tsx
│   │   └── dialog.tsx
│   ├── forms/                    # Formularze wielokrotnego użytku
│   │   ├── LoginForm.tsx
│   │   └── UserForm.tsx
│   └── shared/                   # Współdzielone komponenty aplikacji
│       ├── Header.tsx
│       ├── Sidebar.tsx
│       └── DataTable.tsx
│
├── lib/                          # Logika pomocnicza
│   ├── utils.ts                  # cn() i inne helpers
│   ├── validations.ts            # Zod schemas
│   └── constants.ts
│
├── hooks/                        # Custom React hooks
│   ├── useAuth.ts
│   └── useDebounce.ts
│
├── services/                     # Warstwa komunikacji z API
│   ├── userService.ts
│   └── api.ts                    # Bazowy klient HTTP
│
├── types/                        # Globalne typy TypeScript
│   ├── user.types.ts
│   └── api.types.ts
│
├── public/                       # Statyczne pliki
├── prisma/                       # Schemat bazy danych
│   ├── schema.prisma
│   └── migrations/
│
└── tests/
    └── e2e/                      # Testy Playwright
```

---

## 🖥️ Node.js API (Express / Fastify)

```
api/
├── src/
│   ├── app.ts                    # Konfiguracja aplikacji (bez listen)
│   ├── server.ts                 # Punkt wejścia – app.listen()
│   │
│   ├── modules/                  # Feature-based structure
│   │   ├── users/
│   │   │   ├── users.router.ts   # Definicje routów
│   │   │   ├── users.controller.ts  # Obsługa req/res
│   │   │   ├── users.service.ts  # Logika biznesowa
│   │   │   ├── users.repository.ts  # Zapytania do DB
│   │   │   ├── users.schema.ts   # Zod validation schemas
│   │   │   └── users.types.ts    # Typy modułu
│   │   └── auth/
│   │       ├── auth.router.ts
│   │       ├── auth.controller.ts
│   │       └── auth.service.ts
│   │
│   ├── middleware/
│   │   ├── authenticate.ts       # JWT verification
│   │   ├── authorize.ts          # Role-based access
│   │   ├── errorHandler.ts       # Global error handler
│   │   └── rateLimiter.ts
│   │
│   ├── lib/
│   │   ├── prisma.ts             # Singleton Prisma client
│   │   ├── redis.ts              # Redis client
│   │   └── logger.ts             # Winston/Pino logger
│   │
│   └── types/
│       └── express.d.ts          # Rozszerzenie req.user itp.
│
├── prisma/
├── tests/
│   ├── unit/
│   └── integration/
├── .env
├── .env.example
└── tsconfig.json
```

---

## 📐 Zasady Struktury

### Feature-based vs Layer-based

```
# ❌ Layer-based – trudny do skalowania
controllers/
  userController.ts
  orderController.ts
services/
  userService.ts
  orderService.ts
repositories/
  userRepository.ts
  orderRepository.ts

# ✅ Feature-based – wszystko dla jednego modułu razem
modules/
  users/
    users.controller.ts
    users.service.ts
    users.repository.ts
  orders/
    orders.controller.ts
    orders.service.ts
    orders.repository.ts
```

### Barrel Exports (`index.ts`)

```ts
// components/forms/index.ts
export { LoginForm } from "./LoginForm";
export { UserForm } from "./UserForm";

// Użycie:
import { LoginForm, UserForm } from "@/components/forms";
// zamiast:
import { LoginForm } from "@/components/forms/LoginForm";
```

> ⚠️ Barrel exports mogą pogorszyć tree-shaking i czasy cold-start w Next.js. Używaj rozważnie.

---

## 🗂️ Pliki Konfiguracyjne (root)

```
my-app/
├── .env                          # Lokalne sekrety (gitignore!)
├── .env.example                  # Szablon zmiennych (commituj!)
├── .gitignore
├── .eslintrc.json / eslint.config.ts
├── .prettierrc
├── next.config.ts
├── tailwind.config.ts
├── tsconfig.json
├── package.json
├── docker-compose.yml
└── README.md
```

---

## 📦 Monorepo (Turborepo)

```
my-monorepo/
├── apps/
│   ├── web/                      # Next.js frontend
│   └── api/                      # Node.js backend
│
├── packages/
│   ├── ui/                       # Współdzielone komponenty
│   ├── types/                    # Wspólne typy (frontend + backend)
│   ├── config/                   # ESLint, TS config bazowe
│   └── utils/                    # Współdzielone funkcje pomocnicze
│
├── turbo.json
└── package.json
```

---

## 💡 Tips

- **Trzymaj pliki testów blisko kodu** – `UserForm.test.tsx` obok `UserForm.tsx`
- **`index.ts` w folderze komponentu** – jeden import zamiast długiej ścieżki
- **Nie zagnieżdżaj za głęboko** – max 4 poziomy to dobra granica
- **`lib/` vs `utils/`** – `lib/` dla integracji zewnętrznych (prisma, redis), `utils/` dla czystych funkcji pomocniczych
- **`.env.example`** zawsze commituj do repo – dokumentuje jakich zmiennych wymaga projekt
