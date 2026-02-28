# 🧩 Go Interfaces Cheatsheet

Interfejsy w Go to zestawy sygnatur metod. Są kluczem do tworzenia elastycznego, testowalnego i modułowego kodu.

---

## 1. Definicja i Implementacja

W Go nie deklarujesz, że struktura "implementuje" interfejs. Dzieje się to automatycznie.

```go
// Definicja
type Shape interface {
    Area() float64
}

// Implementacja (struktura)
type Circle struct {
    Radius float64
}

// Circle implementuje Shape, bo ma metodę Area()
func (c Circle) Area() float64 {
    return 3.14 * c.Radius * c.Radius
}
```
