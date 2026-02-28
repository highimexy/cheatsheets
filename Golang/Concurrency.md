# ⚙️ Golang – Współbieżność

Goroutines, kanały i wzorce concurrency w Go.

---

## 🚀 Goroutines

```go
// Uruchom funkcję współbieżnie
go func() {
    fmt.Println("Hello from goroutine")
}()

// Z argumentami
go processUser(userID)

// ⚠️ main() nie czeka na goroutines – używaj sync.WaitGroup lub kanałów
func main() {
    go doWork()
    // Program może zakończyć się zanim goroutine się uruchomi!
}
```

### sync.WaitGroup – czekaj na zakończenie

```go
func main() {
    var wg sync.WaitGroup

    for i := 0; i < 5; i++ {
        wg.Add(1)
        go func(id int) {
            defer wg.Done()         // zawsze defer – nawet przy panice
            processItem(id)
        }(i)                        // przekaż i jako argument – unikaj closure trap
    }

    wg.Wait()                       // zablokuj aż wszystkie zakończą
    fmt.Println("Wszystkie gotowe")
}
```

---

## 📡 Kanały (Channels)

```go
// Tworzenie
ch := make(chan int)          // niebuforowany – blokuje nadawcę do odbioru
ch := make(chan int, 10)      // buforowany – blokuje gdy bufor pełny
ch := make(chan struct{})      // sygnalizacja (zero alloc)

// Wysyłanie i odbieranie
ch <- 42                      // wyślij (blokuje jeśli niebuforowany)
value := <-ch                 // odbierz (blokuje jeśli pusty)
value, ok := <-ch             // ok=false gdy kanał zamknięty i pusty

// Zamknięcie
close(ch)                     // nadawca zamyka kanał
// ⚠️ Nigdy nie zamykaj ze strony odbiorcy
// ⚠️ Wysyłanie na zamknięty kanał = panic

// Iteracja po kanale aż do zamknięcia
for value := range ch {
    fmt.Println(value)
}
```

### Pipeline Pattern

```go
func generate(nums ...int) <-chan int {
    out := make(chan int)
    go func() {
        defer close(out)
        for _, n := range nums {
            out <- n
        }
    }()
    return out
}

func square(in <-chan int) <-chan int {
    out := make(chan int)
    go func() {
        defer close(out)
        for n := range in {
            out <- n * n
        }
    }()
    return out
}

func main() {
    // Połącz etapy pipeline
    nums := generate(2, 3, 4)
    squares := square(nums)
    for result := range squares {
        fmt.Println(result)     // 4, 9, 16
    }
}
```

---

## 🎛️ Select

```go
// Czekaj na wiele kanałów jednocześnie
select {
case msg := <-ch1:
    fmt.Println("z ch1:", msg)
case msg := <-ch2:
    fmt.Println("z ch2:", msg)
case <-time.After(5 * time.Second):
    fmt.Println("timeout!")
case <-ctx.Done():
    fmt.Println("anulowano:", ctx.Err())
default:
    fmt.Println("nic nie czeka")  // non-blocking
}
```

---

## 🔒 Mutex – Ochrona Współdzielonych Danych

```go
type SafeCounter struct {
    mu    sync.Mutex
    count int
}

func (c *SafeCounter) Increment() {
    c.mu.Lock()
    defer c.mu.Unlock()
    c.count++
}

func (c *SafeCounter) Value() int {
    c.mu.RLock()           // RWMutex – wielu czytelników, jeden pisarz
    defer c.mu.RUnlock()
    return c.count
}
```

### sync.RWMutex – gdy czytasz częściej niż piszesz

```go
type Cache struct {
    mu   sync.RWMutex
    data map[string]string
}

func (c *Cache) Get(key string) (string, bool) {
    c.mu.RLock()                    // wiele goroutines może czytać jednocześnie
    defer c.mu.RUnlock()
    val, ok := c.data[key]
    return val, ok
}

func (c *Cache) Set(key, value string) {
    c.mu.Lock()                     // wyłączny dostęp do zapisu
    defer c.mu.Unlock()
    c.data[key] = value
}
```

