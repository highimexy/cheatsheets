# 🔒 Linux Permissions

Zarządzanie dostępem do plików i bezpieczeństwem kluczy.

---

## 🛠️ Chmod (Uprawnienia)

- `chmod 600 ~/.ssh/id_rsa` – wymagane uprawnienia dla klucza prywatnego SSH.
- `chmod 755 <folder>` – standard: właściciel (wszystko), grupa/inni (odczyt/wejście).
- `chmod -R 777 <folder>` – **ZŁA PRAKTYKA** (używaj tylko w ostateczności lokalnie).

## 👤 Chown (Właściciele)

- `chown user:group <file>` – zmiana właściciela i grupy pliku.
- `chown -R www-data:www-data /var/www/html` – nadanie uprawnień serwerowi WWW.

## 🛡️ Sudo i Userzy

- `sudo !!` – uruchomienie ostatniej komendy z uprawnieniami root.
- `whoami` – sprawdzenie na jakim użytkowniku aktualnie pracujesz.
