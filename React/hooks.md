# 🪝 React Hooks Cheatsheet

Hooki pozwalają używać stanu i innych funkcji Reacta bez pisania klas.

---

## 1. useState

Zarządzanie lokalnym stanem komponentu.

- `const [state, setState] = useState(initialValue);`
- **Tip:** Jeśli nowy stan zależy od poprzedniego, użyj funkcji: `setState(prev => prev + 1)`.

---

## 2. useEffect

Obsługa efektów ubocznych (API, subskrypcje, manipulacja DOM).

- `useEffect(() => { ... }, [dependencies]);`
- **Pusta tablica `[]`:** Uruchamia się tylko raz (przy montowaniu).
- **Brak tablicy:** Uruchamia się przy każdym renderze (unikaj tego!).
- **Cleanup:** Zwróć funkcję, aby posprzątać (np. `return () => clearInterval(id)`).

---

## 3. useContext

Łatwy dostęp do danych w drzewie komponentów bez "prop drilling".

- Pozwala uniknąć przekazywania danych przez wiele poziomów komponentów.
- Przy zmianie wartości w Context Providerze, wszystkie komponenty używające `useContext` renderują się ponownie.

---

## 4. useRef

Dostęp do elementów DOM lub przechowywanie wartości, które nie powodują ponownego renderowania przy zmianie.

- `const inputRef = useRef(null);`
- Dostęp przez `inputRef.current`.

---

## 5. useMemo & useCallback (Optymalizacja)

Służą do zapamiętywania wartości i funkcji, aby zapobiec ich niepotrzebnemu tworzeniu przy każdym renderze.

- **useMemo:** Zapamiętuje **wynik** obliczeń.
  `const memoizedValue = useMemo(() => computeExpensiveValue(a, b), [a, b]);`
- **useCallback:** Zapamiętuje **instancję funkcji**.
  `const memoizedCallback = useCallback(() => { doSomething(a); }, [a]);`

---

## 6. useReducer

Alternatywa dla `useState` dla bardziej złożonej logiki stanu (często używana z Context API).

- Działa podobnie do Reduxa (state, action, reducer).

---

## 💡 Dobre Praktyki (Rules of Hooks)

1. **Tylko na najwyższym poziomie:** Nie wywołuj Hooków wewnątrz pętli, warunków ani funkcji zagnieżdżonych.
2. **Tylko w funkcjach Reacta:** Wywołuj je tylko w komponentach funkcyjnych lub własnych Hookach.
3. **Linter:** Zawsze zwracaj uwagę na ostrzeżenia `eslint-plugin-react-hooks` (brakujące zależności w `useEffect`).

---

## 🛠 Własne Hooki (Custom Hooks)

Możesz tworzyć własne hooki, aby reużywać logikę. Zawsze muszą zaczynać się od słowa `use`.

```javascript
function useWindowSize() {
  const [size, setSize] = useState(window.innerWidth);
  useEffect(() => {
    const handleResize = () => setSize(window.innerWidth);
    window.addEventListener("resize", handleResize);
    return () => window.removeEventListener("resize", handleResize);
  }, []);
  return size;
}
```
