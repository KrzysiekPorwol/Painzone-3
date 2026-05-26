# Architektura i Domena: Painzone-3 (Claude Code Optimized)

## 1. Stos Technologiczny
* **UI:** Jetpack Compose (Monolit, pojedynczy moduł).
* **Architektura:** MVVM + Clean Architecture.
* **DI (Wstrzykiwanie zależności):** Hilt (automatyczne dostarczanie obiektów bazy i repozytoriów).
* **Asynchroniczność:** Kotlin Coroutines & Flow (reaktywne strumienie danych).
* **Wersje Android:** Min SDK: 29 (Android 10) | Target SDK: 36 | Compile SDK: 36.1.

## 2. Warstwa Danych (Offline-First)
* **Lokalna baza (SSOT):** Room DB. Wszystkie ID to UUID (String) generowane u klienta.
* **Chmura:** Firebase Auth (Obsługa trybu gościa) + Firestore.
* **Synchronizacja:** WorkManager (SyncWorker) wysyła dane do Firestore po kliknięciu "Zakończ Trening".
* **Konflikty:** Strategia „Ostatni zapis wygrywa” (Last Write Wins) na podstawie `updated_at`.

## 3. Encje i Schemat Bazy (Room)
| Tabela (Entity) | Kluczowe Pola |
| :--- | :--- |
| **Exercise** | `id`, `name`, `muscleGroup` (Enum), `isDeleted` (Boolean) |
| **WorkoutPlan** | `id`, `name`, `createdAt` (Long) |
| **PlanDay** | `id`, `planId`, `name`, `order` |
| **PlanExercise** | `id`, `dayId`, `exerciseId`, `note` (String?), `order` |
| **WorkoutSession** | `id`, `planDayId?`, `startTime`, `endTime`, `status` (Enum) |
| **LoggedSet** | `id`, `sessionId`, `exerciseId`, `weight`, `reps`, `rpe`, `restTime`, `timestamp` |

## 4. Logika Domenowa (Biznesowa)
* **Wzór na 1RM:** Formuła Brzyckiego: `weight * (36 / (37 - reps))`.
* **Analityka:** Wykresy prezentują „Najlepszy 1RM” (Best Set) z każdej sesji treningowej.
* **Progresja:** UI oblicza zmianę procentową (%) obecnego wyniku względem średniej kroczącej z 14 dni.
* **Timer Odpoczynku:** Odliczanie w dół (Countdown). Startuje automatycznie po zapisie serii. Rzeczywisty czas między seriami jest zapisywany w `LoggedSet.restTime`.

## 5. Enums (Wartości stałe)
* **MuscleGroup:** `CHEST`, `BACK`, `LEGS`, `SHOULDERS`, `BICEPS`, `TRICEPS`, `ABS`, `OTHER`.
* **RPE:** `EASY`, `NORMAL`, `HARD`.
* **SessionStatus:** `IN_PROGRESS`, `COMPLETED`, `DISCARDED`.