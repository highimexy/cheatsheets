# 🌐 Linux Networking

Niezbędnik do debugowania połączeń sieciowych i zarządzania portami.

---

## 🔍 Porty i Połączenia

- `sudo lsof -i :3000` – sprawdza, jaki proces zajmuje port 3000.
- `fuser -k 3000/tcp` – zabicie procesu blokującego port 3000.
- `netstat -tulpn` – lista wszystkich nasłuchujących portów.

## 📡 Testowanie API i Serwerów

- `curl -I <url>` – pobranie tylko nagłówków HTTP (status 200/404/500).
- `telnet <host> <port>` – sprawdzenie czy dany port na zdalnym serwerze jest otwarty.
- `ping <host>` – sprawdzenie opóźnień i dostępności hosta.

## 🌍 IP i SSH

- `curl ifconfig.me` – sprawdzenie Twojego publicznego adresu IP.
- `ip addr` – wyświetla lokalne adresy IP.
- `ssh-copy-id user@host` – dodanie klucza SSH do logowania bez hasła.
