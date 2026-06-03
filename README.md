# Projektaufgabe: Continuous Integration für ein gemischtes C/C++ und Python Projekt

## Ziel der Aufgabe

In diesem Projekt baust du ein Repository, das einen typischen Workflow eines
Embedded-Software-Projekts nachbildet: einen C/C++ Teil, der für einen **Raspberry Pi**
gebaut wird, und einen **Python**-Teil, der z. B. zur Auswertung, Steuerung oder
Test-Automatisierung dient. Beide Teile werden über ein gemeinsames Build-System
(`make`) angesprochen und mittels **GitHub Actions** kontinuierlich gebaut, getestet
und statisch analysiert.

### Warum machen wir das?

In echten Software-Projekten reicht es nicht, dass der Code "auf meinem Rechner läuft".
Du sollst hier lernen,

- **reproduzierbare Builds** zu erstellen, die unabhängig von deiner lokalen Maschine
  funktionieren (Stichwort *Devcontainer / Cross-Compilation*),
- **Build- und Testautomatisierung** mit `make` und `cmake` einzusetzen, statt manuell
  Befehle aneinanderzureihen,
- mit **Continuous Integration (CI)** sicherzustellen, dass Änderungen den Build und die
  Tests nicht brechen, **bevor** sie auf den `main` Branch gelangen,
- **statische Code-Analyse** als zusätzliches Sicherheitsnetz neben Unit-Tests zu nutzen,
- und einen **geschützten `main` Branch** zu betreiben, sodass nur reviewte und grüne
  Änderungen integriert werden – wie es in der Praxis üblich ist.

Es geht *nicht* darum, möglichst viel Code zu schreiben, sondern eine **saubere,
wartbare und automatisierte Toolchain** aufzubauen, in die später beliebige fachliche
Inhalte eingehängt werden können.

---

## Anforderungen

### 1. Repository auf GitHub

- Das Projekt liegt in einem **öffentlichen GitHub-Repository**.
- Der `main`-Branch ist **protected**:
  - keine direkten Pushes auf `main`,
  - Merges nur über **Pull Requests**,
  - PRs brauchen **mindestens einen erfolgreichen CI-Lauf** (Build, Tests, Checks),
  - optional: mindestens ein Review erforderlich.

### 2. C/C++ Teil

Der C/C++ Teil wird mit **CMake** gebaut. Der Code soll einige (wenigstens beispielhafte) Unit-Tests beinhalten.

Folgende `make`-Targets sollen vorhanden sein und intern CMake aufrufen:

| Target        | Aufgabe                                                              |
|---------------|----------------------------------------------------------------------|
| `make build`  | Konfiguriert und baut das C/C++ Projekt mit CMake.                   |
| `make test`   | Führt die C/C++ Unit-Tests aus (z. B. mit Unity, GoogleTest oder Catch2).   |
| `make clean`  | Entfernt sämtliche Build-Artefakte.                                  |
| `make check`  | Führt eine **statische Code-Analyse** aus (z. B. `clang-tidy`, `cppcheck`). |

### 3. Python Teil

Der Python Teil wird ebenfalls über `make` angesprochen. Mindestens diese Targets:

| Target        | Aufgabe                                                |
|---------------|--------------------------------------------------------|
| `make check`    | Führt eine **statische Code-Analyse** aus (z. B. `Ruff`, `Pylint`). |
| `make test`   | Führt die Python Unit-Tests aus (z. B. `pytest`).      |

Sorg dafür, dass Abhängigkeiten von make installiert werden
(`requirements.txt`, `pyproject.toml` o. ä.).

### 4. Continuous Integration mit GitHub Actions

Leg einen Workflow unter `.github/workflows/` an, der **bei jedem Pull Request**
und **bei Pushes auf `main`** Folgendes ausführt:

1. **Build** des C/C++ Teils
2. **Unit-Tests** des C/C++ Teils.
3. **Statische Analyse** (`make check`) für den C/C++ Teil.
4. **Unit-Tests** des Python Teils.
5. **Statische Analyse** des Python Teils.

Der Workflow muss in der GitHub-Oberfläche für jeden PR sichtbares **Feedback** geben
(grün/rot, Logs einsehbar). Schlägt ein Schritt fehl, darf der PR **nicht mergebar** sein.

---

## Abgabe

Gib ab: am Tag der Klausur + 1 Woche (02. Juli 2026)

1. **Link zum GitHub-Repository.**
2. **Link zu mindestens einem PR**, der erfolgreich durch die CI gegangen ist.
3. **Link zu mindestens einem PR**, der wegen eines bewussten Fehlers (z. B. failing
   test oder lint-Verstoß) **rot** war – inklusive Folge-Commit, der das Problem behebt.
   Damit zeigst du, dass die CI nicht nur "grün durchwinkt".
4. Eine kurze **README.md** im Repository, die erklärt:
   - welche make-targets existieren, was wird dort ausgefuehrt
   - wie man das Projekt lokal baut und testet,
   - ‼️ **dein Klarname** ‼️
---

## Hinweise und Stolpersteine
- **Dependencies in CMake:** `FetchContent` oder ein vorinstalliertes Paket – beides ist
  okay, aber dokumentiere deine Wahl in der README.md.
- **Branch Protection:** Diese Regel wird in den GitHub-Repo-Einstellungen gesetzt,
  nicht im Code. Pack einen Screenshot oder eine kurze Notiz zur Abgabe dazu.
- **Reproduzierbarkeit:** Wer nach `git clone` + `make test` ein grünes Ergebnis bekommt,
  hat einen großen Teil der Aufgabe schon richtig gelöst.
- **AI-Hilfe** AI Unterstuetzung ist erlaubt - die Tools sind eine echte Hilfe. Wer jedoch alles von der AI machen laesst, lernt halt nichts :-1:.

Viel Erfolg!
