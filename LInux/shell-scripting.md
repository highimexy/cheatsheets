# 🐚 Shell Scripting & Text Processing

Automatyzacja zadań i przeszukiwanie logów.

---

## 🔍 Praca z tekstem (Grep, Tail, Less)

- `tail -f <file>` – śledzenie logów w czasie rzeczywistym.
- `grep -rnw "." -e "pattern"` – szukanie frazy we wszystkich plikach w folderze.
- `less <file>` – przeglądanie dużych plików bez obciążania RAM.

## 📜 Podstawy skryptów .sh

```bash
#!/bin/bash
# Przykład prostego skryptu backupu
NAME="backup_$(date +%F).tar.gz"
tar -czf $NAME ./src
echo "Backup saved as $NAME"
```
