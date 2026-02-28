# ⚛️ React Hooks – Kompletna Ściągawka

Najważniejsze hooki z przykładami użycia i pułapkami.

---

## 🔑 useState

```tsx
const [count, setCount] = useState(0);
const [user, setUser] = useState<User | null>(null);

// Aktualizacja na podstawie poprzedniego stanu (ZAWSZE tak przy increment!)
setCount((prev) => prev + 1);

// Aktualizacja obiektu – pamiętaj o spread
setUser((prev) => ({ ...prev!, name: "Jan" }));
```

---

## 🔄 useEffect

```tsx
// Po każdym renderze (rzadko potrzebne)
useEffect(() => { ... })

// Tylko raz – odpowiednik componentDidMount
useEffect(() => { ... }, [])

// Gdy zmieni się 'userId'
useEffect(() => {
  fetchUser(userId).then(setUser)
}, [userId])

// Cleanup – usuń listenery, anuluj requesty
useEffect(() => {
  const controller = new AbortController()
  fetch('/api/data', { signal: controller.signal }).then(...)
  return () => controller.abort()
}, [])
```

> ⚠️ Nie kłam w tablicy zależności – ESLint plugin `exhaustive-deps` jest Twoim przyjacielem

---

## 📦 useMemo & useCallback

```tsx
// useMemo – memoizacja wartości (np. ciężkie obliczenia, filtrowanie listy)
const filteredUsers = useMemo(
  () => users.filter((u) => u.role === activeRole),
  [users, activeRole], // przelicz tylko gdy to się zmieni
);

// useCallback – memoizacja funkcji (gdy przekazujesz do dziecka jako props)
const handleDelete = useCallback((id: string) => {
  setUsers((prev) => prev.filter((u) => u.id !== id));
}, []); // funkcja nie zmienia się między renderami
```

> 💡 Nie używaj ich na siłę – memoizacja ma koszt. Dodawaj gdy są faktyczne problemy z wydajnością.

---

## 📌 useRef

```tsx
// 1. Dostęp do elementu DOM
const inputRef = useRef<HTMLInputElement>(null)
<input ref={inputRef} />
inputRef.current?.focus()

// 2. Przechowywanie wartości BEZ triggerowania re-renderu
const timerRef = useRef<NodeJS.Timeout | null>(null)
timerRef.current = setTimeout(() => ..., 1000)

// 3. Poprzednia wartość
const prevCount = useRef(count)
useEffect(() => { prevCount.current = count }, [count])
```

---

## 🗺️ useContext

```tsx
// 1. Stwórz context
const ThemeContext = createContext<'light' | 'dark'>('light')

// 2. Owrap drzewo
<ThemeContext.Provider value="dark">
  <App />
</ThemeContext.Provider>

// 3. Użyj w komponencie
const theme = useContext(ThemeContext)
```

> 💡 Dla złożonego stanu globalnego rozważ Zustand zamiast czystego contextu

---

## 🔽 useReducer

```tsx
type Action =
  | { type: "increment" }
  | { type: "reset" }
  | { type: "set"; payload: number };

function reducer(state: number, action: Action): number {
  switch (action.type) {
    case "increment":
      return state + 1;
    case "reset":
      return 0;
    case "set":
      return action.payload;
  }
}

const [count, dispatch] = useReducer(reducer, 0);
dispatch({ type: "increment" });
dispatch({ type: "set", payload: 42 });
```

> Używaj zamiast `useState` gdy masz 3+ powiązanych wartości stanu lub złożoną logikę przejść.

---

## 📡 Custom Hook – Wzorzec

```tsx
// hooks/useLocalStorage.ts
function useLocalStorage<T>(key: string, initialValue: T) {
  const [value, setValue] = useState<T>(() => {
    try {
      const item = localStorage.getItem(key);
      return item ? JSON.parse(item) : initialValue;
    } catch {
      return initialValue;
    }
  });

  const setStoredValue = (newValue: T) => {
    setValue(newValue);
    localStorage.setItem(key, JSON.stringify(newValue));
  };

  return [value, setStoredValue] as const;
}

// Użycie:
const [theme, setTheme] = useLocalStorage("theme", "light");
```

---

## ⚡ useTransition & useDeferredValue (React 18+)

```tsx
// useTransition – oznacz wolną aktualizację stanu jako nieblokującą
const [isPending, startTransition] = useTransition()

startTransition(() => {
  setFilter(newFilter)  // wolna operacja nie zablokuje UI
})
{isPending && <Spinner />}

// useDeferredValue – opóźnij re-render dla ciężkiego komponentu
const deferredQuery = useDeferredValue(searchQuery)
<HeavyList filter={deferredQuery} />
```

---

## 💡 Tips & Pułapki

- **Nie** wywołuj hooków warunkowo (`if (x) useState(...)`) – to łamie Rules of Hooks
- Prefiks `use` jest wymagany dla custom hooków (ESLint to wymusza)
- `useEffect` z `async` – nigdy nie rób `async (effect)`. Stwórz async funkcję wewnątrz:
  ```tsx
  useEffect(() => {
    const load = async () => { const data = await fetch(...) }
    load()
  }, [])
  ```
- Używaj React DevTools do debugowania wartości hooków
