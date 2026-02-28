# ⚡ Performance – Web Vitals i Optymalizacja

Mierzenie i poprawa wydajności aplikacji webowych.

---

## 📊 Core Web Vitals (Google)

```
LCP – Largest Contentful Paint
  Co mierzy: czas renderowania największego elementu (obraz, nagłówek)
  Cel:       < 2.5s  ✅   2.5-4s  ⚠️   > 4s  ❌

FID/INP – Interaction to Next Paint (zastąpił FID w 2024)
  Co mierzy: czas odpowiedzi na interakcję użytkownika
  Cel:       < 200ms ✅   200-500ms ⚠️   > 500ms ❌

CLS – Cumulative Layout Shift
  Co mierzy: nieoczekiwane przesunięcia layoutu podczas ładowania
  Cel:       < 0.1  ✅   0.1-0.25  ⚠️   > 0.25  ❌

TTFB – Time To First Byte
  Co mierzy: czas odpowiedzi serwera
  Cel:       < 800ms ✅
```

### Mierzenie

```ts
// Web Vitals library
import { onLCP, onINP, onCLS, onTTFB } from "web-vitals";

onLCP((metric) => console.log("LCP:", metric.value));
onCLS((metric) => console.log("CLS:", metric.value));
onINP((metric) => console.log("INP:", metric.value));

// Wysyłka do analytics
function sendToAnalytics(metric) {
  fetch("/api/vitals", {
    method: "POST",
    body: JSON.stringify(metric),
  });
}
onLCP(sendToAnalytics);
```

---

## 🖼️ Optymalizacja Obrazów

```tsx
// Next.js Image – automatyczna optymalizacja
import Image from 'next/image'

// ✅ Zawsze podawaj width/height lub fill – zapobiega CLS
<Image
  src="/hero.jpg"
  alt="Hero"
  width={1200}
  height={600}
  priority          // LCP image – ładuj natychmiast bez lazy load
  placeholder="blur"
  blurDataURL="..."
/>

// Ikony i małe obrazy
<Image src="/icon.svg" alt="" width={24} height={24} />

// Full-width (cover)
<div style={{ position: 'relative', aspectRatio: '16/9' }}>
  <Image src="/bg.jpg" alt="Background" fill style={{ objectFit: 'cover' }} />
</div>
```

```html
<!-- Vanilla HTML – nowoczesne formaty -->
<picture>
  <source srcset="image.avif" type="image/avif" />
  <source srcset="image.webp" type="image/webp" />
  <img
    src="image.jpg"
    alt="..."
    loading="lazy"
    decoding="async"
    width="800"
    height="600"
  />
</picture>
```

---

## 📦 Bundle Size

```bash
# Analiza bundle Next.js
npm install -D @next/bundle-analyzer

# next.config.ts
const withBundleAnalyzer = require('@next/bundle-analyzer')({
  enabled: process.env.ANALYZE === 'true',
})
module.exports = withBundleAnalyzer({})

# Uruchom analizę
ANALYZE=true npm run build
```

```ts
// ✅ Dynamic import – code splitting
const HeavyChart = dynamic(() => import('./HeavyChart'), {
  loading: () => <Skeleton />,
  ssr: false,              // wyłącz SSR dla komponentów browser-only
})

// ✅ Import tylko potrzebnych funkcji (tree shaking)
import { format } from 'date-fns'                  // ✅ tylko format
import * as dateFns from 'date-fns'               // ❌ cała biblioteka

import { debounce } from 'lodash-es'              // ✅ ES modules = tree-shakable
import _ from 'lodash'                            // ❌ cały lodash (~70kb)
```

---

## ⚛️ React Performance

```tsx
// 1. React.memo – nie re-renderuj jeśli props się nie zmieniły
const UserCard = React.memo(function UserCard({ user }: UserCardProps) {
  return <div>{user.name}</div>;
});

// 2. useMemo – memoizuj ciężkie obliczenia
const filteredUsers = useMemo(
  () => users.filter((u) => u.role === activeRole && u.name.includes(search)),
  [users, activeRole, search],
);

// 3. useCallback – stabilna referencja funkcji (dla React.memo dzieci)
const handleDelete = useCallback((id: string) => {
  setUsers((prev) => prev.filter((u) => u.id !== id));
}, []);

// 4. Wirtualizacja długich list
import { useVirtualizer } from "@tanstack/react-virtual";

function VirtualList({ items }: { items: Item[] }) {
  const parentRef = useRef<HTMLDivElement>(null);
  const virtualizer = useVirtualizer({
    count: items.length,
    getScrollElement: () => parentRef.current,
    estimateSize: () => 60,
  });

  return (
    <div ref={parentRef} style={{ height: "500px", overflow: "auto" }}>
      <div style={{ height: virtualizer.getTotalSize() }}>
        {virtualizer.getVirtualItems().map((item) => (
          <div
            key={item.key}
            style={{ transform: `translateY(${item.start}px)` }}
          >
            {items[item.index].name}
          </div>
        ))}
      </div>
    </div>
  );
}
```

---

## 🌐 Network & Caching

```ts
// Next.js fetch caching
// Cache na zawsze (statyczne dane)
fetch("/api/config", { cache: "force-cache" });

// Bez cache (zawsze fresh)
fetch("/api/live-data", { cache: "no-store" });

// Rewaliduj co 60 sekund
fetch("/api/posts", { next: { revalidate: 60 } });
```

```ts
// TanStack Query – inteligentny cache po stronie klienta
const { data } = useQuery({
  queryKey: ["users", page],
  queryFn: () => fetchUsers(page),
  staleTime: 5 * 60 * 1000, // dane świeże przez 5 min
  gcTime: 10 * 60 * 1000, // usuń z cache po 10 min bezczynności
  placeholderData: keepPreviousData, // brak migotania przy zmianie strony
});
```

---

## 🗄️ Baza Danych

```sql
-- Sprawdź wolne zapytania (PostgreSQL)
SELECT query, mean_exec_time, calls
FROM pg_stat_statements
ORDER BY mean_exec_time DESC
LIMIT 10;

-- EXPLAIN ANALYZE – plan zapytania z czasami
EXPLAIN ANALYZE SELECT * FROM users WHERE email = 'jan@example.com';

-- Dodaj index gdy filtrujesz/sortujesz po kolumnie
CREATE INDEX CONCURRENTLY idx_users_email ON users(email);
CREATE INDEX CONCURRENTLY idx_orders_user_created ON orders(user_id, created_at DESC);

-- Partial index – tylko aktywni użytkownicy
CREATE INDEX idx_users_active_email ON users(email) WHERE is_active = true;
```

```ts
// Prisma – unikaj N+1 przez include
// ❌ N+1: osobne query dla każdego usera
const users = await prisma.user.findMany();
for (const user of users) {
  const orders = await prisma.order.findMany({ where: { userId: user.id } });
}

// ✅ Jeden query z JOIN
const users = await prisma.user.findMany({
  include: { orders: { take: 5, orderBy: { createdAt: "desc" } } },
});
```

---

## 💡 Tips

- **DevTools → Performance tab** – nagrywaj i analizuj rendering, JS execution
- **Lighthouse** – audyt w Chrome DevTools (Performance, Accessibility, SEO)
- **Network throttling** – testuj na Slow 3G żeby poczuć co czuje użytkownik na słabym łączu
- **Preconnect** do zewnętrznych domen: `<link rel="preconnect" href="https://fonts.googleapis.com">`
- `React.StrictMode` renderuje dwa razy w dev – to celowe, nie optymalizuj pod to
- Mierz przed optymalizacją – przedwczesna optymalizacja to źródło wielu problemów
