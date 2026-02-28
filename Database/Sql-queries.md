# 🗄️ Database – SQL Queries

Praktyczny SQL dla developerów – JOIN, indeksy, optymalizacja, PostgreSQL.

---

## 🔗 JOIN – Rodzaje i Kiedy Używać

```sql
-- INNER JOIN – tylko pasujące rekordy w obu tabelach
SELECT u.name, o.total
FROM users u
INNER JOIN orders o ON u.id = o.user_id;
-- Zwraca: tylko userów którzy mają zamówienia

-- LEFT JOIN – wszyscy z lewej + pasujące z prawej (NULL jeśli brak)
SELECT u.name, COUNT(o.id) AS order_count
FROM users u
LEFT JOIN orders o ON u.id = o.user_id
GROUP BY u.id, u.name;
-- Zwraca: WSZYSCY userzy, order_count=0 dla tych bez zamówień

-- RIGHT JOIN – rzadko używany, można zastąpić LEFT JOIN ze zamienionymi tabelami

-- FULL OUTER JOIN – wszystkie rekordy z obu tabel
SELECT u.name, o.total
FROM users u
FULL OUTER JOIN orders o ON u.id = o.user_id;

-- SELF JOIN – tabela z samą sobą (np. hierarchia)
SELECT e.name AS employee, m.name AS manager
FROM employees e
LEFT JOIN employees m ON e.manager_id = m.id;

-- Wiele JOINów
SELECT
  u.name,
  o.id AS order_id,
  p.name AS product,
  oi.quantity
FROM users u
JOIN orders o ON o.user_id = u.id
JOIN order_items oi ON oi.order_id = o.id
JOIN products p ON p.id = oi.product_id
WHERE o.created_at >= NOW() - INTERVAL '30 days';
```

---

## 📊 Agregacje i Grupowanie

```sql
-- GROUP BY z warunkiem na grupie (HAVING zamiast WHERE)
SELECT
  u.id,
  u.name,
  COUNT(o.id)   AS total_orders,
  SUM(o.total)  AS total_spent,
  AVG(o.total)  AS avg_order_value,
  MAX(o.total)  AS largest_order
FROM users u
LEFT JOIN orders o ON o.user_id = u.id
GROUP BY u.id, u.name
HAVING COUNT(o.id) > 5          -- tylko userzy z >5 zamówieniami
ORDER BY total_spent DESC;

-- Window Functions – agregacja bez grupowania (nie redukuje wierszy)
SELECT
  id,
  name,
  salary,
  AVG(salary) OVER (PARTITION BY department) AS dept_avg,
  RANK()      OVER (PARTITION BY department ORDER BY salary DESC) AS rank,
  ROW_NUMBER() OVER (ORDER BY created_at) AS row_num,
  LAG(salary)  OVER (ORDER BY created_at) AS prev_salary,  -- poprzedni wiersz
  LEAD(salary) OVER (ORDER BY created_at) AS next_salary   -- następny wiersz
FROM employees;
```

---

## 🔍 Subqueries i CTE

```sql
-- Subquery w WHERE
SELECT * FROM users
WHERE id IN (
  SELECT DISTINCT user_id FROM orders
  WHERE total > 1000
);

-- CTE (Common Table Expression) – czytelniejsze niż subqueries
WITH high_value_orders AS (
  SELECT user_id, SUM(total) AS total_spent
  FROM orders
  WHERE status = 'completed'
  GROUP BY user_id
  HAVING SUM(total) > 5000
),
ranked_users AS (
  SELECT
    u.*,
    hvo.total_spent,
    RANK() OVER (ORDER BY hvo.total_spent DESC) AS rank
  FROM users u
  JOIN high_value_orders hvo ON hvo.user_id = u.id
)
SELECT * FROM ranked_users WHERE rank <= 10;

-- Recursive CTE – np. hierarchia kategorii
WITH RECURSIVE category_tree AS (
  SELECT id, name, parent_id, 0 AS depth
  FROM categories
  WHERE parent_id IS NULL            -- korzeń
  UNION ALL
  SELECT c.id, c.name, c.parent_id, ct.depth + 1
  FROM categories c
  JOIN category_tree ct ON c.parent_id = ct.id
)
SELECT * FROM category_tree ORDER BY depth, name;
```

---

## ✏️ INSERT / UPDATE / DELETE

```sql
-- INSERT z ON CONFLICT (upsert)
INSERT INTO user_preferences (user_id, theme, language)
VALUES ('123', 'dark', 'pl')
ON CONFLICT (user_id)
DO UPDATE SET
  theme = EXCLUDED.theme,
  language = EXCLUDED.language,
  updated_at = NOW();

-- UPDATE z JOIN (PostgreSQL)
UPDATE orders o
SET status = 'shipped'
FROM shipments s
WHERE s.order_id = o.id
  AND s.shipped_at IS NOT NULL
  AND o.status = 'processing';

-- DELETE z RETURNING – zwróć usunięte rekordy
DELETE FROM sessions
WHERE expires_at < NOW()
RETURNING id, user_id;

-- Bulk INSERT
INSERT INTO tags (name, slug)
VALUES
  ('JavaScript', 'javascript'),
  ('TypeScript', 'typescript'),
  ('React', 'react')
ON CONFLICT (slug) DO NOTHING;
```

