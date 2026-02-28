# 👀 Code Review

Jak pisać i przyjmować code review – dla autora i reviewera.

---

## 🧑‍💻 Jako Autor PR

### Przed Otwarciem PR

```
□ Kod działa lokalnie (uruchomiłem i przetestowałem)
□ Testy przechodzą (npm test)
□ Brak błędów lintera (npm run lint)
□ Brak zbędnych console.log, debuggerów, komentarzy TODO (lub opisane w ticket)
□ PR dotyczy jednej rzeczy – nie mieszaj feature + refactor + fix
□ Rozmiar PR: idealnie < 400 linii zmian
```

### Opis PR – Szablon

```markdown
## Co zostało zrobione?

Krótki opis co i dlaczego – kontekst dla reviewera.

## Jak to przetestować?

1. Przejdź do /users
2. Kliknij "Dodaj użytkownika"
3. Sprawdź czy formularz waliduje email

## Screenshots (jeśli zmiana UI)

| Przed         | Po         |
| ------------- | ---------- |
| ![przed](url) | ![po](url) |

## Checklist

- [ ] Testy jednostkowe dodane/zaktualizowane
- [ ] Dokumentacja zaktualizowana
- [ ] Breaking change? (opisane poniżej)
```

---

## 🔍 Jako Reviewer

### Hierarchia Komentarzy

Używaj prefiksów żeby jasno komunikować priorytet:

```
nit:      Drobiazg, nie blokuje merge – kosmetyka, styl
         "nit: możesz użyć Optional Chaining zamiast &&"

suggest:  Sugestia do rozważenia, nie musisz zmieniać
         "suggest: rozważyłbym wydzielenie tej logiki do hooka"

question: Pytanie – chcę zrozumieć decyzję
         "question: dlaczego tutaj używamy useEffect zamiast useMemo?"

issue:    Problem który trzeba naprawić przed merge
         "issue: ten kod nie obsługuje przypadku gdy users jest null"

blocker:  Poważny błąd, bezwzględnie do naprawy
         "blocker: to introduce SQL injection vulnerability"
```

### Co Sprawdzać

```
POPRAWNOŚĆ
□ Logika jest poprawna (edge cases: null, pusta tablica, błąd sieci)
□ Nie ma potencjalnych bugów ani race conditions
□ Error handling na async operacjach

BEZPIECZEŃSTWO
□ Brak hardkodowanych sekretów / tokenów
□ Input od użytkownika jest walidowany/sanityzowany
□ Brak przypadkowego logowania wrażliwych danych (hasła, tokeny)

WYDAJNOŚĆ
□ Brak niepotrzebnych re-renderów (React)
□ Brak N+1 queries (baza danych)
□ Duże listy są paginowane / wirtualizowane

JAKOŚĆ KODU
□ Czytelne nazwy zmiennych i funkcji
□ Brak duplikacji – DRY (ale nie na siłę)
□ Funkcje i komponenty nie są zbyt duże (< 50 linii to dobry sygnał)
□ Testy pokrywają nową logikę

TYPY (TypeScript)
□ Brak any bez uzasadnienia
□ Poprawne typy dla props, zwracanych wartości, API response
```

---

## 💬 Kultura Code Review

### Dobre Komentarze ✅

```
# Konkretne + z wyjaśnieniem
"issue: jeśli users jest pustą tablicą, users[0] zwróci undefined
i linia 42 wyrzuci TypeError. Dodaj sprawdzenie: if (!users.length) return null"

# Sugestia z przykładem kodu
"suggest: możesz uprościć to do:
const isAdmin = user?.role === 'admin'
zamiast 3-linijkowego if/else"

# Doceniaj dobre decyzje
"Świetne podejście z useReducer tutaj – dużo czytelniejsze niż kilka useState 👍"
```

### Złe Komentarze ❌

```
"To jest złe."              → brak wyjaśnienia, destruktywne
"Nie podoba mi się."        → subiektywne bez uzasadnienia
"Tutaj użyłbym X."          → bez wyjaśnienia dlaczego
"WTF?"                      → nieprofesjonalne
```

### Zasady Dla Obu Stron

```
DLA REVIEWERA
• Komentuj kod, nie osobę ("ten kod" nie "ty")
• Zadawaj pytania zamiast wydawać wyroki
• Daj approval jeśli tylko drobiazgi – nie blokuj przez nit-picki
• Odpowiadaj w ciągu 24h (w godzinach pracy)

DLA AUTORA
• Nie bierz komentarzy osobiście – to review kodu, nie Ciebie
• Odpowiadaj na każdy komentarz (Resolved / wyjaśnienie)
• Pytaj jeśli komentarz jest niejasny
• Dziękuj za wykrycie bugów – to robota reviewera
```

---

## ⚡ Efektywne Review – Dobre Praktyki

### Rozmiar PR

```
< 200 linii    → Idealny, szybki review
200-400 linii  → Akceptowalny
400-800 linii  → Trudny, rozważ podział
> 800 linii    → Podziel na mniejsze PR
```

### Self-Review Przed Wysłaniem

```bash
# Przejrzyj własne zmiany zanim poprosisz o review
git diff main...HEAD              # wszystkie zmiany względem main
git diff --staged                 # to co idzie w commicie
```

Otwórz PR i przejdź przez każdą linię jakbyś był reviewerem – znajdziesz 30-50% problemów sam.

### Kiedy Odrzucić Review

```
✅ Możesz odrzucić jeśli:
• Komentarz to czysty nit-pick bez wpływu na jakość
• Reviewer proponuje inny styl (nie lepszy, tylko inny)
• Zmiana wymagałaby przebudowania całego PR

❌ Nie możesz odrzucić jeśli:
• Jest potencjalny bug
• Jest problem z bezpieczeństwem
• Brakuje testów dla nowej logiki
```

---

## 🤖 Automatyzacja – Rzeczy Które Nie Powinny Być W Code Review

```yaml
# GitHub Actions – sprawdź to automatycznie, nie ręcznie
- Linting (ESLint)
- Formatowanie (Prettier)
- TypeScript type check
- Testy jednostkowe
- Test coverage threshold
- Security scan (np. npm audit)
```

> Reviewerzy powinni skupiać się na logice i architekturze, nie na formatowaniu.

---

## 💡 Tips

- **Two-pass review** – pierwsze czytanie: zrozum co robi PR. Drugie: komentuj szczegóły
- **Review w max 400 linii na raz** – po tym koncentracja spada i przegapisz bugi
- Jeśli coś zajmuje > 1h do review – poproś autora o podział PR lub walk-through
- Używaj **Draft PR** gdy chcesz wczesnego feedbacku, zanim skończysz