---

## 🌐 Context – Anulowanie i Timeout

```go
// Timeout
ctx, cancel := context.WithTimeout(context.Background(), 5*time.Second)
defer cancel()                      // zawsze defer cancel() – zapobiega wyciekom

// Anulowanie ręczne
ctx, cancel := context.WithCancel(context.Background())
go func() {
    time.Sleep(2 * time.Second)
    cancel()                        // anuluj z innej goroutine
}()

// Użycie w funkcji
func fetchData(ctx context.Context, url string) ([]byte, error) {
    req, err := http.NewRequestWithContext(ctx, "GET", url, nil)
    if err != nil {
        return nil, err
    }
    resp, err := http.DefaultClient.Do(req)
    // jeśli ctx zostanie anulowany – request zostanie przerwany
    ...
}

// Sprawdzenie anulowania w pętli
for {
    select {
    case <-ctx.Done():
        return ctx.Err()
    default:
        doWork()
    }
}
```

---

## 🏊 Worker Pool

```go
func workerPool(jobs <-chan int, results chan<- int, workerCount int) {
    var wg sync.WaitGroup

    for i := 0; i < workerCount; i++ {
        wg.Add(1)
        go func() {
            defer wg.Done()
            for job := range jobs {         // odbiera aż kanał zamknięty
                results <- process(job)
            }
        }()
    }

    go func() {
        wg.Wait()
        close(results)                      // zamknij results gdy workers skończą
    }()
}

func main() {
    jobs := make(chan int, 100)
    results := make(chan int, 100)

    workerPool(jobs, results, 5)           // 5 workerów

    for i := 0; i < 50; i++ {
        jobs <- i
    }
    close(jobs)                             // sygnał że nie będzie więcej pracy

    for result := range results {
        fmt.Println(result)
    }
}
```

---

## ⚛️ sync.Once – Wykonaj raz (Singleton)

```go
type Database struct {
    conn *sql.DB
}

var (
    instance *Database
    once     sync.Once
)

func GetDB() *Database {
    once.Do(func() {
        instance = &Database{conn: connectDB()}
    })
    return instance
}
```

---

## 🔢 atomic – Lekkie Operacje bez Mutex

```go
import "sync/atomic"

var counter int64

atomic.AddInt64(&counter, 1)            // bezpieczny increment
value := atomic.LoadInt64(&counter)     // bezpieczny odczyt
atomic.StoreInt64(&counter, 0)          // bezpieczny zapis
atomic.CompareAndSwapInt64(&counter, old, new)  // CAS
```

---

## 🚦 errgroup – WaitGroup + Error Handling

```go
import "golang.org/x/sync/errgroup"

func fetchAll(ctx context.Context, urls []string) error {
    g, ctx := errgroup.WithContext(ctx)

    for _, url := range urls {
        url := url                          // loop variable capture
        g.Go(func() error {
            return fetch(ctx, url)          // anuluje pozostałe jeśli błąd
        })
    }

    return g.Wait()                         // zwraca pierwszy błąd
}
```

---

## 💡 Tips & Pułapki

```go
// ❌ Loop variable capture – klasyczny błąd
for i := 0; i < 5; i++ {
    go func() { fmt.Println(i) }()  // wszystkie drukują 5!
}

// ✅ Przekaż jako argument
for i := 0; i < 5; i++ {
    go func(i int) { fmt.Println(i) }(i)
}

// ✅ Go 1.22+ – loop variable capture naprawione automatycznie
```

- **Nie komunikuj przez współdzielenie pamięci – współdziel pamięć przez komunikację** (Go proverb)
- `defer cancel()` zawsze po `WithTimeout`/`WithCancel` – zapobiega wyciekom goroutines
- Goroutine leak = goroutine która nigdy się nie kończy – używaj `ctx.Done()` w pętlach
- `race detector`: `go test -race ./...` – wykrywa data races w testach
