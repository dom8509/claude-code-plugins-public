---
name: claude-md
description: Skill zur Analyse, Pflege und Erstellung von CLAUDE.md-Dateien nach offiziellen Anthropic Best Practices. Verwenden wenn der User eine CLAUDE.md erstellen, verbessern, prüfen oder verstehen will — oder wenn eine bestehende CLAUDE.md analysiert werden soll.
---

# CLAUDE.md — Analyse, Pflege und Erstellung

Offiziell dokumentierte Best Practices von code.claude.com/docs/en/memory und code.claude.com/docs/en/best-practices.

---

## Was ist CLAUDE.md?

CLAUDE.md ist eine Markdown-Datei, die Claude zu Beginn jeder Session liest. Sie enthält persistente Anweisungen — Dinge, die Claude ohne diese Datei jedes Mal neu erklärt bekommen müsste. Sie ist kein Konfigurationsfile (keine Enforcement), sondern ein Kontextfile: Claude liest es und verhält sich entsprechend.

**Faustregel:** Alles, was man einem neuen Teammitglied am ersten Tag erklären würde und was nicht im Code steht, gehört in CLAUDE.md.

---

## Platzierungs-Optionen (Scope)

| Scope | Pfad | Geteilt mit | Typischer Inhalt |
|---|---|---|---|
| **Org-weit (managed)** | macOS: `/Library/Application Support/ClaudeCode/CLAUDE.md` | Allen Usern auf der Maschine | Unternehmensstandards, Compliance |
| **User (global)** | `~/.claude/CLAUDE.md` | Nur du — alle Projekte | Persönliche Präferenzen, Shortcuts |
| **Projekt** | `./CLAUDE.md` oder `./.claude/CLAUDE.md` | Team via Git | Architektur, Coding Standards, Workflows |
| **Lokal** | `./CLAUDE.local.md` | Nur du — dieses Projekt | Sandbox-URLs, persönliche Overrides |

Dateien werden **von oben nach unten** geladen (Org → User → Projekt → Lokal). Subdirectory-CLAUDE.md-Files werden lazy geladen, wenn Claude Dateien in diesem Verzeichnis liest.

---

## Was gehört rein — Was nicht

| ✅ Rein | ❌ Raus |
|---|---|
| Bash-Befehle die Claude nicht erraten kann | Alles was Claude aus dem Code selbst lesen kann |
| Code-Style-Regeln die vom Standard abweichen | Standard-Sprachkonventionen die Claude ohnehin kennt |
| Test-Befehle und bevorzugter Test-Runner | Ausführliche API-Dokumentation (stattdessen verlinken) |
| Branch-Naming, PR-Konventionen | Informationen die sich häufig ändern |
| Architekturentscheidungen die spezifisch für das Projekt sind | Lange Erklärungen oder Tutorials |
| Dev-Environment-Quirks (required env vars) | Datei-für-Datei-Beschreibungen der Codebase |
| Häufige Fallstricke und nicht-offensichtliche Verhaltensweisen | Selbstverständliche Praktiken wie "write clean code" |

---

## Größe und Struktur

**Zielgröße: unter 200 Zeilen pro CLAUDE.md-Datei.**

Längere Dateien:
- Verbrauchen mehr Context-Window-Tokens
- Reduzieren die Adherence (Claude ignoriert Regeln die im Rauschen untergehen)
- Führen dazu dass wichtige Anweisungen übersehen werden

**Struktur-Best-Practices:**
- Markdown-Headers und Bullets verwenden — Claude scannt Struktur wie Leser es tun
- Konkret schreiben: "Use 2-space indentation" statt "Format code properly"
- Verifizierbar schreiben: "Run `npm test` before committing" statt "Test your changes"
- Keine Widersprüche zwischen Dateien — bei Konflikten wählt Claude willkürlich

**Emphasis für kritische Regeln:** `IMPORTANT:` oder `YOU MUST` verbessert die Adherence für Regeln die oft missachtet werden.

---

## @Import-Syntax

CLAUDE.md kann andere Dateien importieren:

```markdown
See @README.md for project overview and @package.json for available commands.

# Additional Instructions
- Git workflow: @docs/git-instructions.md
- Personal overrides: @~/.claude/my-project-instructions.md
```

- Relative und absolute Pfade erlaubt
- Importierte Dateien werden bei Launch vollständig in den Kontext geladen (sparen keine Tokens)
- Max. 5 Import-Ebenen (rekursiv)
- HTML-Kommentare (`<!-- ... -->`) werden vor der Injektion entfernt — nützlich für Maintainer-Notizen ohne Token-Kosten

---

## .claude/rules/ — Path-spezifische Regeln

Für größere Projekte: Regeln in separate Dateien aufteilen, die nur laden wenn Claude mit passenden Dateien arbeitet.

```
.claude/
├── CLAUDE.md
└── rules/
    ├── testing.md         # immer geladen
    ├── api-design.md      # immer geladen
    └── frontend.md        # path-scoped
```

**Path-scoped Rule (mit YAML Frontmatter):**
```markdown
---
paths:
  - "src/api/**/*.ts"
  - "lib/**/*.{ts,tsx}"
---

# API Development Rules
- All endpoints must include input validation
- Use the standard error response format
```

Regeln ohne `paths`-Frontmatter laden immer. Regeln mit `paths` laden nur wenn Claude Dateien öffnet, die dem Muster entsprechen — das spart Kontext.

---

## Häufige Fehlerquellen

