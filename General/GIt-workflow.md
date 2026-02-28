# 🌿 Git Workflow

Praca z gitem w zespole – branche, commity, pull requesty.

---

## 🏗️ Strategia Branchy (GitHub Flow)

```
main ──────────────────────────────────────── (zawsze deployowalny)
  │
  ├── feat/user-authentication ──── PR ──► main
  ├── fix/login-redirect-loop  ──── PR ──► main
  └── refactor/user-service    ──── PR ──► main
```

```bash
# Standardowy cykl pracy
git checkout main
git pull origin main                    # zawsze zacznij od aktualnego main
git checkout -b feat/payment-integration

# ... praca, commity ...

git push origin feat/payment-integration
# Otwórz Pull Request na GitHubie
```

---

## ✍️ Conventional Commits

Format: `type(scope): opis` — opis małą literą, bez kropki na końcu.

```bash
# Nowa funkcja
git commit -m "feat(auth): add JWT refresh token rotation"
git commit -m "feat(cart): implement discount code validation"

# Naprawa buga
git commit -m "fix(login): redirect to dashboard after OAuth login"
git commit -m "fix(api): handle null response from payment provider"

# Refactoring (bez zmiany funkcjonalności)
git commit -m "refactor(user): extract email validation to separate service"

# Testy
git commit -m "test(auth): add edge cases for token expiration"

# Dokumentacja
git commit -m "docs(api): add OpenAPI spec for /users endpoints"

# Konfiguracja, narzędzia, dependencje
git commit -m "chore: update dependencies to latest versions"
git commit -m "chore(ci): add GitHub Actions workflow for tests"

# Stylowanie (formatowanie, brak zmian logiki)
git commit -m "style: fix eslint warnings in user module"

# BREAKING CHANGE
git commit -m "feat(api)!: change response format for /users endpoint

BREAKING CHANGE: response.data is now wrapped in { items, total }"
```

### Typy Commitów

| Typ        | Kiedy używać                         |
| ---------- | ------------------------------------ |
| `feat`     | Nowa funkcjonalność                  |
| `fix`      | Naprawa buga                         |
| `refactor` | Refactoring bez zmiany zachowania    |
| `test`     | Dodawanie/poprawianie testów         |
| `docs`     | Dokumentacja                         |
| `chore`    | Narzędzia, konfiguracja, dependencje |
| `style`    | Formatowanie, brak zmian logiki      |
| `perf`     | Poprawa wydajności                   |
| `ci`       | Zmiany w CI/CD                       |
| `revert`   | Cofnięcie commita                    |

---

## 🔄 Codzienny Workflow

```bash
# Rano – synchronizacja z main
git checkout main
git pull origin main
git checkout moja-galaz
git rebase main                         # przenieś zmiany na aktualny main

# Podczas pracy – małe commity
git add src/components/UserForm.tsx
git commit -m "feat(user): add form validation for email field"

# Przed pushiem – squash jeśli za dużo WIP commitów
git rebase -i HEAD~3                    # interaktywny rebase ostatnich 3 commitów

# Push
git push origin feat/user-form
git push --force-with-lease             # po rebase (bezpieczniejszy force push)
```

---

## 🔀 Rebase vs Merge

```bash
# REBASE – czysta, liniowa historia (zalecane dla feature branchy)
git rebase main
# Wynik: Twoje commity "przesunięte" na czubek main

# MERGE – zachowuje historię rozgałęzień
git merge main
# Wynik: dodatkowy "merge commit"

# Zasada: rebase lokalnie, merge przez PR na GitHubie
```

---

## 🚑 Ratowanie Sytuacji

```bash
# Cofnij ostatni commit (zachowaj zmiany w staging)
git reset --soft HEAD~1

# Cofnij ostatni commit (zachowaj zmiany w working directory)
git reset HEAD~1

# Cofnij ostatni commit (USUŃ zmiany – nieodwracalne!)
git reset --hard HEAD~1

# Cofnij konkretny commit (bezpieczne – tworzy nowy commit)
git revert <hash>

# Przywróć plik do stanu z ostatniego commita
git checkout -- src/components/UserForm.tsx

# Zapisz zmiany na później (stash)
git stash
git stash pop                           # przywróć
git stash list                          # lista stashy
git stash pop stash@{2}                 # przywróć konkretny

# Znajdź który commit wprowadził buga
git bisect start
git bisect bad                          # aktualny commit ma buga
git bisect good v1.0.0                  # ten był ok
# Git przechodzi przez historię, ty oceniasz: git bisect good/bad

# Wyciągnij konkretny commit z innej gałęzi
git cherry-pick <hash>
```

---

## 🧹 Porządki

```bash
# Usuń lokalne branche już zmergowane do main
git branch --merged main | grep -v "main" | xargs git branch -d

# Usuń zdalne branche już usunięte z GitHuba
git remote prune origin

# Oba naraz
git fetch --prune

# Sprawdź co jest w staging przed commitem
git diff --staged

# Historia commitów z grafem
git log --oneline --graph --all
```

---

## ⚙️ Konfiguracja `.gitconfig`

```ini
[user]
  name = Jan Kowalski
  email = jan@example.com

[core]
  editor = code --wait          # VS Code jako edytor commitów
  autocrlf = input              # LF na macOS/Linux

[pull]
  rebase = true                 # git pull = git pull --rebase

[push]
  autoSetupRemote = true        # automatycznie trackuj remote przy pierwszym push

[alias]
  lg = log --oneline --graph --all --decorate
  undo = reset HEAD~1
  aliases = config --get-regexp alias
```

---

## 💡 Tips

- **Małe, częste commity** – łatwiejsze do review i revertowania niż jeden gigantyczny commit
- **Nigdy nie rób force push na main** – `git push --force-with-lease` zamiast `--force`
- **Commit messages** piszą historię projektu – za rok docenisz konkretne opisy
- **`.gitignore` generator** – gitignore.io generuje gotowe pliki dla Twojego stacku
- Użyj `git add -p` zamiast `git add .` – staging konkretnych fragmentów pliku