---

## ⚡ Indeksy – Wydajność

```sql
-- Podstawowy indeks (B-tree – domyślny)
CREATE INDEX idx_users_email ON users(email);

-- Bez blokowania tabeli (produkcja!)
CREATE INDEX CONCURRENTLY idx_users_email ON users(email);

-- Złożony indeks – kolejność kolumn ma znaczenie!
-- Przydatny dla: WHERE user_id = ? AND created_at > ?
CREATE INDEX idx_orders_user_created ON orders(user_id, created_at DESC);

-- Partial index – tylko aktywne rekordy (mniejszy, szybszy)
CREATE INDEX idx_users_active ON users(email) WHERE is_active = true;

-- Index dla pełnotekstowego wyszukiwania
CREATE INDEX idx_products_search ON products USING gin(to_tsvector('polish', name || ' ' || description));

-- Sprawdź czy indeks jest używany
EXPLAIN ANALYZE SELECT * FROM users WHERE email = 'jan@example.com';
-- Szukaj: "Index Scan" vs "Seq Scan" (sequence scan = brak indeksu)

-- Nieużywane indeksy (spowalniają INSERT/UPDATE)
SELECT schemaname, tablename, indexname, idx_scan
FROM pg_stat_user_indexes
WHERE idx_scan = 0
ORDER BY schemaname, tablename;
```

---

## 🔎 EXPLAIN ANALYZE – Diagnoza

```sql
EXPLAIN ANALYZE
SELECT u.name, COUNT(o.id)
FROM users u
LEFT JOIN orders o ON o.user_id = u.id
GROUP BY u.id;

-- Co szukać w wyniku:
-- "Seq Scan"      → brak indeksu, skanuje całą tabelę ❌
-- "Index Scan"    → używa indeksu ✅
-- "Hash Join"     → efektywny JOIN dla dużych tabel ✅
-- "Nested Loop"   → może być wolny przy dużych tabelach ⚠️
-- rows=X          → estymata; jeśli bardzo różna od actual → ANALYZE tabela
-- cost=X..Y       → koszt planera (nie czas, ale proporcja)
-- actual time=X   → rzeczywisty czas w ms ← to jest ważne
```

---

## 🛠️ PostgreSQL – Przydatne Komendy

```sql
-- Rozmiar tabel
SELECT
  tablename,
  pg_size_pretty(pg_total_relation_size(tablename::regclass)) AS size
FROM pg_tables
WHERE schemaname = 'public'
ORDER BY pg_total_relation_size(tablename::regclass) DESC;

-- Aktywne połączenia
SELECT pid, usename, application_name, state, query
FROM pg_stat_activity
WHERE state = 'active';

-- Zabij długo trwające zapytanie
SELECT pg_terminate_backend(pid)
FROM pg_stat_activity
WHERE duration > INTERVAL '5 minutes';

-- Statystyki zapytań (wymaga pg_stat_statements)
SELECT query, calls, mean_exec_time, total_exec_time
FROM pg_stat_statements
ORDER BY total_exec_time DESC
LIMIT 10;

-- Vacuum / Analyze (utrzymanie wydajności)
VACUUM ANALYZE users;
VACUUM FULL users;    -- pełna defragmentacja (blokuje tabelę!)
```

---

## 📐 Wzorce – Paginacja

```sql
-- OFFSET (prosta, ale wolna przy dużych offsetach)
SELECT * FROM posts
ORDER BY created_at DESC
LIMIT 20 OFFSET 40;     -- strona 3, po 20 elementów

-- Cursor-based (keyset pagination) – wydajniejsza dla dużych zbiorów
SELECT * FROM posts
WHERE created_at < '2024-01-15T10:00:00'  -- ostatni element poprzedniej strony
ORDER BY created_at DESC
LIMIT 20;
```

---

## 💡 Tips

- **EXPLAIN ANALYZE** zawsze przed i po dodaniu indeksu – sprawdź efekt
- Indeks na kolumnie gdzie 90%+ wartości jest identycznych (np. `is_active`) mało pomaga
- `CONCURRENTLY` przy CREATE INDEX w produkcji – nie blokuje tabel
- Zbyt wiele indeksów spowalnia `INSERT`/`UPDATE` – każdy indeks musi być zaktualizowany
- Używaj **CTE** zamiast zagnieżdżonych subqueries – bardziej czytelne i często szybsze
- `RETURNING` eliminuje potrzebę osobnego `SELECT` po `INSERT`/`UPDATE`/`DELETE`
