# 📝 Conventional Commits

Standard wiadomości commitów – czytelna historia, automatyczny changelog.

---

## 📐 Format

```
type(scope): opis

[opcjonalne ciało]

[opcjonalne stopki]
```

```
type     → obowiązkowy – co to za zmiana
scope    → opcjonalny – czego dotyczy (w nawiasach)
opis     → obowiązkowy – krótko, małą literą, bez kropki na końcu
```

---

## 🏷️ Typy

| Typ        | Kiedy używać                                         | SemVer |
| ---------- | ---------------------------------------------------- | ------ |
| `feat`     | Nowa funkcjonalność dla użytkownika                  | MINOR  |
| `fix`      | Naprawa buga                                         | PATCH  |
| `refactor` | Refactoring – brak nowej funkcji ani fixa            | –      |
| `test`     | Dodawanie lub poprawianie testów                     | –      |
| `docs`     | Tylko dokumentacja                                   | –      |
| `chore`    | Narzędzia, dependencje, konfiguracja                 | –      |
| `style`    | Formatowanie, brakujące średniki (brak zmian logiki) | –      |
| `perf`     | Poprawa wydajności                                   | PATCH  |
| `ci`       | Zmiany w plikach CI/CD                               | –      |
| `build`    | Zmiany systemu budowania                             | –      |
| `revert`   | Cofnięcie poprzedniego commita                       | –      |

---

## ✅ Przykłady

### `feat` – Nowa funkcja

```bash
git commit -m "feat(auth): add JWT refresh token rotation"
git commit -m "feat(cart): implement discount code validation"
git commit -m "feat(users): add avatar upload with image compression"
git commit -m "feat: add dark mode toggle"
```

### `fix` – Naprawa buga

```bash
git commit -m "fix(login): redirect to dashboard after OAuth callback"
git commit -m "fix(api): handle null response from payment provider"
git commit -m "fix(cart): correct total calculation when applying discount"
git commit -m "fix: prevent double form submission on slow connection"
```

### `refactor` – Porządkowanie kodu

```bash
git commit -m "refactor(user): extract email validation to separate service"
git commit -m "refactor(auth): replace class-based service with functional approach"
git commit -m "refactor: simplify cart reducer logic"
```

### `test` – Testy

```bash
git commit -m "test(auth): add edge cases for token expiration handling"
git commit -m "test(cart): add integration tests for checkout flow"
git commit -m "test: increase coverage for user service to 90%"
```

### `docs` – Dokumentacja

```bash
git commit -m "docs(api): add OpenAPI spec for /users endpoints"
git commit -m "docs: update README with Docker setup instructions"
git commit -m "docs(contributing): add PR template and guidelines"
```

### `chore` – Konfiguracja i narzędzia

```bash
git commit -m "chore: update dependencies to latest versions"
git commit -m "chore(ci): add GitHub Actions workflow for automated tests"
git commit -m "chore: add eslint rule for no-console in production"
git commit -m "chore(docker): optimize Dockerfile for smaller image size"
```

### `perf` – Wydajność

```bash
git commit -m "perf(users): add database index on email column"
git commit -m "perf(images): switch to WebP format with lazy loading"
git commit -m "perf: memoize expensive filtering with useMemo"
```

---

## 💥 Breaking Changes

Dodaj `!` po typie lub użyj stopki `BREAKING CHANGE:`:

```bash
# Krótki breaking change – wykrzyknik
git commit -m "feat(api)!: change response format for /users endpoint"

# Z wyjaśnieniem w ciele commita
git commit -m "feat(api)!: change response format for /users endpoint

Wcześniej API zwracało tablicę bezpośrednio.
Teraz odpowiedź jest opakowana w obiekt z paginacją.

BREAKING CHANGE: response.data jest teraz { items: User[], total: number }
zamiast User[]. Zaktualizuj wszystkich konsumentów API."
```

---

## 🔭 Scope – Przykłady

Scope powinien odzwierciedlać moduł lub obszar aplikacji:

```
feat(auth):        # moduł autoryzacji
feat(users):       # moduł użytkowników
feat(cart):        # koszyk
fix(api):          # warstwa API
fix(db):           # baza danych
chore(deps):       # dependencje
chore(ci):         # CI/CD
docs(readme):      # plik README
test(e2e):         # testy e2e
style(dashboard):  # komponent dashboard
```

---

## 📏 Zasady Dobrego Commita

```bash
# ✅ Dobrze – konkretne, aktywna forma
"feat(auth): add password strength indicator to registration form"
"fix(api): return 404 instead of 500 when user not found"
"refactor(cart): replace multiple useState with useReducer"

# ❌ Źle – za ogólne
"fix: bug fix"
"feat: new feature"
"update stuff"
"WIP"
"asdfgh"

# ❌ Źle – wielka litera, kropka na końcu
"Feat(auth): Add login."
"Fix: Fixed the bug."
```

---

## 🤖 Automatyzacja

### Commitlint (walidacja commitów)

```bash
npm install -D @commitlint/cli @commitlint/config-conventional husky

# commitlint.config.ts
export default { extends: ['@commitlint/config-conventional'] }

# Dodaj hook przez husky
npx husky add .husky/commit-msg 'npx --no -- commitlint --edit "$1"'
```

### Generowanie Changelogu

```bash
# standard-version
npm install -D standard-version
# package.json: "release": "standard-version"
npm run release

# lub release-it
npm install -D release-it
```

---

## 💡 Tips

- **Jeden commit = jedna zmiana** – łatwiejszy revert, czytelniejszy blame
- **Imperatyw w opisie**: "add feature" nie "added feature" ani "adding feature"
- **Scope w nawiasach** – bez spacji: `feat(user-profile)` nie `feat( user profile )`
- Commitlint + Husky = automatyczna walidacja w zespole, zero rozbieżności
- Na podstawie Conventional Commits możesz auto-generować `CHANGELOG.md`
