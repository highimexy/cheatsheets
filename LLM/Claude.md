# 🤖 Claude – Prompt Engineering

Wzorce i techniki pisania skutecznych promptów dla Claude (Anthropic).

---

## 🏗️ Anatomia Dobrego Promptu

```
[ROLA]       Kim ma być model?
[KONTEKST]   Jakie tło jest potrzebne?
[ZADANIE]    Co dokładnie ma zrobić?
[FORMAT]     Jak ma wyglądać output?
[OGRANICZENIA] Czego unikać?
```

**Przykład:**

```
Jesteś senior TypeScript developerem specjalizującym się w React.
Mam komponent który re-renderuje się zbyt często.
Przejrzyj poniższy kod i wskaż przyczyny oraz popraw je.
Odpowiedz w formacie: 1) Problem 2) Wyjaśnienie 3) Poprawiony kod.
Nie zmieniaj logiki biznesowej – tylko optymalizacja.
```

---

## 📐 Techniki Promptowania

### 1. Role Prompting

```
"Jesteś doświadczonym code reviewerem. Oceń ten kod..."
"Działaj jak QA engineer – znajdź potencjalne bugi w..."
"Jesteś architektem systemu – zaproponuj strukturę dla..."
```

### 2. Chain of Thought (krok po kroku)

```
"Zanim odpiszesz, przemyśl to krok po kroku:
1. Zidentyfikuj problem
2. Rozważ możliwe rozwiązania
3. Wybierz najlepsze i uzasadnij"
```

### 3. Few-shot (przykłady wejście → wyjście)

```
"Konwertuj nazwy zmiennych na camelCase:
user_name → userName
first_name → firstName
api_key → ???"
```

### 4. Format XML/JSON dla strukturyzowanego outputu

```
"Zwróć odpowiedź WYŁĄCZNIE w formacie JSON:
{
  'bugs': [],
  'suggestions': [],
  'score': 0-10
}"
```

---

## 💻 Prompty dla Developera

### Code Review

```
Zrób code review tego kodu [TS/React/Go].
Oceń pod kątem:
- Potencjalne bugi i edge cases
- Wydajność i re-rendery
- Czytelność i naming
- Zgodność z SOLID/DRY

Kod:
[wklej kod]
```

### Debugowanie

```
Mam błąd którego nie rozumiem.
Środowisko: [Next.js 14 / Node 20]
Błąd: [wklej error]
Kod w którym występuje: [wklej fragment]
Co już próbowałem: [twoje próby]

Wyjaśnij przyczynę i podaj rozwiązanie.
```

### Generowanie Testów

```
Napisz testy jednostkowe Vitest dla tej funkcji.
Uwzględnij: happy path, edge cases, przypadki błędów.
Używaj wzorca AAA (Arrange-Act-Assert).

Funkcja:
[wklej funkcję]
```

### Refactoring

```
Zrefaktoryzuj ten kod zachowując dokładnie tę samą funkcjonalność.
Cel: lepsza czytelność, TypeScript best practices, mniejsza złożoność.
Wyjaśnij każdą zmianę.

[wklej kod]
```

---

## 🎨 Prompty dla Treści / Dokumentacji

### README / Dokumentacja

```
Napisz README.md dla tego projektu.
Tech stack: [React, TypeScript, Vite]
Cel projektu: [opis]
Uwzględnij: opis, instalację, konfigurację, przykłady użycia.
Styl: profesjonalny, z emoji, po polsku.
```

### Conventional Commits

```
Na podstawie tych zmian w kodzie zaproponuj 3 wiadomości commit
zgodne z Conventional Commits (feat/fix/refactor/chore/docs).
Zmiany: [opis lub diff]
```

---

## ⚙️ Zaawansowane Techniki

### Iteracyjne Doprecyzowywanie

```
Wygeneruj [X].
Teraz popraw to uwzględniając: [feedback].
Zachowaj to co było dobre, zmień tylko: [konkretne elementy].
```

### Porównanie Podejść

```
Pokaż mi 2-3 różne sposoby implementacji [feature].
Dla każdego podaj: kod, zalety, wady, kiedy używać.
```

### System Prompt (dla własnych narzędzi AI)

```
Jesteś asystentem do code review dla zespołu używającego:
TypeScript, React 18, Next.js 14 App Router, Prisma, tRPC.
Zawsze: sprawdzaj typy, wskazuj missing error handling,
sugeruj nazewnictwo zgodne z conventions projektu.
Nigdy: nie zmieniaj architektury bez wyraźnej prośby.
```

---

## 💡 Tips

- Im więcej kontekstu, tym lepsza odpowiedź – wklej błąd, kod, wersje
- `"Myśl krok po kroku"` znacząco poprawia jakość przy złożonych problemach
- Jeśli output jest za długi: `"Odpowiedz zwięźle, maksymalnie 5 punktów"`
- Jeśli output jest za krótki: `"Rozwiń punkt X, pokaż konkretny kod"`
- Używaj `"Bez komentarzy w kodzie"` jeśli chcesz czysty kod do copy-paste
