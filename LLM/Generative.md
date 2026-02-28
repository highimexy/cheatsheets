# 🤖 Generative AI – Koncepcje i Narzędzia

Fundamenty LLM, wzorce integracji i narzędzia dla developerów.

---

## 🧠 Kluczowe Pojęcia

### Tokeny

```
Token ≈ 0.75 słowa (angielski) / 0.5 słowa (polski)

"Hello, world!" = 4 tokeny
"Cześć, świecie!" = 6 tokenów

Kontekst (context window) = ile tokenów model widzi naraz:
  GPT-4o:          128k tokenów  ≈ ~300 stron tekstu
  Claude Sonnet:   200k tokenów  ≈ ~460 stron tekstu
  Gemini 1.5 Pro:    1M tokenów  ≈ całe repozytorium

Koszt = input tokens + output tokens (rozliczenie per 1M tokenów)
```

### Temperature i Sampling

```
temperature: 0.0 → deterministyczny, zawsze ta sama odpowiedź
temperature: 0.7 → balans kreatywność/spójność (domyślne)
temperature: 1.0 → bardziej losowy, kreatywny
temperature: 2.0 → chaotyczny, niezalecany

Kiedy używać niskiej temperatury (0.0-0.3):
  - Kod, SQL, JSON
  - Pytania z jedną poprawną odpowiedzią
  - Klasyfikacja, ekstrakcja danych

Kiedy używać wyższej (0.7-1.0):
  - Treści kreatywne, brainstorming
  - Generowanie wariantów, parafrazowanie
```

### Typy Modeli

```
Frontier models (największe, najdroższe):
  Claude Opus, GPT-4o, Gemini 1.5 Pro
  → złożone zadania, reasoning, długi kontekst

Mid-tier (balans jakość/cena):
  Claude Sonnet, GPT-4o-mini, Gemini 1.5 Flash
  → produkcja, większość use-casów

Fast/Cheap (najszybsze, najtańsze):
  Claude Haiku, GPT-3.5-turbo
  → proste zadania, klasyfikacja, duże wolumeny
```

---

## 🏗️ Wzorce Integracji

### RAG – Retrieval Augmented Generation

```
Problem: LLM nie zna Twoich prywatnych danych ani aktualności

Rozwiązanie:
  1. Chunking    → podziel dokumenty na fragmenty (~500 tokenów)
  2. Embedding   → zamień fragmenty na wektory (np. text-embedding-3-small)
  3. Vector DB   → zapisz w bazie wektorowej (Pinecone, Qdrant, pgvector)
  4. Query       → zamień pytanie użytkownika na wektor
  5. Retrieval   → znajdź najbardziej podobne fragmenty (cosine similarity)
  6. Augment     → wstrzyknij fragmenty do promptu jako kontekst
  7. Generate    → LLM odpowiada mając kontekst z Twoich danych

Narzędzia: LangChain, LlamaIndex, Vercel AI SDK
```

```ts
// Prosty RAG flow (pseudokod)
async function ragQuery(question: string) {
  const queryEmbedding = await embedText(question);
  const relevantDocs = await vectorDB.search(queryEmbedding, { topK: 5 });
  const context = relevantDocs.map((d) => d.content).join("\n\n");

  return await llm.complete({
    messages: [
      {
        role: "user",
        content: `Kontekst:\n${context}\n\nPytanie: ${question}`,
      },
    ],
  });
}
```

### Function Calling / Tool Use

```ts
// Model może wywoływać Twoje funkcje
const tools = [
  {
    name: "get_weather",
    description: "Pobiera aktualną pogodę dla danego miasta",
    input_schema: {
      type: "object",
      properties: {
        city: { type: "string", description: "Nazwa miasta" },
      },
      required: ["city"],
    },
  },
];

// Model decyduje kiedy wywołać narzędzie
// Ty wykonujesz funkcję i zwracasz wynik do modelu
// Model generuje końcową odpowiedź na podstawie danych
```

### Structured Output (JSON Mode)

