# 🐙 Git Cheatsheet

Zbiór najużyteczniejszych komend Git, zoptymalizowany pod nowoczesny workflow, pracę w zespole i unikanie typowych błędów w historii commitów.

---

## 🔧 Konfiguracja i Inicjalizacja

Ustawienia, które wykonujesz raz na nowym środowisku lub projekcie.

- `git init` – inicjalizacja nowego repozytorium w bieżącym folderze.
- `git clone <url>` – sklonowanie istniejącego repozytorium na dysk.
- `git config --global user.name "Twoje Imię"` – globalna nazwa autora commitów.
- `git config --global user.email "twoj@email.com"` – globalny email autora.

---

## 🚀 Codzienny Workflow (The Loop)

Komendy używane wielokrotnie każdego dnia podczas pracy nad feature'ami.

- `git status` – sprawdzenie stanu plików (untracked, modified, staged).
- `git add .` – dodanie wszystkich zmian do poczekalni (staging).
- `git commit -m "opis"` – zatwierdzenie zmian (używaj [Conventional Commits](Conventional-commits.md)).
- `git push origin <branch>` – wysłanie Twoich lokalnych commitów na serwer.
- `git pull origin <branch>` – pobranie i automatyczne scalenie zmian z serwera.

---

## 🌿 Gałęzie i Przełączanie (Branching)

Zarządzanie odizolowanymi wersjami kodu.

- `git branch` – lista wszystkich lokalnych gałęzi.
- `git checkout -b <name>` – stworzenie nowej gałęzi i natychmiastowe przejście na nią.
- `git checkout <name>` – przełączenie się na istniejącą już gałąź.
- `git merge <name>` – scalenie wskazanej gałęzi z tą, na której aktualnie jesteś.
- `git branch -d <name>` – usunięcie gałęzi (użyj `-D`, aby wymusić usunięcie niescalonej gałęzi).

---

## 🛠️ Naprawianie Błędów i Cofanie

Ratunek, gdy coś pójdzie nie tak (zanim zrobisz `push`).

- `git commit --amend -m "nowy opis"` – poprawka opisu lub zawartości ostatniego commita.
- `git reset --soft HEAD~1` – cofnięcie commita, ale zachowanie zmian w plikach (staging).
- `git reset --hard HEAD~1` – całkowite usunięcie ostatniego commita i zmian (**nieodwracalne!**).
- `git checkout -- <file>` – przywrócenie pliku do stanu z ostatniego commita (cofnięcie edycji).
- `git pull origin <branch> --rebase` – czystsza historia: pobiera zmiany i układa Twoje commity na górze.

---

## 🌐 Zarządzanie Remote

Praca ze zdalnymi serwerami (GitHub, GitLab, Bitbucket).

- `git remote -v` – podgląd adresów URL podpiętego repozytorium.
- `git remote add origin <url>` – podpięcie zdalnego serwera do lokalnego folderu.
- `git remote set-url origin <url>` – zmiana adresu istniejącego już `origin`.

---

## 🔍 Inspekcja i Historia

Sprawdzanie co, kto i kiedy zmienił w kodzie.

- `git log --oneline` – czytelna, skrócona historia wszystkich commitów.
- `git diff` – podgląd zmian w plikach, które nie trafiły jeszcze do stagingu.
- `git show <commit_id>` – szczegółowy podgląd zmian wprowadzonych w konkretnym commicie.

---

## 💡 Pro-tip: Przydatne Aliasy

Dodaj do `~/.bashrc` lub `~/.zshrc`, aby pisać mniej:

```bash
alias gs="git status"
alias gp="git push"
alias gl="git log --oneline --graph --all"
alias gaa="git add ."
alias gc="git commit -m"
```
