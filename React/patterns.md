# 🧩 React Design Patterns

Zbiór sprawdzonych wzorców projektowych, które pomagają budować skalowalne i łatwe w utrzymaniu aplikacje.

---

## 1. Container & Presentational Components

Rozdzielenie logiki od wyglądu.

- **Container:** Pobiera dane, zarządza stanem, obsługuje logikę (np. `UserListContainer.tsx`).
- **Presentational:** Przyjmuje dane przez propsy i tylko je wyświetla (np. `UserList.tsx`).
- **Zaleta:** Łatwiejsze testowanie i możliwość ponownego użycia UI z innymi danymi.

---

## 2. Higher-Order Components (HOC)

Funkcja, która przyjmuje komponent i zwraca nowy, wzbogacony komponent.

- **Przykład:** `withAuth(MyComponent)` – dodaje logikę sprawdzania uprawnień do dowolnego komponentu.
- **Uwaga:** W nowoczesnym Reactcie wzorzec ten jest często zastępowany przez **Custom Hooks**.

---

## 3. Render Props

Technika współdzielenia kodu między komponentami za pomocą propa, którego wartością jest funkcja.

```jsx
<DataProvider render={(data) => <h1>Witaj, {data.name}</h1>} />
```
