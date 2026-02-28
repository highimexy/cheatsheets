# 🏷️ Naming Conventions

Spójne nazewnictwo to jeden z najważniejszych elementów "Clean Code". Poniżej znajdziesz standardy stosowane w nowoczesnym programowaniu.

## 1. Główne Style (Cases)

- `camelCase` – pierwsza litera mała, kolejne wyrazy wielką (np. `userName`).
- `PascalCase` – każda pierwsza litera wyrazu wielką (np. `UserProfile`).
- `snake_case` – małe litery oddzielone podkreślnikiem (np. `user_id`).
- `kebab-case` – małe litery oddzielone myślnikiem (np. `main-button`).

---

## 2. React & Frontend

### Komponenty

- **Standard:** `PascalCase`
- **Dlaczego:** React odróżnia komponenty od tagów HTML po wielkiej literze.
- **Przykład:** `SidebarNavigation.tsx`, `PrimaryButton.jsx`

### Hooki

- **Standard:** `camelCase` z prefiksem `use`
- **Przykład:** `useAuth()`, `useLocalStorage()`

### Pliki stylów / Assety

- **Standard:** `kebab-case`
- **Przykład:** `main-layout.css`, `hero-image.png`

---

## 3. TypeScript / JavaScript

### Zmienne i Funkcje

- **Standard:** `camelCase`
- **Przykład:** `const isLoading = true;`, `function fetchData() {}`

### Interfejsy i Typy

- **Standard:** `PascalCase`
- **Dobra praktyka:** Unikaj prefiksu `I` (nie `IUser`, tylko `User`).
- **Przykład:** `type ApiResponse = { ... }`

### Stałe (Globalne)

- **Standard:** `SCREAMING_SNAKE_CASE`
- **Przykład:** `const API_KEY = '123';`, `const MAX_RETRIES = 5;`

---

## 4. Golang

### Widoczność (Exporting)

- **Publiczne (Exported):** `PascalCase` (dostępne poza pakietem).
- **Prywatne:** `camelCase` (dostępne tylko wewnątrz pakietu).

### Skróty (Initialisms)

- **Zasada:** Skróty takie jak URL, ID, HTTP powinny być pisane w całości wielkimi lub małymi literami.
- **Przykład:** `userID` (zamiast `userId`), `httpServer`.

---

## 5. Foldery w projekcie

- **Standard:** `kebab-case` (najbezpieczniejszy dla systemów plików i URL-i).
- **Przykład:** `ui-libraries/`, `auth-context/`

---

## 💡 Złote Zasady

1. **Nazwy powinny być opisowe:** `const d = 10;` (źle) ➔ `const daysUntilDeadline = 10;` (dobrze).
2. **Boolean startuje od czasownika:** `active` (średnio) ➔ `isActive`, `hasError`, `shouldRender` (dobrze).
3. **Funkcje to czasowniki:** `data()` (źle) ➔ `fetchData()`, `handleSubmit()`.
