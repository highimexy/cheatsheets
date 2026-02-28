# ✅ Manual Testing Checklist

Checklista do ręcznego testowania przed deployem i podczas code review.

---

## 🚀 Pre-Deploy Checklist (każdy deploy)

```
FUNKCJONALNOŚĆ
□ Główny feature działa zgodnie z wymaganiami
□ Edge cases obsłużone (puste stany, błędne dane, limity)
□ Dane poprawnie zapisują się i odczytują
□ Operacje CRUD działają end-to-end

REGRESJA
□ Feature który był działający wcześniej – nadal działa
□ Powiązane moduły nie zostały zepsute
□ Nie pojawiły się nowe błędy w konsoli (błędy JS, failed requests)
```

---

## 🖥️ UI & Layout

```
RESPONSYWNOŚĆ
□ Mobile (375px – iPhone SE)
□ Tablet (768px – iPad)
□ Desktop (1280px+)
□ Brak overflow / poziomego scrolla na mobile

WIZUALNE
□ Brak urwanych/nakładających się elementów
□ Fonty, kolory i spacing zgodne z design systemem
□ Ikony i obrazki ładują się poprawnie
□ Brak "flash of unstyled content" (FOUC) przy ładowaniu strony

DARK MODE (jeśli wspierany)
□ Wszystkie elementy czytelne w dark mode
□ Brak hardkodowanych białych/czarnych kolorów
```

---

## 📋 Formularze

```
WALIDACJA
□ Pole wymagane – błąd przy próbie submitu pustego pola
□ Format email – błąd przy nieprawidłowym formacie
□ Min/max długość – odpowiedni komunikat błędu
□ Błędy wyświetlają się przy odpowiednim polu (nie na górze strony)
□ Komunikaty błędów są zrozumiałe dla użytkownika (nie "Error 422")

DZIAŁANIE
□ Submit działa (dane trafiają do backendu)
□ Loading state widoczny podczas wysyłania
□ Sukces – odpowiedni feedback (toast, redirect, komunikat)
□ Błąd serwera – czytelny komunikat dla użytkownika
□ Podwójny submit zablokowany (button disabled podczas loading)
□ Po sukcesie formularz resetuje się lub przekierowuje

AUTOFILL & UX
□ Browser autofill działa poprawnie
□ Tab order logiczny (kolejność pól ma sens)
□ Enter w ostatnim polu submituje formularz
```

---

## 🔐 Autoryzacja & Bezpieczeństwo

```
DOSTĘP
□ Niezalogowany użytkownik – redirect do /login przy próbie wejścia na chronioną stronę
□ Zalogowany użytkownik – nie widzi strony logowania (redirect do /dashboard)
□ Role-based access – user nie widzi panelu admina i vice versa

DANE
□ Użytkownik A nie może zobaczyć/edytować danych użytkownika B
□ Token wygasa – sesja kończy się, redirect do logowania
□ Po wylogowaniu – chronione strony niedostępne
```

---

## ⚡ Stany Interfejsu

```
LOADING
□ Skeleton / spinner widoczny podczas pobierania danych
□ Przyciski zablokowane podczas operacji asynchronicznych
□ Strona nie "skacze" gdy dane się ładują (layout shift)

PUSTE STANY (Empty States)
□ Pusta lista – wyświetla sensowny komunikat (nie pustą stronę)
□ Brak wyników wyszukiwania – informacja dla użytkownika
□ Pierwszy raz w aplikacji – onboarding lub placeholder

BŁĘDY
□ Błąd sieci (offline) – czytelny komunikat
□ Błąd 404 – strona not found zamiast białego ekranu
□ Błąd 500 – ogólny komunikat błędu (nie stack trace dla użytkownika)
□ Error boundary działa – błąd w jednym komponencie nie crashuje całej aplikacji
```

---

## ♿ Dostępność (Accessibility)

```
KLAWIATURA
□ Cała nawigacja możliwa tylko klawiaturą (Tab, Enter, Escape, strzałki)
□ Focus visible – widać który element jest aktywny
□ Modal – focus trapped wewnątrz, Escape zamyka
□ Dropdown – strzałki nawigują po opcjach

CZYTNIKI EKRANU
□ Obrazy mają atrybut alt (lub alt="" dla dekoracyjnych)
□ Przyciski z ikonami mają aria-label
□ Formularze – label połączone z inputem (htmlFor / id)
□ Komunikaty błędów powiązane z polem (aria-describedby)

KONTRAST
□ Tekst na tle ma wystarczający kontrast (min. 4.5:1)
□ Disabled elementy rozpoznawalne (nie tylko kolorem)
```

---

## 🌐 Sieć & Wydajność

```
WOLNE ŁĄCZE (throttling w DevTools → Slow 3G)
□ Strona jest używalna podczas ładowania (progressive rendering)
□ Obrazy ładują się bez przesuwania layoutu
□ Spinner/skeleton widoczny, nie biała strona

BŁĘDY SIECI
□ Przerwane połączenie – aplikacja reaguje gracefully
□ Retry logic działa (jeśli zaimplementowane)
□ Offline – strona wyświetla komunikat zamiast kręcić się w nieskończoność
```

---

## 🔍 Testowanie Cross-Browser

```
□ Chrome (główna przeglądarka)
□ Firefox
□ Safari (szczególnie ważne dla iOS)
□ Edge

Najczęstsze problemy Safari:
□ CSS gap w Flexbox (starsze wersje)
□ input[type="date"] wygląd
□ Scroll behavior
□ WebP obrazki (stare wersje)
```

---

## 📱 Mobile-Specific

```
□ Touch targets min. 44x44px (przyciski nie za małe)
□ Brak hover-only interakcji (na mobile nie ma hover)
□ Klawiatura mobilna nie zasłania aktywnego pola
□ Swipe gestures działają (jeśli zaimplementowane)
□ Pinch-to-zoom nie zepsute (viewport meta tag)
□ Notch / safe area obsłużone (iPhone X+)
```

---

## 📊 Dane & Skrajne Przypadki

```
DŁUGIE DANE
□ Długa nazwa użytkownika nie psuje layoutu (truncate/wrap)
□ Długi tekst w buttonie / tabeli
□ Wiele elementów na liście (100+) – wydajność i scrollowanie

SPECJALNE ZNAKI
□ Znaki specjalne w formularzu: <script>, ', ", &, emoji 🎉
□ Polskie znaki: ą, ę, ó, ź, ż, ć, ń, ł
□ Cyfry i daty: rok przestępny, 29 lutego, strefy czasowe

LICZBY BRZEGOWE
□ Wartość 0
□ Wartość ujemna
□ Bardzo duże liczby
□ Wartości null / undefined (graceful handling)
```

---

## 🚦 Przed Każdym PR – Szybka Checklista

```
□ Funkcja działa zgodnie z opisem w tickecie
□ Nie ma błędów w konsoli przeglądarki
□ Responsywność sprawdzona (mobile + desktop)
□ Loading i error states działają
□ Nie zepsułem nic co działało wcześniej
□ Przetestowałem na co najmniej jednej innej przeglądarce
```

---

## 💡 Tips

- Używaj **DevTools → Network → Slow 3G** do symulacji słabego łącza
- **DevTools → Rendering → Paint flashing** pokazuje niepotrzebne re-rendery
- Testuj z **wyłączonym CSS** – czy strona jest nadal użyteczna (dostępność)
- **BrowserStack** lub **LambdaTest** do testów na prawdziwych urządzeniach
- Rób screenshoty/nagrania bugów – ułatwia reproductibility