| Problem | Symptom | Fix |
|---|---|---|
| **Zu lang** | Claude ignoriert Regeln | Auf <200 Zeilen kürzen; path-spezifische Regeln auslagern |
| **Zu vage** | Claude fragt nach Dingen die schon erklärt sind | Konkrete, verifizierbare Anweisungen schreiben |
| **Widersprüchlich** | Claude wählt willkürlich | `/memory` öffnen, alle geladenen Files prüfen, Konflikte lösen |
| **Zu allgemein** | Kein Effekt auf Claude-Verhalten | Nur Regeln die Claude ohne sie falsch machen würde |
| **Falsche Platzierung** | Datei wird nicht geladen | Mit `/memory` prüfen welche Files geladen sind |

---

## Workflow A — Neue CLAUDE.md erstellen

### Schritt 1: `/init` ausführen (falls Projekt vorhanden)
```bash
/init
```
Claude analysiert die Codebase und generiert eine Starter-CLAUDE.md mit Build-Befehlen, Test-Instruktionen und erkannten Konventionen. Danach manuell verfeinern.

### Schritt 2: Kontext erfassen
Folgende Fragen beantworten:
1. Was für ein Projekt ist das? (Tech Stack, Sprache, Framework)
2. Welche Build/Test/Lint-Befehle werden verwendet?
3. Welche Code-Style-Regeln weichen vom Standard ab?
4. Welche Architekturentscheidungen sind nicht-offensichtlich?
5. Welche Fehler macht Claude ohne Anleitung immer wieder?
6. Welche Workflows (Branch-Naming, PR-Konventionen) gibt es?
7. Welche Gotchas / nicht-offensichtlichen Verhaltensweisen gibt es?

### Schritt 3: Entwurf erstellen
Struktur:
```markdown
# [Projekt-Name]

## Bash Commands
- Build: `...`
- Test: `...`
- Lint: `...`

## Code Style
- [Regel 1]
- [Regel 2]

## Architecture
- [Entscheidung 1]
- [Entscheidung 2]

## Workflow
- [Konvention 1]
- [Konvention 2]

## Gotchas
- [Fallstrick 1]
```

### Schritt 4: Pruning-Test
Für jede Zeile fragen: *"Würde Claude ohne diese Zeile Fehler machen?"* — Wenn nein: streichen.

---

## Workflow B — Bestehende CLAUDE.md analysieren

### Schritt 1: Alle geladenen Files ermitteln
```
/memory
```
Zeigt alle CLAUDE.md, CLAUDE.local.md und Rules-Files die in der aktuellen Session geladen sind.

### Schritt 2: Datei lesen und gegen Checkliste prüfen

**Checkliste:**
- [ ] Unter 200 Zeilen?
- [ ] Enthält keine Dinge die Claude aus dem Code lesen kann?
- [ ] Alle Regeln konkret und verifizierbar formuliert?
- [ ] Keine Widersprüche zwischen Abschnitten?
- [ ] Keine Standard-Konventionen die Claude ohnehin kennt?
- [ ] Keine langen Erklärungen (→ in Skill auslagern)?
- [ ] Keine häufig ändernden Informationen?
- [ ] Kritische Regeln mit IMPORTANT/YOU MUST hervorgehoben?
- [ ] Path-spezifische Regeln in `.claude/rules/` ausgelagert?

### Schritt 3: Diagnose — warum Regeln nicht befolgt werden

| Symptom | Wahrscheinliche Ursache |
|---|---|
| Claude fragt nach Dingen die in CLAUDE.md stehen | Phrasing zu vage oder File zu lang |
| Claude ignoriert eine spezifische Regel | Regel geht im Rauschen unter → File kürzen oder Regel hervorheben |
| Claude befolgt Regeln inkonsistent | Widersprüchliche Anweisungen in verschiedenen Dateien |
| CLAUDE.md scheint gar nicht geladen | `/memory` prüfen — falscher Pfad oder Datei nicht in geladenem Verzeichnis |

### Schritt 4: Optimierungsvorschläge erstellen
Konkret ausgeben:
- Was gestrichen werden kann (mit Begründung)
- Was in einen Skill ausgelagert werden sollte
- Was in path-spezifische Rules verschoben werden sollte
- Was umformuliert werden sollte (vage → konkret)
- Was ergänzt werden sollte (fehlende Kategorien)

---

## Workflow C — CLAUDE.md pflegen (Routine-Review)

Trigger: nach 2+ Wochen aktiver Nutzung, oder wenn Claude wiederholt Fehler macht.

```bash
# Zeilenzahl prüfen
wc -l CLAUDE.md
```

Review-Fragen:
1. Macht Claude trotz CLAUDE.md Fehler? → Regel fehlt oder ist zu vage
2. Fragt Claude Dinge die schon erklärt sind? → Phrasing überarbeiten
3. Gibt es Regeln die nie relevant waren? → Streichen
4. Hat sich das Projekt verändert? → Veraltete Regeln aktualisieren
5. Werden Teile nur für bestimmte Dateipfade relevant? → In rules/ auslagern

---

## Verhältnis zu anderen Mechanismen

| Mechanismus | Wann verwenden |
|---|---|
| **CLAUDE.md** | Regeln die in jeder Session gelten — immer geladen |
| **Skills** | Workflows die nur manchmal relevant sind — on demand geladen |
| **Hooks** | Aktionen die garantiert ausgeführt werden müssen (nicht advisory) |
| **Subagents** | Isolierte Tasks die den Haupt-Kontext nicht belasten sollen |
| **Auto Memory** | Claude lernt von Korrekturen — automatisch, kein manueller Aufwand |

**Faustregel:** Wenn es eine Regel ist → CLAUDE.md. Wenn es ein Workflow ist → Skill. Wenn es zwingend ausgeführt werden muss → Hook.
