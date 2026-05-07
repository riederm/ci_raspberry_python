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

### 2. C/C++ Teil (für Raspberry Pi)

Der C/C++ Teil wird mit **CMake** gebaut. Du kannst wählen:

- **Variante A:** Build direkt auf einem Raspberry Pi.
- **Variante B:** Build auf deinem PC, z. B. über einen **Devcontainer** mit
  Cross-Compile-Toolchain (`aarch64-linux-gnu-gcc` o. ä.).

Liefere eine kleine Beispielanwendung (z. B. *Blink*, *Hello GPIO*, oder ein
einfaches Sensor-Read), die zeigt, dass die Toolchain funktioniert.

Folgende `make`-Targets sollen vorhanden sein und intern CMake aufrufen:

| Target        | Aufgabe                                                              |
|---------------|----------------------------------------------------------------------|
| `make build`  | Konfiguriert und baut das C/C++ Projekt mit CMake.                   |
| `make test`   | Führt die C/C++ Unit-Tests aus (z. B. mit GoogleTest oder Catch2).   |
| `make clean`  | Entfernt sämtliche Build-Artefakte.                                  |
| `make check`  | Führt eine **statische Code-Analyse** aus (z. B. `clang-tidy`, `cppcheck`). |
| `make run`    | **Deployt** das Binary auf den Pi (z. B. via `scp`) und **führt es aus** (`ssh`). |

> Hinweis: `make run` darf voraussetzen, dass eine Variable wie `PI_HOST=pi@raspberrypi.local`
> gesetzt ist. Dokumentier das im Repo.

### 3. Python Teil

Der Python Teil wird ebenfalls über `make` angesprochen. Mindestens diese Targets:

| Target        | Aufgabe                                                |
|---------------|--------------------------------------------------------|
| `make run`    | Führt das Python-Projekt aus (Einstiegspunkt).         |
| `make test`   | Führt die Python Unit-Tests aus (z. B. `pytest`).      |

Sorg dafür, dass Abhängigkeiten reproduzierbar installiert werden
(`requirements.txt`, `pyproject.toml` o. ä.).

### 4. Continuous Integration mit GitHub Actions

Leg einen Workflow unter `.github/workflows/ci.yml` an, der **bei jedem Pull Request**
und **bei Pushes auf `main`** Folgendes ausführt:

1. **Build** des C/C++ Teils (Cross-Compile reicht – der Pi muss in CI nicht laufen).
2. **Unit-Tests** des C/C++ Teils.
3. **Statische Analyse** (`make check`) für den C/C++ Teil.
4. **Unit-Tests** des Python Teils.
5. (Optional, empfohlen) **Linter** für Python (`ruff`, `flake8`, …).

Der Workflow muss in der GitHub-Oberfläche für jeden PR sichtbares **Feedback** geben
(grün/rot, Logs einsehbar). Schlägt ein Schritt fehl, darf der PR **nicht mergebar** sein.

---

## Abgabe

Gib ab:

1. **Link zum GitHub-Repository.**
2. **Link zu mindestens einem PR**, der erfolgreich durch die CI gegangen ist.
3. **Link zu mindestens einem PR**, der wegen eines bewussten Fehlers (z. B. failing
   test oder lint-Verstoß) **rot** war – inklusive Folge-Commit, der das Problem behebt.
   Damit zeigst du, dass die CI nicht nur "grün durchwinkt".
4. Eine kurze **README.md** im Repository, die erklärt:
   - wie man das Projekt lokal baut und testet,
   - wie der Devcontainer / die Cross-Compile-Toolchain genutzt wird (falls Variante B),
   - wie `make run` für den Pi konfiguriert wird (`PI_HOST` o. ä.).

---

## Hinweise und Stolpersteine

- **Cross-Compile vs. native Build:** Wenn du auf dem PC baust, achte darauf,
  dass das Binary tatsächlich auf `aarch64` / `armv7` (je nach Pi-Modell) zielt.
  `file ./meinbinary` zeigt die Architektur an.
- **GoogleTest in CMake:** `FetchContent` oder ein vorinstalliertes Paket – beides ist
  okay, aber dokumentier deine Wahl.
- **`make run` für den Pi:** In CI **nicht** ausführen – CI hat keinen Pi. Schütz
  das Target z. B. durch eine Variable oder lass CI nur `build`, `test`, `check` aufrufen.
- **Branch Protection:** Diese Regel wird in den GitHub-Repo-Einstellungen gesetzt,
  nicht im Code. Pack einen Screenshot oder eine kurze Notiz zur Abgabe dazu.
- **Reproduzierbarkeit:** Wer nach `git clone` + `make test` ein grünes Ergebnis bekommt,
  hat einen großen Teil der Aufgabe schon richtig gelöst.

Viel Erfolg!
