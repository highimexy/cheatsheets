# 🐳 Docker – Ściągawka

Konteneryzacja aplikacji i zarządzanie środowiskami.

---

## 🔧 Podstawowe Komendy

### Obrazy (Images)

```bash
docker images                        # lista lokalnych obrazów
docker pull node:20-alpine           # pobierz obraz z Docker Hub
docker build -t my-app:1.0 .         # zbuduj obraz z Dockerfile w bieżącym folderze
docker build -t my-app:1.0 -f Dockerfile.prod .  # z konkretnym Dockerfile
docker rmi my-app:1.0                # usuń obraz
docker image prune                   # usuń nieużywane obrazy
```

### Kontenery (Containers)

```bash
docker run my-app                    # uruchom kontener
docker run -d my-app                 # w tle (detached)
docker run -d -p 3000:3000 my-app    # mapuj port host:kontener
docker run -d --name api my-app      # nadaj nazwę
docker run --rm my-app               # usuń kontener po zatrzymaniu

docker ps                            # lista działających kontenerów
docker ps -a                         # wszystkie (łącznie ze zatrzymanymi)
docker stop <id/name>                # zatrzymaj kontener (graceful)
docker kill <id/name>                # zatrzymaj natychmiast
docker rm <id/name>                  # usuń zatrzymany kontener
docker rm -f <id/name>               # zatrzymaj i usuń naraz
```

### Debugowanie

```bash
docker logs <id/name>                # logi kontenera
docker logs -f <id/name>             # logi w czasie rzeczywistym (follow)
docker logs --tail 100 <id/name>     # ostatnie 100 linii logów

docker exec -it <id/name> sh         # wejdź do kontenera (sh)
docker exec -it <id/name> bash       # wejdź do kontenera (bash)
docker exec <id/name> ls /app        # wykonaj komendę bez wchodzenia

docker inspect <id/name>             # szczegółowe info o kontenerze (JSON)
docker stats                         # zużycie CPU/RAM w czasie rzeczywistym
```

---

## 📄 Dockerfile – Wzorce

### Node.js / Next.js (produkcja)

```dockerfile
# ---- Build Stage ----
FROM node:20-alpine AS builder
WORKDIR /app

COPY package*.json ./
RUN npm ci --only=production

COPY . .
RUN npm run build

# ---- Production Stage ----
FROM node:20-alpine AS runner
WORKDIR /app

ENV NODE_ENV=production

# Kopiuj tylko to co potrzebne
COPY --from=builder /app/.next/standalone ./
COPY --from=builder /app/.next/static ./.next/static
COPY --from=builder /app/public ./public

EXPOSE 3000
CMD ["node", "server.js"]
```

### Node.js API (Express / Fastify)

```dockerfile
FROM node:20-alpine
WORKDIR /app

COPY package*.json ./
RUN npm ci --only=production

COPY . .
RUN npm run build

EXPOSE 4000
USER node                          # nie uruchamiaj jako root!
CMD ["node", "dist/index.js"]
```

### Golang

```dockerfile
FROM golang:1.22-alpine AS builder
WORKDIR /app
COPY go.mod go.sum ./
RUN go mod download
COPY . .
RUN CGO_ENABLED=0 go build -o server ./cmd/api

FROM alpine:3.19
WORKDIR /app
COPY --from=builder /app/server .
EXPOSE 8080
CMD ["./server"]
```

### Najważniejsze Instrukcje

```dockerfile
FROM node:20-alpine          # bazowy obraz
WORKDIR /app                 # ustaw katalog roboczy
COPY package*.json ./        # kopiuj pliki (pattern → dest)
RUN npm ci                   # wykonaj komendę podczas budowania
ENV NODE_ENV=production      # zmienna środowiskowa
EXPOSE 3000                  # dokumentuje port (nie publishuje!)
USER node                    # zmień użytkownika (bezpieczeństwo)
CMD ["node", "index.js"]     # komenda startowa (można nadpisać)
ENTRYPOINT ["node"]          # sztywna komenda (trudniej nadpisać)
ARG BUILD_VERSION            # argument tylko podczas budowania
HEALTHCHECK --interval=30s CMD wget -qO- http://localhost:3000/health || exit 1
```

### .dockerignore

```
node_modules
.next
dist
.git
.env*
*.log
README.md
```

---

## 🐙 Docker Compose

