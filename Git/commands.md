# 🐙 Git Cheatsheet

Zbiór najużyteczniejszych komend Git, podzielony na kategorie.

---

## 🔧 Konfiguracja i Inicjalizacja

- `git init` – inicjalizacja nowego repozytorium w bieżącym folderze.
- `git clone <url>` – sklonowanie istniejącego repozytorium.
- `git config --global user.name "Twoje Imię"` – ustawienie nazwy użytkownika.
- `git config --global user.email "twoj@email.com"` – ustawienie adresu email.

---

## 🚀 Codzienny Workflow

- `git status` – sprawdzenie stanu plików (untracked, modified, staged).
- `git add .` – dodanie wszystkich zmian do poczekalni (staging).
- `git commit -m "opis zmian"` – zatwierdzenie zmian z krótkim opisem.
- `git push origin <branch>` – wysłanie zmian do zdalnego repozytorium.
- `git pull origin <branch>` – pobranie i scalenie zmian ze zdalnego repozytorium.

---

## 🌐 Zarządzanie Remote (Zdalne repozytoria)

- `git remote -v` – wyświetlenie listy podpiętych zdalnych repozytoriów.
- `git remote add origin <url>` – podpięcie nowego adresu zdalnego repozytorium.
- `git remote set-url origin <url>` – zmiana adresu istniejącego repozytorium `origin`.
- `git remote remove origin` – odpięcie zdalnego repozytorium.

---

## 🌿 Gałęzie (Branching)

- `git branch` – lista lokalnych gałęzi.
- `git checkout -b <nazwa-galazi>` – stworzenie nowej gałęzi i przełączenie się na nią.
- `git checkout <nazwa-galazi>` – przełączenie się na inną gałąź.
- `git merge <nazwa-galazi>` – scalenie wskazanej gałęzi z obecną.
- `git branch -d <nazwa-galazi>` – usunięcie lokalnej gałęzi.

---

## 🛠 Naprawianie błędów i Cofanie

- `git commit --amend -m "nowy opis"` – zmiana opisu ostatniego (niezpushowanego) commita.
- `git reset --soft HEAD~1` – cofnięcie ostatniego commita, ale pozostawienie zmian w plikach.
- `git reset --hard HEAD~1` – całkowite usunięcie ostatniego commita i zmian w plikach (**uwaga: nieodwracalne!**).
- `git checkout -- <file>` – cofnięcie zmian w konkretnym pliku do stanu z ostatniego commita.
- `git pull origin <branch> --rebase` – pobranie zmian i "przestawienie" Twoich lokalnych commitów na koniec (pozwala uniknąć zbędnych merge-commitów).

---

## 🔍 Logi i Historia

- `git log --oneline` – skrócona historia commitów.
- `git log -p <file>` – historia zmian w konkretnym pliku.
- `git diff` – podgląd zmian, które nie zostały jeszcze dodane do poczekalni.
