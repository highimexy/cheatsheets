# 🌿 Git – Komendy

Najczęściej używane komendy w codziennej pracy.

---

## 🏁 Setup

```bash
git config --global user.name "Jan Kowalski"
git config --global user.email "jan@example.com"
git config --global core.editor "code --wait"
git config --global pull.rebase true          # git pull = pull --rebase
git config --global push.autoSetupRemote true # auto-track remote przy push

git init                                       # nowe repo
git clone <url>                                # sklonuj repo
git clone <url> --depth 1                      # shallow clone (szybszy CI)
```

---

## 📸 Podstawowe Operacje

```bash
git status                        # stan working directory
git diff                          # zmiany nieindeksowane
git diff --staged                 # zmiany w staging (przed commitem)
git diff main...HEAD              # wszystkie zmiany względem main

git add <file>                    # dodaj plik do staging
git add .                         # dodaj wszystko
git add -p                        # interaktywny staging (fragment po fragmencie)

git commit -m "feat: add login"   # commit z wiadomością
git commit --amend --no-edit      # dodaj do ostatniego commita (przed pushem!)
git commit --amend -m "nowa wiadomosc"  # zmień wiadomość ostatniego commita
```

---

## 🌿 Branche

```bash
git branch                        # lista lokalnych branchy
git branch -a                     # lista lokalnych + zdalnych
git branch feat/login             # stwórz branch (bez przełączania)
git checkout -b feat/login        # stwórz i przełącz
git switch -c feat/login          # nowszy odpowiednik checkout -b

git checkout main                 # przełącz na branch
git switch main                   # nowszy odpowiednik

git branch -d feat/login          # usuń zmergowany branch
git branch -D feat/login          # usuń na siłę (niezmergowany)
git push origin --delete feat/login  # usuń zdalny branch
```

---

## 🔄 Synchronizacja z Remote

```bash
git fetch                         # pobierz zmiany (bez merge)
git fetch --prune                 # + usuń lokalne ślady usuniętych zdalnych branchy

git pull                          # fetch + merge (lub rebase jeśli skonfigurowane)
git pull origin main              # z konkretnego brancha

git push                          # wypchnij zmiany
git push -u origin feat/login     # pierwszy push + ustaw upstream
git push --force-with-lease       # force push (bezpieczny – sprawdza czy ktoś nie pushował)
```

---

## 🔀 Merge & Rebase

```bash
# Merge – zachowuje historię gałęzi
git merge feat/login              # merge do aktualnego brancha
git merge --no-ff feat/login      # zawsze tworzy merge commit
git merge --squash feat/login     # squashuje wszystkie commity w jeden

# Rebase – czysta, liniowa historia
git rebase main                   # przenieś aktualny branch na czubek main
git rebase -i HEAD~3              # interaktywny rebase – squash/edit/reorder
git rebase --abort                # porzuć rebase w razie konfliktów
git rebase --continue             # kontynuuj po rozwiązaniu konfliktu
```

---

## 🚑 Cofanie Zmian

```bash
# Cofnij plik do stanu z HEAD (nieodwracalne!)
git checkout -- <file>
git restore <file>                # nowszy odpowiednik

# Usuń z stagingu (zachowaj zmiany)
git reset HEAD <file>
git restore --staged <file>       # nowszy odpowiednik

# Cofnij ostatni commit
git reset --soft HEAD~1           # zachowaj zmiany w staging
git reset HEAD~1                  # zachowaj zmiany w working dir
git reset --hard HEAD~1           # USUŃ zmiany (nieodwracalne!)

# Bezpieczne cofnięcie – tworzy nowy commit
git revert <hash>
git revert HEAD                   # cofnij ostatni commit
```

---

## 📦 Stash

```bash
git stash                         # zapisz zmiany na później
git stash push -m "WIP: login"    # z opisem
git stash pop                     # przywróć ostatni stash + usuń go
git stash apply stash@{1}         # przywróć konkretny (bez usuwania)
git stash list                    # lista wszystkich stashy
git stash drop stash@{0}          # usuń konkretny stash
git stash clear                   # usuń wszystkie
```

---

## 🔍 Historia i Szukanie

```bash
git log                           # pełna historia
git log --oneline                 # jedna linia na commit
git log --oneline --graph --all   # drzewo wszystkich branchy
git log -p <file>                 # historia zmian w pliku
git log --author="Jan"            # commity konkretnego autora
git log --since="2 weeks ago"

git show <hash>                   # szczegóły commita
git blame <file>                  # kto i kiedy zmienił każdą linię

# Szukanie
git grep "TODO"                   # szukaj w plikach
git log --all --grep="login"      # szukaj w wiadomościach commitów
git log -S "functionName"         # kiedy dana linia pojawiła się/zniknęła

# Znajdź który commit wprowadził buga
git bisect start
git bisect bad                    # aktualny commit ma buga
git bisect good v1.0.0            # ten był ok
git bisect reset                  # zakończ bisect
```

---

## 🍒 Cherry-pick & Inne

```bash
git cherry-pick <hash>            # skopiuj commit na aktualny branch
git cherry-pick <hash1>..<hash2>  # zakres commitów

git tag v1.0.0                    # utwórz tag
git tag v1.0.0 <hash>             # tag na konkretnym commicie
git push origin --tags            # wypchnij tagi

git submodule add <url>           # dodaj submodule
git submodule update --init       # inicjalizuj submoduły po clone
```

---

## 🧹 Porządki

```bash
# Usuń zmergowane lokalne branche
git branch --merged main | grep -v "main\|master\|dev" | xargs git branch -d

# Usuń nieśledzone pliki i foldery
git clean -fd                     # usuń untracked pliki i katalogi
git clean -n                      # dry run – pokaż co zostanie usunięte

# Skompresuj repo
git gc --aggressive

# Sprawdź rozmiar repo
git count-objects -vH
```

---

## ⚙️ Aliasy (dodaj do `~/.gitconfig`)

```ini
[alias]
  lg    = log --oneline --graph --all --decorate
  st    = status -s
  undo  = reset HEAD~1
  oops  = commit --amend --no-edit
  aliases = config --get-regexp alias
```

```bash
# Użycie:
git lg
git st
git undo
```

---

## 💡 Tips

- `git add -p` zamiast `git add .` — commituj świadomie, fragment po fragmencie
- `--force-with-lease` zamiast `--force` — sprawdza czy ktoś nie pushował w międzyczasie
- `git reflog` — historia wszystkich operacji, ratunek gdy "zgubisz" commity
- `git stash push -m` z opisem — stash bez opisu to zagadka po tygodniu
