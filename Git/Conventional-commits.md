# 📝 Conventional Commits

Standard ułatwiający czytanie historii zmian oraz automatyzację wersjonowania projektów.

---

## 🏗️ Struktura Commita

Każdy commit powinien trzymać się schematu:
`<typ>[opcjonalny zakres]: <krótki opis>`

---

## 📌 Najważniejsze Typy (Types)

- **feat:** Nowa funkcjonalność dla użytkownika (np. `feat: add dark mode to tailwind guide`).
- **fix:** Naprawa błędu w kodzie lub dokumentacji (np. `fix: correct typo in docker commands`).
- **docs:** Zmiany wyłącznie w dokumentacji (np. `docs: update readme structure`).
- **style:** Zmiany nie wpływające na działanie kodu (formatowanie, brakujące średniki, białe znaki).
- **refactor:** Zmiana kodu, która nie jest ani poprawką błędu, ani nową funkcją.
- **perf:** Poprawa wydajności kodu.
- **chore:** Zadania administracyjne, aktualizacja zależności, zmiany w `.gitignore` (np. `chore: add node_modules to gitignore`).
- **test:** Dodanie lub poprawa istniejących testów.

---

## 🛠️ Przykłady w praktyce

### Prosty wpis

`docs: add conventional commits guide`

### Z określeniem zakresu (Scope)

`feat(ui): add chakra-ui button variants`

### Zmiana krytyczna (Breaking Change)

Dodajemy wykrzyknik przed dwukropkiem, aby zasygnalizować zmianę psującą wsteczną kompatybilność:
`refactor!: change folder structure for all cheatsheets`

---

## 💡 Dobre Praktyki

1.  **Tryb rozkazujący:** Pisz `add`, nie `added`; `fix`, nie `fixed`.
2.  **Mała litera:** Opis po dwukropku zaczynaj od małej litery.
3.  **Zwięzłość:** Pierwsza linia nie powinna przekraczać 50-72 znaków.
4.  **Atomowość:** Jeden commit = jedna logiczna zmiana.