### Struktura `docker-compose.yml`

```yaml
services:
  app:
    build:
      context: .
      dockerfile: Dockerfile
    ports:
      - "3000:3000"
    environment:
      - NODE_ENV=development
      - DATABASE_URL=postgresql://postgres:password@db:5432/mydb
    env_file:
      - .env.local
    volumes:
      - .:/app # hot reload w dev
      - /app/node_modules # zachowaj node_modules kontenera
    depends_on:
      db:
        condition: service_healthy # czekaj aż DB będzie gotowa
    restart: unless-stopped

  db:
    image: postgres:16-alpine
    environment:
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: password
      POSTGRES_DB: mydb
    volumes:
      - postgres_data:/var/lib/postgresql/data # dane persystentne
    ports:
      - "5432:5432" # tylko lokalnie, usuń w prod
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U postgres"]
      interval: 5s
      timeout: 5s
      retries: 5

  redis:
    image: redis:7-alpine
    ports:
      - "6379:6379"

  nginx:
    image: nginx:alpine
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - ./nginx.conf:/etc/nginx/nginx.conf:ro
    depends_on:
      - app

volumes:
  postgres_data:
```

### Komendy Docker Compose

```bash
docker compose up                    # uruchom wszystkie serwisy
docker compose up -d                 # w tle
docker compose up --build            # przebuduj obrazy przed uruchomieniem
docker compose up app db             # uruchom tylko wybrane serwisy

docker compose down                  # zatrzymaj i usuń kontenery
docker compose down -v               # + usuń volumes (dane!)

docker compose logs -f app           # logi konkretnego serwisu
docker compose exec app sh           # wejdź do kontenera serwisu
docker compose exec db psql -U postgres  # psql bezpośrednio

docker compose ps                    # status serwisów
docker compose restart app           # restart serwisu
docker compose pull                  # pobierz najnowsze obrazy
```

---

## 🌐 Sieci (Networks)

```yaml
# Izolacja serwisów
services:
  app:
    networks:
      - frontend
      - backend
  db:
    networks:
      - backend # db niedostępna z frontendu!

networks:
  frontend:
  backend:
    internal: true # brak dostępu do internetu
```

```bash
docker network ls                    # lista sieci
docker network inspect bridge        # szczegóły sieci
```

---

## 🗂️ Volumes

```bash
docker volume ls                     # lista volumes
docker volume inspect postgres_data  # szczegóły volume
docker volume rm postgres_data       # usuń volume
docker volume prune                  # usuń nieużywane volumes
```

---

## 🧹 Czyszczenie

```bash
# Usuń wszystko co nieużywane (bezpieczne)
docker system prune

# Usuń wszystko łącznie z volumes (OSTROŻNIE!)
docker system prune -a --volumes

# Sprawdź ile zajmuje Docker
docker system df
```

---

## 🛠️ Przydatne Wzorce

### Override dla developmentu

```yaml
# docker-compose.yml – baza (produkcja)
# docker-compose.override.yml – automatycznie mergowany w dev

# docker-compose.override.yml
services:
  app:
    volumes:
      - .:/app
      - /app/node_modules
    command: npm run dev
    environment:
      - DEBUG=true
```

### Health Check w aplikacji

```ts
// Express – endpoint do healthcheck
app.get("/health", (req, res) => {
  res.json({ status: "ok", uptime: process.uptime() });
});
```

### Wieloetapowy build z cache

```dockerfile
# Warstwy z rzadko zmieniającymi się plikami NAJPIERW
COPY package*.json ./        # 1. dependencies (rzadko zmieniane)
RUN npm ci                   # 2. install (cache jeśli package.json bez zmian)
COPY . .                     # 3. kod aplikacji (często zmieniane)
RUN npm run build            # 4. build
```

---

## 💡 Tips

- Używaj `node:20-alpine` zamiast `node:20` – 5x mniejszy obraz
- `npm ci` zamiast `npm install` w Dockerfile – deterministyczna instalacja
- Nigdy nie trzymaj `.env` w obrazie – przekazuj przez `env_file` lub `environment`
- `depends_on` z `condition: service_healthy` zapobiega błędom "DB not ready"
- W produkcji nigdy nie publikuj portów bazy danych (usuń `ports` z serwisu `db`)
- `USER node` w Dockerfile – nigdy nie uruchamiaj kontenera jako root
