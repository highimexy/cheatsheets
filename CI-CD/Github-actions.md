# 🚀 GitHub Actions – CI/CD

Automatyzacja testów, buildów i deploymentów.

---

## 🏗️ Struktura Workflow

```yaml
# .github/workflows/ci.yml
name: CI

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  test:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v4

      - uses: actions/setup-node@v4
        with:
          node-version: "20"
          cache: "npm"

      - run: npm ci
      - run: npm run lint
      - run: npm run type-check
      - run: npm test
```

---

## ✅ Pełne CI Pipeline (Next.js / Node)

```yaml
name: CI

on:
  pull_request:
    branches: [main, develop]

jobs:
  quality:
    name: Lint & Types
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: "20"
          cache: "npm"
      - run: npm ci
      - run: npm run lint
      - run: npm run type-check

  test:
    name: Unit & Integration Tests
    runs-on: ubuntu-latest
    needs: quality # uruchom po quality

    services:
      postgres: # baza na potrzeby testów
        image: postgres:16
        env:
          POSTGRES_PASSWORD: testpass
          POSTGRES_DB: testdb
        options: >-
          --health-cmd pg_isready
          --health-interval 10s
          --health-timeout 5s
          --health-retries 5
        ports:
          - 5432:5432

    env:
      DATABASE_URL: postgresql://postgres:testpass@localhost:5432/testdb
      JWT_SECRET: test-secret-min-32-characters-long

    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: "20"
          cache: "npm"
      - run: npm ci
      - run: npx prisma migrate deploy
      - run: npm test -- --coverage
      - uses: actions/upload-artifact@v4
        with:
          name: coverage
          path: coverage/

  e2e:
    name: E2E Tests
    runs-on: ubuntu-latest
    needs: test

    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: "20"
          cache: "npm"
      - run: npm ci
      - run: npx playwright install --with-deps chromium
      - run: npm run build
      - run: npx playwright test
      - uses: actions/upload-artifact@v4
        if: failure() # zapisz raport tylko przy błędzie
        with:
          name: playwright-report
          path: playwright-report/
```

---

## 🚢 Deploy na Vercel / Railway

```yaml
name: Deploy

on:
  push:
    branches: [main]

jobs:
  deploy:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v4

      # Vercel – przez Vercel CLI
      - name: Deploy to Vercel
        run: npx vercel --prod --token=${{ secrets.VERCEL_TOKEN }}
        env:
          VERCEL_ORG_ID: ${{ secrets.VERCEL_ORG_ID }}
          VERCEL_PROJECT_ID: ${{ secrets.VERCEL_PROJECT_ID }}
```

---

## 🐳 Build i Push Docker Image

```yaml
name: Build & Push Docker

on:
  push:
    branches: [main]
    tags: ["v*"]

jobs:
  docker:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v4

      - name: Login to Docker Hub
        uses: docker/login-action@v3
        with:
          username: ${{ secrets.DOCKERHUB_USERNAME }}
          password: ${{ secrets.DOCKERHUB_TOKEN }}

      - name: Build and push
        uses: docker/build-push-action@v5
        with:
          context: .
          push: true
          tags: |
            myuser/myapp:latest
            myuser/myapp:${{ github.sha }}
          cache-from: type=gha # cache warstw Docker w GitHub Actions
          cache-to: type=gha,mode=max
```

---

## ♻️ Reużywalne Elementy

### Composite Action

```yaml
# .github/actions/setup-node/action.yml
name: Setup Node.js
description: Checkout + Node + npm ci

runs:
  using: composite
  steps:
    - uses: actions/checkout@v4
    - uses: actions/setup-node@v4
      with:
        node-version: '20'
        cache: 'npm'
    - run: npm ci
      shell: bash

# Użycie w workflow:
- uses: ./.github/actions/setup-node
```

### Reusable Workflow

```yaml
# .github/workflows/reusable-test.yml
on:
  workflow_call:
    inputs:
      node-version:
        type: string
        default: '20'
    secrets:
      DATABASE_URL:
        required: true

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: ./.github/actions/setup-node
      - run: npm test

# Wywołanie z innego workflow:
jobs:
  run-tests:
    uses: ./.github/workflows/reusable-test.yml
    secrets:
      DATABASE_URL: ${{ secrets.DATABASE_URL }}
```

---

## ⚙️ Przydatne Wzorce

### Cache dependencies

```yaml
- uses: actions/setup-node@v4
  with:
    node-version: "20"
    cache: "npm" # automatyczny cache node_modules

# Lub manualnie (pnpm, yarn)
- uses: actions/cache@v4
  with:
    path: ~/.pnpm-store
    key: ${{ runner.os }}-pnpm-${{ hashFiles('**/pnpm-lock.yaml') }}
```

### Matrix – testuj na wielu wersjach

```yaml
jobs:
  test:
    strategy:
      matrix:
        node: ["18", "20", "22"]
        os: [ubuntu-latest, windows-latest]
    runs-on: ${{ matrix.os }}
    steps:
      - uses: actions/setup-node@v4
        with:
          node-version: ${{ matrix.node }}
```

### Concurrency – anuluj poprzednie runy

```yaml
concurrency:
  group: ${{ github.workflow }}-${{ github.ref }}
  cancel-in-progress: true # anuluj poprzedni run dla tego samego PR
```

### Warunkowe kroki

```yaml
- name: Deploy tylko na main
  if: github.ref == 'refs/heads/main'
  run: npm run deploy

- name: Notify na błąd
  if: failure()
  uses: actions/github-script@v7
  with:
    script: |
      github.rest.issues.createComment({
        issue_number: context.issue.number,
        body: '❌ CI failed!'
      })
```

---

## 🔐 Secrets i Zmienne

```yaml
# Użycie secretów (Settings → Secrets → Actions)
env:
  DATABASE_URL: ${{ secrets.DATABASE_URL }}
  API_KEY: ${{ secrets.API_KEY }}

# Zmienne środowiskowe (nie-wrażliwe)
env:
  NODE_ENV: production
  PORT: 3000

# Automatyczne zmienne GitHub
${{ github.sha }}          # hash commita
${{ github.ref }}          # refs/heads/main
${{ github.actor }}        # nazwa użytkownika
${{ github.repository }}   # owner/repo
```

---

## 💡 Tips

- **`npm ci`** zamiast `npm install` w CI – deterministyczna instalacja z lock file
- **`needs:`** do kolejkowania jobów – nie deployuj jeśli testy nie przeszły
- `if: failure()` + upload artifacts – zawsze zbieraj raporty błędów testów E2E
- **Branch protection rules** w GitHubie – zablokuj merge bez zielonego CI
- `concurrency: cancel-in-progress: true` – oszczędza minuty Actions przy wielu PR
- Limit: darmowe konto = 2000 min/miesiąc – optymalizuj czas za pomocą cache
