# 🧪 Jest & Vitest – Testy Jednostkowe

Szybka baza wiedzy do pisania testów w projektach JS/TS.

---

## ⚙️ Setup

```bash
# Vitest (zalecany w projektach Vite/React)
npm install -D vitest @vitest/ui

# Jest (klasyk, np. w Next.js)
npm install -D jest @types/jest ts-jest
```

```ts
// vitest.config.ts
import { defineConfig } from "vitest/config";
export default defineConfig({
  test: {
    environment: "jsdom", // dla testów komponentów React
    globals: true,
  },
});
```

---

## 🏗️ Struktura Testu (AAA Pattern)

```ts
describe("formatPrice", () => {
  it("should format number as PLN currency", () => {
    // Arrange
    const price = 1999.99;

    // Act
    const result = formatPrice(price);

    // Assert
    expect(result).toBe("1 999,99 zł");
  });
});
```

---

## ✅ Najważniejsze Matchery

```ts
// Wartości
expect(value).toBe(42); // ścisła równość (===)
expect(value).toEqual({ a: 1 }); // głęboka równość (obiekty/tablice)
expect(value).toBeTruthy();
expect(value).toBeFalsy();
expect(value).toBeNull();
expect(value).toBeUndefined();

// Liczby
expect(value).toBeGreaterThan(10);
expect(value).toBeLessThanOrEqual(100);
expect(value).toBeCloseTo(0.3, 5); // dla float

// Stringi
expect(str).toContain("hello");
expect(str).toMatch(/^Error:/);

// Tablice
expect(arr).toHaveLength(3);
expect(arr).toContain("item");

// Obiekty
expect(obj).toHaveProperty("name", "Jan");

// Wyjątki
expect(() => riskyFn()).toThrow("Invalid input");
```

---

## 🤖 Mocki i Spies

```ts
// Mock funkcji
const mockFn = vi.fn(); // vitest
const mockFn = jest.fn(); // jest
mockFn.mockReturnValue(42);
mockFn.mockResolvedValue({ data }); // async

expect(mockFn).toHaveBeenCalled();
expect(mockFn).toHaveBeenCalledWith("arg1", "arg2");
expect(mockFn).toHaveBeenCalledTimes(1);

// Mock modułu
vi.mock("../api/users", () => ({
  fetchUsers: vi.fn().mockResolvedValue([{ id: 1, name: "Jan" }]),
}));

// Spy – obserwuj bez zastępowania
const spy = vi.spyOn(console, "error").mockImplementation(() => {});
// po teście:
spy.mockRestore();
```

---

## ⏱️ Testy Asynchroniczne

```ts
// async/await (zalecane)
it("should fetch user data", async () => {
  const user = await fetchUser(1);
  expect(user.name).toBe("Jan");
});

// Obsługa odrzucenia Promise
it("should throw on invalid id", async () => {
  await expect(fetchUser(-1)).rejects.toThrow("Not found");
});
```

---

## 🪝 Hooki Cyklu Życia

```ts
describe("UserService", () => {
  beforeAll(() => {
    /* raz przed wszystkimi testami */
  });
  afterAll(() => {
    /* raz po wszystkich testach */
  });
  beforeEach(() => {
    /* przed każdym testem */
  });
  afterEach(() => {
    /* po każdym teście – np. vi.clearAllMocks() */
  });
});
```

---

## 🏃 Uruchamianie Testów

```bash
npx vitest              # watch mode (domyślny)
npx vitest run          # jednorazowy run (CI)
npx vitest --ui         # UI w przeglądarce
npx vitest run --coverage  # raport pokrycia kodu

# Uruchom tylko konkretny plik
npx vitest run src/utils/format.test.ts

# Filtruj po nazwie testu
npx vitest -t "formatPrice"
```

---

## 💡 Tips

- Trzymaj pliki testów obok pliku źródłowego: `utils.ts` → `utils.test.ts`
- Używaj `it.only()` / `describe.only()` do debugowania jednego testu
- Używaj `it.skip()` do tymczasowego pomijania testu
- Celuj w pokrycie kodu (coverage) minimum 80% dla logiki biznesowej
