# 🎭 Playwright & Cypress – Testy E2E

Automatyzacja testów w przeglądarce – klikanie, wypełnianie formularzy, asercje UI.

---

## ⚙️ Setup – Playwright (zalecany)

```bash
npm init playwright@latest

# Uruchom testy
npx playwright test

# Tryb UI (podgląd na żywo)
npx playwright test --ui

# Tryb headed (widać przeglądarkę)
npx playwright test --headed

# Raport HTML
npx playwright show-report
```

---

## 🏗️ Struktura Testu Playwright

```ts
// tests/login.spec.ts
import { test, expect } from "@playwright/test";

test.describe("Login Page", () => {
  test.beforeEach(async ({ page }) => {
    await page.goto("/login");
  });

  test("should login with valid credentials", async ({ page }) => {
    await page.fill('[data-testid="email"]', "test@example.com");
    await page.fill('[data-testid="password"]', "password123");
    await page.click('[data-testid="submit-btn"]');

    await expect(page).toHaveURL("/dashboard");
    await expect(page.getByText("Witaj!")).toBeVisible();
  });

  test("should show error on invalid credentials", async ({ page }) => {
    await page.fill('[data-testid="email"]', "wrong@example.com");
    await page.fill('[data-testid="password"]', "wrongpass");
    await page.click('[data-testid="submit-btn"]');

    await expect(page.getByText("Nieprawidłowe dane")).toBeVisible();
  });
});
```

---

## 🎯 Selektory (od najlepszego do najgorszego)

```ts
// ✅ ZALECANE – odporne na zmiany UI
page.getByRole("button", { name: "Zaloguj" });
page.getByLabel("Email");
page.getByPlaceholder("Wpisz email");
page.getByText("Witaj!");
page.getByTestId("submit-btn"); // data-testid="submit-btn"

// ⚠️ AKCEPTOWALNE
page.locator(".btn-primary");
page.locator("#login-form");

// ❌ UNIKAJ – kruche, łamią się przy refactorze
page.locator("div > form > div:nth-child(2) > input");
```

---

## ✅ Najważniejsze Asercje

```ts
// Widoczność
await expect(locator).toBeVisible();
await expect(locator).toBeHidden();
await expect(locator).toBeEnabled();
await expect(locator).toBeDisabled();

// Treść
await expect(locator).toHaveText("Witaj, Jan!");
await expect(locator).toContainText("Witaj");
await expect(locator).toHaveValue("test@example.com"); // input

// Atrybuty
await expect(locator).toHaveAttribute("type", "submit");
await expect(locator).toHaveClass(/active/);

// URL i tytuł strony
await expect(page).toHaveURL("/dashboard");
await expect(page).toHaveURL(/dashboard/);
await expect(page).toHaveTitle("Panel główny");

// Liczba elementów
await expect(page.getByRole("listitem")).toHaveCount(5);
```

---

## 🌐 Akcje i Interakcje

```ts
// Kliknięcia
await page.click("button");
await page.dblclick(".item");
await page.getByRole("button").click({ force: true });

// Formularze
await page.fill('[name="email"]', "jan@example.com");
await page.type('[name="search"]', "query", { delay: 50 }); // wolne pisanie
await page.selectOption("select", "option-value");
await page.check('[type="checkbox"]');
await page.uncheck('[type="checkbox"]');

// Klawiatura
await page.press("Enter");
await page.keyboard.press("Escape");

// Czekanie
await page.waitForURL("/dashboard");
await page.waitForSelector(".loading", { state: "hidden" });

// Screenshot (do debugowania)
await page.screenshot({ path: "debug.png" });
```

---

## 🔌 Mockowanie API (Page Route)

```ts
test("should handle API error gracefully", async ({ page }) => {
  // Przechwyć żądanie i zwróć błąd
  await page.route("**/api/users", (route) => {
    route.fulfill({ status: 500, body: "Server Error" });
  });

  await page.goto("/users");
  await expect(page.getByText("Wystąpił błąd")).toBeVisible();
});
```

---

## ⚙️ Konfiguracja `playwright.config.ts`

```ts
import { defineConfig, devices } from "@playwright/test";

export default defineConfig({
  testDir: "./tests",
  fullyParallel: true,
  retries: process.env.CI ? 2 : 0,
  use: {
    baseURL: "http://localhost:3000",
    trace: "on-first-retry",
    screenshot: "only-on-failure",
  },
  projects: [
    { name: "chromium", use: { ...devices["Desktop Chrome"] } },
    { name: "Mobile Safari", use: { ...devices["iPhone 13"] } },
  ],
  webServer: {
    command: "npm run dev",
    url: "http://localhost:3000",
    reuseExistingServer: !process.env.CI,
  },
});
```

---

## 💡 Tips

- Używaj `data-testid` w komponentach React dla niezawodnych selektorów
- `test.only()` – uruchom tylko jeden test podczas debugowania
- `await page.pause()` – zatrzymaj test i przejmij kontrolę ręcznie
- Nagrywaj testy: `npx playwright codegen localhost:3000`
- Używaj **Page Object Model (POM)** dla większych projektów
