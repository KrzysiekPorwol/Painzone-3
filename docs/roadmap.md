# Roadmap: Painzone-3

Ten plik jest **mapą drogową projektu**. Na początku każdej nowej sesji Claude czyta go w pierwszej kolejności (po `CLAUDE.md`), żeby wiedzieć, nad którym etapem pracujemy i co jest następne.

## Konwencja

- Każdy etap ma status: `TODO` / `DOING` / `DONE`.
- W danym momencie **co najwyżej jeden etap ma `DOING`** (zgodnie z regułą 12 z `docs/rules.md` — jedna sesja, jedno zadanie).
- Każdy etap kończy się: zielony build (`./gradlew assembleDebug`) + smoke test na emulatorze + commit + push na `origin/main` + aktualizacja statusu w tym pliku.
- „Następny krok" pod tabelą zawsze pokazuje, od czego zacząć w kolejnej sesji.

## Etapy

| ID  | Etap                                                                                   | Status |
| :-- | :------------------------------------------------------------------------------------- | :----- |
| E0  | Setup zależności (Hilt, Room, Navigation, Coroutines, Firebase BoM, WorkManager) + plugin Google Services + JDK 17/21 + sanity `assembleDebug` | DOING  |
| E1  | Skeleton aplikacji: Hilt App, MainActivity z NavHost, bottom nav 3 zakładki (Trenuj/Plany/Postęp) + overflow → Biblioteka | TODO   |
| E2  | Warstwa danych: encje Room (Exercise, WorkoutPlan, PlanDay, PlanExercise, WorkoutSession, LoggedSet) + DAO + Database + repozytoria + DI | TODO   |
| E3  | S5 Biblioteka ćwiczeń: CRUD lokalny + filtr po `MuscleGroup` + walidacja (brak pustych nazw) + miękkie usuwanie (`isDeleted`) | TODO   |
| E4  | S3 Plany: lista planów → edycja dni → picker ćwiczeń z biblioteki + notatki + kolejność | TODO   |
| E5  | S2 Sesja: logowanie serii (`reps × kg × RPE`), Last Set Preview, auto-timer odpoczynku, auto-jump fokus, persistence Room < 6h, auto-discard > 6h | TODO   |
| E6  | S1 Home: smart-karta „Kontynuuj plan" + banner aktywnej sesji (jeśli `IN_PROGRESS`)    | TODO   |
| E7  | S0 Auth: Google Sign-In (Firebase Auth) + tryb gościa (anonymous) + sync state indicator | TODO   |
| E8  | Sync: WorkManager (SyncWorker) → Firestore, strategia Last Write Wins po `updated_at`, pełne pobranie historii przy loginie na nowym urządzeniu | TODO   |
| E9  | S4 Analiza: wykresy objętości per partia, 1RM per ćwiczenie (Brzycki), drill-down, filtry 30/90/365 dni, progresja % vs 14-dniowa średnia krocząca | TODO   |
| E10 | Polish: stany pusty/błąd/syncing, edge cases z PRD (m.in. usunięte ćwiczenie na wykresach), `./gradlew lint`, testy jednostkowe domeny | TODO   |

## Następny krok

**E0 — Setup zależności.** Konkretnie:
1. Dodać do `gradle/libs.versions.toml` wersje i aliasy: KSP, Hilt, Room, Coroutines, Navigation Compose, Lifecycle ViewModel Compose, Firebase BoM + Auth + Firestore, WorkManager + Hilt-Work, Google Services plugin, kotlinx-datetime.
2. Dodać aliasy pluginów: `kotlin-android`, `ksp`, `hilt`, `google-services` (oraz `kotlin-android` zastosować w `app/build.gradle.kts` obok `kotlin-compose`).
3. W `build.gradle.kts` (root) i `app/build.gradle.kts` zarejestrować pluginy + zależności.
4. Podnieść `sourceCompatibility`/`targetCompatibility`/`jvmTarget` do **17** (kompatybilne z AGP 9 i JBR 21).
5. Utworzyć klasę `PainZoneApp : Application` z `@HiltAndroidApp` i podpiąć w `AndroidManifest.xml`.
6. `./gradlew assembleDebug` — musi przejść zielono.
7. Commit + push, status E0 → `DONE`, E1 → `DOING`.