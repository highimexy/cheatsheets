# 🏛️ React – Wzorce Projektowe

Sprawdzone wzorce do budowania skalowalnych komponentów.

---

## 📦 Compound Components

Komponenty które dzielą ukryty stan – jak `<select>` i `<option>`.

```tsx
// Implementacja
const TabsContext = createContext<{ active: string; setActive: (v: string) => void } | null>(null)

function Tabs({ children, defaultTab }: { children: ReactNode; defaultTab: string }) {
  const [active, setActive] = useState(defaultTab)
  return (
    <TabsContext.Provider value={{ active, setActive }}>
      {children}
    </TabsContext.Provider>
  )
}

Tabs.Tab = function Tab({ value, children }: { value: string; children: ReactNode }) {
  const ctx = useContext(TabsContext)!
  return (
    <button
      className={ctx.active === value ? 'active' : ''}
      onClick={() => ctx.setActive(value)}
    >
      {children}
    </button>
  )
}

// Użycie – czyste i intuicyjne API
<Tabs defaultTab="info">
  <Tabs.Tab value="info">Informacje</Tabs.Tab>
  <Tabs.Tab value="settings">Ustawienia</Tabs.Tab>
</Tabs>
```

---

## 🎭 Render Props

Przekaż funkcję jako prop – dziel logikę, nie UI.

```tsx
// Komponent z logiką hover
function HoverTracker({
  render,
}: {
  render: (isHovered: boolean) => ReactNode;
}) {
  const [isHovered, setIsHovered] = useState(false);
  return (
    <div
      onMouseEnter={() => setIsHovered(true)}
      onMouseLeave={() => setIsHovered(false)}
    >
      {render(isHovered)}
    </div>
  );
}

// Użycie
<HoverTracker
  render={(hovered) => (
    <button style={{ opacity: hovered ? 1 : 0.7 }}>Najedź na mnie</button>
  )}
/>;
```

> 💡 Dziś częściej zastępowane custom hookami, ale wciąż przydatne w bibliotekach komponentów.

---

## 🪝 Custom Hook zamiast HOC

Zamiast `withAuth(Component)` – użyj hooka:

```tsx
// hook
function useAuth() {
  const { user, isLoading } = useContext(AuthContext)!;
  const isAdmin = user?.role === "admin";
  return { user, isLoading, isAdmin };
}

// Użycie
function AdminPanel() {
  const { user, isAdmin, isLoading } = useAuth();
  if (isLoading) return <Spinner />;
  if (!isAdmin) return <Navigate to="/" />;
  return <div>Panel admina dla {user?.name}</div>;
}
```

---

## 🏭 Container / Presentational Pattern

Oddziel logikę od UI.

```tsx
// 📦 Container – logika, dane, side effecty
function UserListContainer() {
  const { data: users, isLoading } = useQuery({
    queryKey: ["users"],
    queryFn: fetchUsers,
  });
  const handleDelete = (id: string) => deleteUser(id);

  return (
    <UserList
      users={users ?? []}
      isLoading={isLoading}
      onDelete={handleDelete}
    />
  );
}

// 🎨 Presentational – tylko UI, bez logiki
function UserList({ users, isLoading, onDelete }: UserListProps) {
  if (isLoading) return <Skeleton />;
  return (
    <ul>
      {users.map((user) => (
        <li key={user.id}>
          {user.name}
          <button onClick={() => onDelete(user.id)}>Usuń</button>
        </li>
      ))}
    </ul>
  );
}
```

---

## 🛡️ Error Boundary

Łap błędy renderowania – React wymaga klasy (lub react-error-boundary).

```tsx
// npm install react-error-boundary
import { ErrorBoundary } from "react-error-boundary";

function ErrorFallback({ error, resetErrorBoundary }: FallbackProps) {
  return (
    <div>
      <p>Coś poszło nie tak: {error.message}</p>
      <button onClick={resetErrorBoundary}>Spróbuj ponownie</button>
    </div>
  );
}

// Użycie
<ErrorBoundary FallbackComponent={ErrorFallback} onReset={() => refetch()}>
  <UserProfile userId={id} />
</ErrorBoundary>;
```

---

## ⚡ Lazy Loading & Code Splitting

```tsx
import { lazy, Suspense } from "react";

// Załaduj komponent dopiero gdy jest potrzebny
const AdminPanel = lazy(() => import("./pages/AdminPanel"));
const Chart = lazy(() => import("./components/Chart"));

function App() {
  return (
    <Suspense fallback={<LoadingSpinner />}>
      <Routes>
        <Route path="/admin" element={<AdminPanel />} />
      </Routes>
    </Suspense>
  );
}
```

---

## 🧩 Composition over Configuration

Zamiast `<Modal type="confirm" showClose size="lg">`:

```tsx
// ✅ Kompozycja – elastyczna
<Modal>
  <Modal.Header>
    <Modal.Title>Potwierdź</Modal.Title>
    <Modal.CloseButton />
  </Modal.Header>
  <Modal.Body>Czy na pewno chcesz usunąć?</Modal.Body>
  <Modal.Footer>
    <Button variant="ghost">Anuluj</Button>
    <Button variant="danger">Usuń</Button>
  </Modal.Footer>
</Modal>
```

---

## 💡 Tips

- Zanim stworzysz nowy kontekst – sprawdź czy `props drilling` na 2-3 poziomy to realny problem
- `useMemo` na komponencie prezentacyjnym + `React.memo()` = zero zbędnych re-renderów
- Trzymaj komponenty poniżej 200 linii – jeśli więcej, podziel
- Jeśli prop-lista przekracza 5 props, rozważ obiekt konfiguracyjny lub Compound Components