```ts
// Wymuszenie odpowiedzi w formacie JSON
const response = await anthropic.messages.create({
  model: "claude-sonnet-4-5",
  messages: [
    {
      role: "user",
      content: `Wyekstrahuj dane z tekstu. Odpowiedz TYLKO w JSON bez żadnego tekstu:
    {
      "name": "string",
      "email": "string | null",
      "company": "string | null"
    }

    Tekst: "Cześć, jestem Jan Kowalski z firmy Acme, jan@acme.com"`,
    },
  ],
});

const data = JSON.parse(response.content[0].text);
```

---

## 🔗 Agentic AI – Autonomiczne Agenty

```
Agent = LLM + Tools + Loop

Pętla agenta:
  1. Otrzymaj zadanie
  2. Zaplanuj kolejne kroki (reasoning)
  3. Wybierz i wywołaj narzędzie
  4. Obserwuj wynik
  5. Powtórz aż zadanie ukończone

Wzorce:
  ReAct    → Reason + Act (myśl, działaj, obserwuj)
  Chain    → sekwencja kroków z góry zdefiniowana
  Router   → wybierz specjalistyczny agent do zadania

Ryzyka:
  - Pętle bez wyjścia (ustaw max_iterations)
  - Koszty – każdy krok = tokeny
  - Halucynacje przy wywoływaniu narzędzi
```

---

## 💬 System Prompt vs User Prompt

```
System Prompt:
  - Rola i osobowość modelu
  - Stałe instrukcje i ograniczenia
  - Kontekst aplikacji
  - Format odpowiedzi
  - Język i ton

User Prompt:
  - Konkretne pytanie/zadanie od użytkownika
  - Dane do przetworzenia
  - Zmienne informacje
```

```ts
const response = await anthropic.messages.create({
  system: `Jesteś asystentem do code review dla zespołu TypeScript.
  Odpowiadaj zawsze po polsku.
  Oceniaj pod kątem: błędów, wydajności, czytelności.
  Format: markdown z sekcjami ## Błędy ## Sugestie ## Ocena`,

  messages: [
    {
      role: "user",
      content: `Zrób review:\n\`\`\`ts\n${code}\n\`\`\``,
    },
  ],
});
```

---

## 📊 Embeddings i Wektory

```ts
// Zamień tekst na wektor (do porównywania semantycznego)
const embedding = await openai.embeddings.create({
  model: "text-embedding-3-small",
  input: "Jak resetować hasło?",
});
// Zwraca tablicę 1536 liczb

// Cosine similarity – mierzy podobieństwo (-1 do 1)
function cosineSimilarity(a: number[], b: number[]): number {
  const dot = a.reduce((sum, ai, i) => sum + ai * b[i], 0);
  const normA = Math.sqrt(a.reduce((sum, ai) => sum + ai * ai, 0));
  const normB = Math.sqrt(b.reduce((sum, bi) => sum + bi * bi, 0));
  return dot / (normA * normB);
}

// Zastosowania:
// - Wyszukiwanie semantyczne (RAG)
// - Klasyfikacja dokumentów
// - Wykrywanie duplikatów
// - Rekomendacje
```

---

## 🛠️ Narzędzia i SDK

```
Frameworki:
  LangChain     – najpopularniejszy, Python + JS, wszystko w jednym
  LlamaIndex    – RAG-focused, zarządzanie dokumentami
  Vercel AI SDK – React hooks do streamingu, integracja Next.js

Vector Databases:
  Pinecone      – managed, production-ready
  Qdrant        – open-source, self-hosted
  pgvector      – PostgreSQL extension (masz już bazę!)
  Weaviate      – open-source, GraphQL API

Observability:
  LangSmith     – tracing dla LangChain
  Helicone      – proxy z analytics dla wszystkich LLM
  Langfuse      – open-source, self-hosted
```

---

## 💡 Tips

- **Streaming** – używaj SSE/stream zamiast czekać na pełną odpowiedź (lepszy UX)
- **Caching** – identyczne prompty z tą samą temperaturą = identyczna odpowiedź, możesz cachować
- **Prompt versioning** – traktuj prompty jak kod: wersjonuj, testuj, mierz regresje
- **Eval > Vibe check** – mierz jakość odpowiedzi automatycznie, nie tylko ręcznie
- Zawsze miej **fallback** gdy API jest niedostępne lub zwraca błąd
- **Rate limiting** po stronie aplikacji – jeden user nie powinien wyczerpać całego budżetu
