# 🐳 Docker Cheatsheet

Szybka ściąga z najczęściej używanych komend i pojęć w ekosystemie Docker.

---

## 🏗️ Zarządzanie Obrazami (Images)

Obrazy to "szablony" Twoich kontenerów.

- `docker images` – lista wszystkich obrazów na dysku.
- `docker build -t nazwa-obrazu .` – budowanie obrazu z pliku `Dockerfile` w bieżącym folderze.
- `docker rmi <image_id>` – usuwanie konkretnego obrazu.
- `docker pull <image_name>` – pobranie obrazu z Docker Hub (np. `docker pull node:20`).

---

## 🚀 Zarządzanie Kontenerami (Containers)

Kontener to uruchomiona instancja obrazu.

- `docker ps` – lista uruchomionych kontenerów.
- `docker ps -a` – lista wszystkich kontenerów (również zatrzymanych).
- `docker run -d -p 3000:3000 --name moj-apka nazwa-obrazu` – uruchomienie kontenera w tle (`-d`), z mapowaniem portów (`-p`) i własną nazwą (`--name`).
- `docker stop <container_id>` – zatrzymanie kontenera.
- `docker rm <container_id>` – usunięcie kontenera.
- `docker logs -f <container_id>` – podgląd logów w czasie rzeczywistym.
- `docker exec -it <container_id> sh` – wejście do wnętrza działającego kontenera (terminal).

---

## 📂 Docker Compose

Narzędzie do definiowania i uruchamiania wielokontenerowych aplikacji (np. App + DB).

- `docker-compose up` – uruchomienie usług z pliku `docker-compose.yml`.
- `docker-compose up -d` – uruchomienie w tle.
- `docker-compose down` – zatrzymanie i usunięcie wszystkich kontenerów, sieci i obrazów zdefiniowanych w pliku.
- `docker-compose restart` – restart wszystkich usług.

---

## 🧹 Sprzątanie (Pruning)

Docker potrafi szybko zająć dużo miejsca na dysku.

- `docker system prune` – usuwa nieużywane kontenery, sieci i obrazy "dangling" (bez tagów).
- `docker system prune -a` – usuwa **wszystkie** nieużywane obrazy i kontenery (czyści dysk do zera).
- `docker volume prune` – usuwa nieużywane wolumeny (ostrożnie, to usuwa dane!).

---

## 💡 Tip: Przydatne flagi

- `--rm` – usuwa kontener automatycznie zaraz po jego zatrzymaniu (dobre do szybkich testów).
- `-v /host/path:/container/path` – montowanie wolumenu (synchronizacja plików między komputerem a kontenerem).

---

## 📄 Przykładowy Dockerfile (Node.js)

```dockerfile
FROM node:20-alpine
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
EXPOSE 3000
CMD ["npm", "run", "dev"]
```
