# 🐹 Go Concurrency: The Ultimate Guide

Współbieżność w Go to nie tylko `go func()`. To filozofia oparta na modelu CSP:

> "Don't communicate by sharing memory; share memory by communicating."

---

## 1. Goroutines (Lekkie wątki)

- **Uruchomienie:** `go f(x, y)` – tworzy nowy stos (ok. 2KB), zarządzany przez scheduler Go (nie OS).
- **Zasada "Goroutine Leak":** Zawsze wiedz, jak i kiedy Twoja goroutine się zakończy. Jeśli nie ma warunku wyjścia, zablokuje pamięć na zawsze.

---

## 2. Channels (Przesyłanie danych)

Kanały to jedyny bezpieczny sposób na przekazywanie danych między goroutines bez Mutexów.

### Typy kanałów:

- **Unbuffered (Niesynchronizowane):** `make(chan int)`. Blokuje nadawcę, dopóki ktoś nie odbierze (i odwrotnie). Gwarantuje synchronizację.
- **Buffered (Buforowane):** `make(chan int, 10)`. Nie blokuje nadawcy, dopóki bufor nie jest pełny. Używaj ostrożnie – może maskować problemy z wydajnością.

### Kluczowe operacje:

- **Zamykanie:** `close(ch)`. Zamykaj tylko po stronie **nadawcy**. Odbiorca może sprawdzić stan: `v, ok := <-ch` (`ok == false` oznacza zamknięty kanał).
- **For-range:** `for msg := range ch { ... }` – pętla kończy się automatycznie, gdy kanał zostanie zamknięty.

---

## 3. Select (Multiplexing)

`select` pozwala czekać na wiele kanałów jednocześnie.

```go
select {
case msg := <-ch1:
    // Obsługa wiadomości
case ch2 <- result:
    // Wysłanie wyniku
case <-time.After(5 * time.Second):
    // Timeout - ratuje przed zawieszeniem aplikacji
default:
    // Wykonywane, gdy nic innego nie jest gotowe (non-blocking)
}
```
