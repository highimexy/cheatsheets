# ✨ Gemini – Prompt Engineering

Wzorce i techniki dla modeli Google Gemini (1.5 Pro / 2.0 Flash).

---

## 🏗️ Mocne Strony Gemini

| Cecha             | Gemini                          |
| ----------------- | ------------------------------- |
| Kontekst okna     | Do 1M tokenów (1.5 Pro)         |
| Multimodalność    | Tekst, obraz, audio, video, PDF |
| Integracja Google | Docs, Sheets, Drive, Search     |
| Kod               | Silny w Python, JS, SQL         |

---

## 📐 Podstawowe Techniki

### Strukturyzowany Prompt

```
## Rola
Jesteś ekspertem od baz danych i SQL.

## Zadanie
Zoptymalizuj poniższe zapytanie SQL.
Baza: PostgreSQL 15, tabela 'orders' ma 5M rekordów.

## Kryteria sukcesu
- Czas zapytania < 100ms
- Zaproponuj indeksy jeśli potrzebne
- Wyjaśnij każdą zmianę

## Zapytanie
[wklej SQL]
```

### Wykorzystanie Długiego Kontekstu

```
Poniżej wklejam całą bazę kodu (10 plików).
Przeanalizuj architekturę i odpowiedz na pytania:
1. Gdzie mogą wystąpić wycieki pamięci?
2. Które moduły mają zbyt silne powiązania?

[wklej kod]
```

---

## 💻 Prompty dla Developera

### Analiza Dużego Codebase

```
Masz dostęp do całego repozytorium.
Zadanie: Znajdź wszystkie miejsca gdzie nie obsługujemy błędów async/await.
Zwróć listę: plik, linia, sugestia poprawki.
```

### Generowanie Danych Testowych

```
Wygeneruj 20 realistycznych rekordów JSON dla encji User:
- id (UUID)
- name (polskie imiona i nazwiska)
- email (realistyczne domeny)
- createdAt (ostatnie 2 lata)
- role: 'admin' | 'user' | 'moderator' (10% admin, 20% moderator)
```

### SQL i Bazy Danych

```
Napisz migrację Prisma i zapytanie które:
1. Dodaje tabelę 'AuditLog' z polami: id, userId, action, metadata(JSON), createdAt
2. Pobiera 10 ostatnich akcji dla usera z JOIN na tabelę users
3. Indeks na (userId, createdAt) dla wydajności
```

---

## 🖼️ Multimodalne Prompty

### Analiza Screenshota / Mockupu

```
Popatrz na ten screenshot interfejsu.
1. Opisz strukturę komponentów React którą byś zaproponował
2. Wskaż potencjalne problemy UX/dostępności
3. Napisz szkielet kodu w React + Tailwind
```

### Analiza Diagramu / ERD

```
Na podstawie tego diagramu ERD:
1. Wygeneruj schemat Prisma
2. Wskaż brakujące relacje lub potencjalne problemy
3. Zaproponuj indeksy dla typowych query patterns
```

---

## 🔧 Gemini API (w kodzie)

```ts
// Instalacja
// npm install @google/generative-ai

import { GoogleGenerativeAI } from "@google/generative-ai";

const genAI = new GoogleGenerativeAI(process.env.GEMINI_API_KEY!);
const model = genAI.getGenerativeModel({ model: "gemini-1.5-flash" });

// Prosty prompt
const result = await model.generateContent("Wyjaśnij czym jest closure w JS");
console.log(result.response.text());

// Z system promptem
const chat = model.startChat({
  systemInstruction: "Jesteś senior TypeScript developerem.",
  history: [],
});
const response = await chat.sendMessage("Zrób review tego kodu: ...");
```

---

## ⚖️ Kiedy Gemini vs Claude?

| Zadanie                          | Lepszy wybór               |
| -------------------------------- | -------------------------- |
| Analiza całego repozytorium      | ✅ Gemini (długi kontekst) |
| Analiza obrazów/video            | ✅ Gemini                  |
| Nuansowane instrukcje i styl     | ✅ Claude                  |
| Złożone rozumowanie etyczne      | ✅ Claude                  |
| Integracja z Google Workspace    | ✅ Gemini                  |
| Pisanie i edycja długich tekstów | ✅ Claude                  |

---

## 💡 Tips

- Dla długich dokumentów: wklej cały plik zamiast fragmentu – Gemini to ogarnie
- `"Odpowiedz tylko kodem, bez wyjaśnień"` – clean output do copy-paste
- Przy analizie obrazów bądź bardzo konkretny: `"Wskaż element X i opisz Y"`
- Gemini Flash jest szybszy i tańszy niż Pro – używaj do prostych zadań
