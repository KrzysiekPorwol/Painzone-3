# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Źródła prawdy — zawsze czytaj zanim coś zmienisz

Treść tych dokumentów ma pierwszeństwo nad tym plikiem. Jeśli coś się nie zgadza, ufaj plikom z `docs/` i zaktualizuj `CLAUDE.md`.

- [`docs/rules.md`](docs/rules.md) — zasady współpracy (komunikacja po polsku, limit 1000 linii, zakaz `TODO`, kolejność: zależności → kod, użycie MCP `context7` dla dokumentacji bibliotek, limit 2 prób).
- [`docs/prd.md`](docs/prd.md) — zakres MVP, ekrany (S0–S5), stany UI, edge cases i kryteria akceptacji (m.in. UI update < 300 ms po zapisie serii, pełna historia po loginie na nowym urządzeniu, sesja < 6h przetrwa restart).
- [`docs/architecture.md`](docs/architecture.md) — stos technologiczny, warstwa danych (Room SSOT + Firestore + WorkManager), encje, logika domenowa (wzór Brzyckiego, progresja, timer), enumy.

Zanim zaproponujesz plan implementacji albo zaczniesz pisać kod nowej funkcji, przeczytaj odpowiedni rozdział z powyższych plików (reguła 11 z `docs/rules.md`).

# Git workflow

## Kiedy commitować
- Po skończeniu każdego zadania **automatycznie zrób commita i wypushuj** na `origin/main` — bez pytania.
- Jeden commit = jedna spójna zmiana. Niezwiązane rzeczy → kilka osobnych commitów.
- Push bezpośrednio na `main` jest OK — to jednoosobowy projekt.

## Jak commitować
**Zawsze przy commitcie w tym projekcie** użyj skilla `.claude/skills/commit/SKILL.md` (format, typy, dwujęzyczność EN+PL, przykłady).

## Wymaga jawnej zgody
Przed jakąkolwiek nieodwracalną operacją git (force push, reset --hard, branch -D, rebase/amend na wypushowanych commitach) — **zawsze pytaj**.

## Budowanie i uruchamianie

Pojedynczy moduł Androida (`:app`), Gradle Kotlin DSL, katalog wersji `gradle/libs.versions.toml`.

```bash
./gradlew assembleDebug                                    # zbuduj debug APK
./gradlew installDebug                                     # zbuduj + zainstaluj na urządzeniu/emulatorze
./gradlew test                                             # testy jednostkowe JVM (app/src/test)
./gradlew testDebugUnitTest --tests "pain.zone_3.ExampleUnitTest.addition_isCorrect"  # pojedynczy test
./gradlew connectedDebugAndroidTest                        # testy instrumentalne (wymaga urządzenia)
./gradlew lint
./gradlew clean
```

## Stan repo

Repo jest świeżym szablonem Android Studio — istnieje tylko `MainActivity` + motyw Compose w `pain.zone_3.ui.theme`. Architektura opisana w `docs/architecture.md` (MVVM + Clean, Hilt, Room z UUID-ami klienta, Coroutines/Flow, Firestore + WorkManager) jest **docelowa** — żadna z tych zależności nie jest jeszcze na classpath. Zgodnie z regułą 2 z `docs/rules.md`: najpierw dodaj wpis do `gradle/libs.versions.toml` i `app/build.gradle.kts` (oraz pluginy Hilt/KSP w roocie + module), dopiero potem pisz kod, który je importuje.
