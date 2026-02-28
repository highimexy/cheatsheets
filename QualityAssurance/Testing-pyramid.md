# 🔺 Testing Pyramid

Strategia testowania – ile i jakich testów pisać dla optymalnego pokrycia.

---

## 🏗️ Piramida Testów

```
           /\
          /E2E\          ← Mało (5-10%)
         /______\           Playwright, Cypress
        /        \
       / Integracja\     ← Średnio (20-30%)
      /______________\      Supertest, MSW, Testing Library
     /                \
    /   Jednostkowe    \  ← Najwięcej (60-70%)
   /____________________\  Jest, Vitest
```

| Poziom       | Szybkość             | Koszt utrzymania | Co testuje             |
| ------------ | -------------------- | ---------------- | ---------------------- |
| Jednostkowe  | ⚡ Błyskawiczne (ms) | Niski            | Funkcja, hook, klasa   |
| Integracyjne | 🐇 Szybkie (sekundy) | Średni           | Moduły razem, API      |
| E2E          | 🐢 Wolne (minuty)    | Wysoki           | Pełny flow użytkownika |

---

## 🧱 Poziom 1 – Testy Jednostkowe

**Co testować:** czyste funkcje, utility functions, custom hooks, logika biznesowa, transformacje danych.

```ts
// ✅ Dobre kandydaty do testów jednostkowych
function calculateDiscount(price: number, percent: number): number {
  if (percent < 0 || percent > 100) throw new Error("Invalid percent");
  return price * (1 - percent / 100);
}

describe("calculateDiscount", () => {
  it("applies discount correctly", () => {
    expect(calculateDiscount(100, 20)).toBe(80);
  });

  it("throws on invalid percent", () => {
    expect(() => calculateDiscount(100, -5)).toThrow("Invalid percent");
    expect(() => calculateDiscount(100, 150)).toThrow("Invalid percent");
  });

  it("handles 0% discount", () => {
    expect(calculateDiscount(100, 0)).toBe(100);
  });
});
```

**Zasady:**

- Jeden test = jedna odpowiedzialność
- Testuj edge cases: `null`, `0`, pusty string, pusta tablica
- Testuj ścieżki błędów (throw, reject)
- Mocku zewnętrznych zależności (API, baza danych)

---

## 🔗 Poziom 2 – Testy Integracyjne

**Co testować:** komponenty React z interakcjami, endpointy API, integracja z bazą danych.

### Frontend – React Testing Library

```tsx
import { render, screen, waitFor } from "@testing-library/react";
import userEvent from "@testing-library/user-event";
import { UserForm } from "./UserForm";

describe("UserForm", () => {
  it("submits form with valid data", async () => {
    const onSubmit = vi.fn();
    const user = userEvent.setup();

    render(<UserForm onSubmit={onSubmit} />);

    await user.type(screen.getByLabelText("Email"), "jan@example.com");
    await user.type(screen.getByLabelText("Hasło"), "password123");
    await user.click(screen.getByRole("button", { name: "Zaloguj" }));

    await waitFor(() => {
      expect(onSubmit).toHaveBeenCalledWith({
        email: "jan@example.com",
        password: "password123",
      });
    });
  });

  it("shows validation error on empty email", async () => {
    const user = userEvent.setup();
    render(<UserForm onSubmit={vi.fn()} />);

    await user.click(screen.getByRole("button", { name: "Zaloguj" }));

    expect(screen.getByText("Email jest wymagany")).toBeInTheDocument();
  });
});
```

### Backend – Supertest (Express/Fastify)

```ts
import request from "supertest";
import { app } from "../app";

describe("POST /api/users", () => {
  it("creates user and returns 201", async () => {
    const response = await request(app)
      .post("/api/users")
      .send({ name: "Jan", email: "jan@example.com" })
      .expect(201);

    expect(response.body).toMatchObject({
      name: "Jan",
      email: "jan@example.com",
    });
    expect(response.body.id).toBeDefined();
  });

  it("returns 400 on invalid email", async () => {
    await request(app)
      .post("/api/users")
      .send({ name: "Jan", email: "not-an-email" })
      .expect(400);
  });
});
```

### Mockowanie API – MSW (Mock Service Worker)

```ts
import { http, HttpResponse } from 'msw'
import { setupServer } from 'msw/node'

const server = setupServer(
  http.get('/api/users', () => {
    return HttpResponse.json([{ id: '1', name: 'Jan' }])
  })
)

beforeAll(() => server.listen())
afterEach(() => server.resetHandlers())
afterAll(() => server.close())

it('renders user list from API', async () => {
  render(<UserList />)
  await screen.findByText('Jan')  // czeka aż pojawi się po fetchu
})
```

---

## 🎭 Poziom 3 – Testy E2E

**Co testować:** krytyczne ścieżki użytkownika (happy path), flow logowania, checkout, onboarding.

```ts
// Testuj FLOW, nie szczegóły implementacji
test("user can register and login", async ({ page }) => {
  // Rejestracja
  await page.goto("/register");
  await page.fill('[name="email"]', "newuser@example.com");
  await page.fill('[name="password"]', "SecurePass123!");
  await page.click('button[type="submit"]');

  // Przekierowanie po rejestracji
  await expect(page).toHaveURL("/onboarding");

  // Login
  await page.goto("/login");
  await page.fill('[name="email"]', "newuser@example.com");
  await page.fill('[name="password"]', "SecurePass123!");
  await page.click('button[type="submit"]');

  await expect(page).toHaveURL("/dashboard");
  await expect(page.getByText("Witaj!")).toBeVisible();
});
```

**Co NIE testować w E2E:**

- Szczegóły UI (kolory, marginesy) → testy wizualne (Chromatic)
- Walidacja każdego pola formularza → testy integracyjne
- Logika biznesowa → testy jednostkowe

---

## 🎯 Co Testować na Każdym Poziomie

```
JEDNOSTKOWE ✅
├── Utility functions (formatDate, calculateTotal)
├── Custom hooks (useDebounce, useLocalStorage)
├── Reducery i store actions (Zustand, Redux)
├── Walidatory (Zod schemas logic)
└── Transformacje danych (mappers, parsery)

INTEGRACYJNE ✅
├── Komponenty z formularzami i interakcjami
├── Komponenty z fetchowaniem danych
├── Endpointy API (request → response)
├── Integracja z bazą danych (repository pattern)
└── Middleware i autoryzacja

E2E ✅
├── Rejestracja i logowanie
├── Główny flow produktu (np. złożenie zamówienia)
├── Krytyczne ścieżki płatności
└── Onboarding nowego użytkownika
```

---

## 📊 Metryki – Ile Pokrycia?

```
Logika biznesowa (serwisy, utils):  ≥ 90%
Komponenty React:                   ≥ 70%
Endpointy API:                      ≥ 80%
Ogólne pokrycie projektu:           ≥ 70%
```

> ⚠️ 100% coverage ≠ brak bugów. Testuj zachowania, nie linie kodu.

---

## 💡 Tips

- **Pisz testy PRZED bugfixem** – najpierw test który reprodukuje bug, potem fix
- **Nie mockuj za dużo** – im bliżej produkcji, tym bardziej wartościowy test
- **Testing Library mantra:** _"Test what users see, not implementation details"_ – nie testuj `useState`, testuj co jest na ekranie
- **Nazewnictwo:** `it('should [action] when [condition]')` – opis jak dokumentacja
- **Flaky tests E2E** – dodaj `retries: 2` w CI, ale napraw przyczynę
