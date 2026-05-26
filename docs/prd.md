# PRD: Painzone-3 (Claude Code Optimized)

## 1. Core Concept & MVP Scope
* **Produkt:** Painzone-3 — Ultra-szybki dziennik siłowy.
* **Cel:** Rejestracja serii w sekundy + analityka progresu.
* **Model:** Offline-first + obowiązkowe Auth/Sync (Firebase/Custom API).

| Funkcja | Priorytet | Logika / Zachowanie |
| :--- | :--- | :--- |
| **Auth & Sync** | MUST | Logowanie/Backup. Pełna historia pobierana przy logowaniu na nowym urządzeniu. |
| **Logowanie Serii** | MUST | `reps × kg × RPE`. Last Set Preview inline. Auto-timer odpoczynku. |
| **Plany & Biblioteka** | MUST | CRUD własnych ćwiczeń (pusta baza) i planów treningowych. |
| **Wykresy Postępu** | MUST | Wizualizacja objętości per partia + siła (1RM) per konkretne ćwiczenie. |
| **Stats Lite** | MUST | Historia serii, Best Set, filtrowanie okresu (30/90/365 dni). |

## 2. Nawigacja i Ekrany (Jetpack Compose)
* **Główna Nav:** Bottom Navigation (3 zakładki).
* **S0 (Auth):** Login/Register/Guest. Syncing state indicator.
* **S1 (Trenuj - Home):** Smart-karta "Kontynuuj plan" + Banner aktywnej sesji (jeśli trwa).
* **S2 (Sesja):** Logowanie serii. Focus: Auto-jump do nast. pola po zapisie serii.
* **S3 (Plany/Edytor):** Lista planów -> Edycja dni -> Picker ćwiczeń.
* **S4 (Analiza):** Dashboard wykresów (Partia -> Drill-down do Ćwiczenia).
* **S5 (Biblioteka):** Globalny CRUD ćwiczeń (dostęp: Overflow Menu [⋮]).

## 3. Stany UI & UX Logic
| Stan | Zachowanie UI |
| :--- | :--- |
| **Input Auto** | Po zapisie serii: reset pól, start timer, focus na pole `Ciężar`. |
| **Empty Stats** | Komunikat: "Brak danych. Ukończ trening, aby zobaczyć wykresy". |
| **Syncing** | Silent sync. Ikona chmury: "Waiting" (offline) / "Synced" (online). |
| **Deleted Item** | Usunięte ćwiczenie zostaje na wykresach z dopiskiem "[Usunięte]". |

## 4. Edge Cases & Validation
* **Sync Conflict:** Merge na podstawie `updated_at` i UUID.
* **Input Validation:** Pozwalamy na wartości `0` (rozgrzewka). Brak pustych nazw ćwiczeń.
* **Stale Session:** Sesja `InProgress` bez aktywności > 6h zostaje automatycznie porzucona.
* **Persistence:** Aktywna sesja (< 6h) musi przetrwać restart aplikacji (Room storage).

## 5. Kryteria Akceptacji (TAC)
* **GIVEN** Logowanie na nowym urządzeniu **THEN** pobierz całą historię (nie tylko 90 dni).
* **GIVEN** Zapis serii **THEN** UI update i focus w < 300ms.
* **GIVEN** Usunięcie ćwiczenia **THEN** zachowaj punkty na wykresie (min. 2 punkty by rysować linię).
* **GIVEN** Brak sieci **THEN** pełny CRUD lokalny, sync po odzyskaniu połączenia.