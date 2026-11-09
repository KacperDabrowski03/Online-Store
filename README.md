# Sklep internetowy drukarni

Praktyczna część pracy licencjackiej „Projekt i implementacja sklepu internetowego z wykorzystaniem frameworków Java Spring Boot i Angular oraz bazy danych PostgreSQL” (KUL, Informatyka).

Sklep sprzedaje pieczątki i akcesoria (cena za sztukę, stan magazynowy) oraz banery i wydruki wielkoformatowe (cena liczona z wymiarów i materiału).

## Stan projektu

| Etap | Stan |
|---|---|
| 0. Plan techniczny | gotowy – [docs/01-plan-techniczny.md](docs/01-plan-techniczny.md) |
| 1. Baza danych | gotowa i przetestowana – [docs/02-baza-danych.md](docs/02-baza-danych.md) |
| 2. Backend (Spring Boot) | gotowy – [docs/03-backend.md](docs/03-backend.md) |
| 3. Frontend (Angular) | gotowy – [docs/04-frontend.md](docs/04-frontend.md) |
| 4. Testy końcowe | zakończone: 223 testy automatyczne zaliczone (baza 23, backend 99, frontend 43, E2E 14, Postman 44) na Linuksie oraz na Windows 11 z Dockerem (PostgreSQL 17, Testcontainers) – [docs/05-testy.md](docs/05-testy.md), bezpieczeństwo – [docs/06-bezpieczenstwo.md](docs/06-bezpieczenstwo.md) |
| 5. Dokumentacja końcowa | zakończona: listingi do pracy – [docs/07-listingi.md](docs/07-listingi.md), diagramy – [docs/diagramy](docs/diagramy), kryteria ukończenia i mapa rozdziałów pracy – [docs/08-podsumowanie.md](docs/08-podsumowanie.md) |

## Wymagania

- Docker Desktop (Windows) lub Docker Engine + Compose (Linux)
- Java 21 (Maven nie jest potrzebny – backend zawiera Maven Wrapper)
- Node.js 22 LTS (≥ 22.22.3) lub 24 LTS (≥ 24.15.0)

## Uruchomienie bazy danych

```bash
cp .env.example .env          # Windows PowerShell: Copy-Item .env.example .env
docker compose up -d postgres
```

Tabele i dane demonstracyjne tworzy backend przy pierwszym starcie (Flyway). Testy bazy i ręczne uruchamianie migracji – [docs/02-baza-danych.md, punkt 11](docs/02-baza-danych.md#11-uruchomienie-bazy).

## Uruchomienie backendu

```bash
cd backend
./mvnw spring-boot:run        # Windows: mvnw.cmd spring-boot:run
```

API: http://localhost:8080/api (np. http://localhost:8080/api/produkty). Opis endpointów i testów: [docs/03-backend.md](docs/03-backend.md).

## Uruchomienie frontendu

```bash
cd frontend
npm install                   # jednorazowo
npm start                     # http://localhost:4200
```

Sklep: http://localhost:4200. Zapytania `/api/**` trafiają do backendu przez proxy (`frontend/proxy.conf.json`).

Przy pierwszym poleceniu `ng`/`npm start` Angular CLI może zapytać o udostępnianie anonimowych statystyk („Would you like to share pseudonymous usage data…”) – można odpowiedzieć `N`.

## Testy

```bash
cd backend
./mvnw verify                 # backend: testy jednostkowe i integracyjne (Testcontainers – wymaga Dockera)

cd frontend
npm test                      # frontend: testy jednostkowe (Vitest); w terminalu tryb obserwowania zmian – koniec: q lub Ctrl+C,
                              # jednorazowo: npm test -- --watch=false
npx playwright install chromium   # jednorazowo
npm run e2e                   # testy end-to-end w przeglądarce (wymagają działającej bazy i backendu)

cd ..
npx newman run docs/postman/drukarnia.postman_collection.json -e docs/postman/drukarnia-lokalnie.postman_environment.json
                              # testy REST API (Postman/Newman) – wymagają działającego backendu z danymi demo
```

Kolejność pełnej regresji, testy bazy danych i wyniki: [docs/05-testy.md](docs/05-testy.md). Kolekcję można też zaimportować do aplikacji Postman – [docs/postman/README.md](docs/postman/README.md).

## Wdrożenie produkcyjne

Profil `prod` (`SPRING_PROFILES_ACTIVE=prod`) wymaga zmiennych `DB_URL`, `DB_UZYTKOWNIK`, `DB_HASLO` i `JWT_SEKRET` i nie ładuje danych demonstracyjnych. Lista kontrolna (HTTPS, nagłówek CSP, pierwszy administrator): [docs/06-bezpieczenstwo.md, sekcja 4](docs/06-bezpieczenstwo.md#4-wdrożenie-produkcyjne--lista-kontrolna).

## Dokumentacja

| Dokument | Zawartość |
|---|---|
| [01-plan-techniczny.md](docs/01-plan-techniczny.md) | cel i zakres, architektura, wersje technologii, role, decyzje, plan etapów |
| [02-baza-danych.md](docs/02-baza-danych.md) | ERD, tabele, relacje, normalizacja, transakcje, indeksy, testy bazy |
| [03-backend.md](docs/03-backend.md) | struktura, profile, encje JPA, bezpieczeństwo, REST API, składanie zamówienia |
| [04-frontend.md](docs/04-frontend.md) | architektura Angulara, widoki, komunikacja z API, formularze, wygląd |
| [05-testy.md](docs/05-testy.md) | wszystkie testy: jak uruchomić, tabela przypadków, wyniki |
| [06-bezpieczenstwo.md](docs/06-bezpieczenstwo.md) | zabezpieczenia i ich weryfikacja, ograniczenia, wdrożenie produkcyjne |
| [07-listingi.md](docs/07-listingi.md) | 26 listingów kodu do pracy (tytuł, plik, wyjaśnienie) |
| [08-podsumowanie.md](docs/08-podsumowanie.md) | wymagania, kryteria ukończenia, mapa rozdziałów pracy, decyzje, scenariusz prezentacji |
| [diagramy/](docs/diagramy) | diagramy PNG/SVG do pracy |
| [postman/](docs/postman) | kolekcja Postmana |
| [screenshots/](screenshots) | 21 zrzutów ekranu |

## Struktura

```
backend/      Spring Boot
frontend/     Angular
database/     migracje Flyway, dane demo, testy i przykładowe zapytania SQL
docs/         dokumentacja (01–06) i kolekcja Postmana (docs/postman)
screenshots/  rzeczywiste zrzuty ekranu aplikacji i instrukcja ich wykonania
```

## Konta demonstracyjne (tylko środowisko deweloperskie)

| E-mail | Hasło | Rola |
|---|---|---|
| admin@drukarnia.local | Admin123! | Administrator |
| klient@drukarnia.local | Klient123! | Klient |
| anna.nowak@drukarnia.local | Klient123! | Klient |

Konta istnieją wyłącznie w danych demonstracyjnych ładowanych w profilu `dev`.
