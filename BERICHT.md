# WotAI (wotai.co) – Recherchebericht

Stand: 14.10.2026

> **Hinweis zur Quellenlage:** wotai.co selbst war aus der Cloud-Umgebung nicht erreichbar (der Netzwerk-Proxy hat die Domain blockiert). Die Angaben zur Website stammen aus Suchmaschinen-Snippets der wotai.co-Seiten. Die beiden eigenen GitHub-Repos habe ich geklont und direkt gelesen. Preise können veraltet sein, für ECHO widersprechen sich die Quellen sogar.

---

## 1. Wer ist WotAI?

- AI-Automation-Beratung aus den USA, die inzwischen eigene Produkte baut. Slogan: „AI Automation that Actually Ships“.
- Gründer ist **Alex Kim**: n8n Ambassador und n8n Certified Expert Partner, auch als Creator auf n8n.io (`alexk1919`) und auf Gumroad (`alexk1919`) aktiv.
  - In den strukturierten Daten der ECHO-Seite steht zusätzlich **Wayne Eagle** als Co-Founder, die About-Seite nennt nur Kim.
- Nach eigener Angabe 300+ Automationen in Produktion gebracht (nicht unabhängig geprüft).
- Technischer Schwerpunkt: n8n, NocoDB, Next.js/Python, Claude Code.
- Skool-Community „WotAI“: kostenloser Einstieg, ca. 790 Mitglieder.

## 2. Produkte und Preise

| Produkt | Was es ist | Preis |
|---|---|---|
| **WotAI Flow** | KI-Generator für n8n-Workflows: man beschreibt den Workflow, ein Assistent stellt Rückfragen und gibt n8n-JSON aus (laut Anbieter zu 70–99 % fertig, Nodes werden gegen die n8n-Schemas geprüft) | Free: 1 Generierung/Monat · Bezahlpläne ab **19 $/Monat** (14 Tage Test) |
| **ECHO** | Paket für Marketing-Automation, das in die **eigene n8n- und NocoDB-Instanz** installiert wird: 10 Workflows, „6 Intelligence-Module“, „15+ AI-Agents“ | Widersprüchlich: 97 $/Monat · 970 $/Jahr · 2.970 $ einmalig · Einzel-Deployment 499 $ (Seite `/echo`) **oder** 29 $/Monat · 290 $/Jahr · 990 $ Lifetime (Seite `/community`, die Preise steigen mit der Mitgliederzahl) |
| **WoterClip** | Claude-Code-Plugin für Agent-Orchestrierung (siehe unten) | **kostenlos, MIT** · optional ein bezahltes „Persona Starter Pack“ auf Gumroad |
| **Persona Starter Pack** | Fertige Personas für WoterClip | Gumroad, Preis nicht ermittelt |
| **n8n Workflow Generator v1** | Älteres Gumroad-Produkt | Gumroad, Preis nicht ermittelt |
| **Dienstleistungen** | Workflow-Builds · Custom AI Apps · laufender AI-Betrieb | 5.000–20.000+ $ pro Projekt · Pilot zum Festpreis plus Retainer · 10.000–30.000+ $/Monat |
| **Free Resources** | Kostenlose n8n-Workflows (`wotai.co/resources`), Blog, z. B. eine Anleitung zum Selbsthosten von n8n | kostenlos |

## 3. GitHub: `github.com/wotai-dev`

7 öffentliche Repos, davon nur **2 eigene**. Die übrigen 5 sind unveränderte oder kaum geänderte Forks.

### 3.1 WoterClip (eigenes Repo, 59 ★, MIT, v0.3.0)

<https://github.com/wotai-dev/woterclip>

- **Was:** Agent-Orchestrierung für Claude Code mit **GitHub Issues als Backend**. Es gibt keinen Server, keine Datenbank und keine Zusatzprozesse.
- **Prinzip:** Eine einzige Claude-Instanz wechselt je nach Issue-Label die „Persona“:
  - Orchestrator (Haiku) – verteilt die Arbeit
  - CEO (Sonnet) – Strategie und Priorisierung
  - Backend (Opus), Frontend (Sonnet) – erledigen die Arbeit
  - Der Mensch ist das „Board“, also die letzte Eskalationsstufe.
- **Heartbeat-Loop:** `/heartbeat` nimmt das wichtigste zugewiesene Issue, sperrt es, bearbeitet es und schreibt einen strukturierten Bericht als Issue-Kommentar. Ein JSONL-Log dient als Audit-Trail.
- **Labels als Zustand:** `agent-working`, `agent-blocked`, Status- und Prioritäts-Labels.
- **Personas** bestehen aus Dateien: `SOUL.md` (Identität), `TOOLS.md`, `config.yaml` (Modell, Effort, Max-Turns). Eigene Personas legt man mit `/persona-create` an.
- **Betrieb:** Alle GitHub-Zugriffe laufen über die `gh`-CLI, ein MCP-Server ist nicht nötig. Deshalb läuft es auch headless per `/schedule 30m /heartbeat`.
- **Installation:** in Claude Code `/plugin`, dann „Add Marketplace“ mit `wotai-dev/woterclip`, danach `/woterclip-init`.
- **Einschränkungen:**
  - Kommentare laufen unter dem eigenen `gh`-Account. Dadurch gibt es keine Benachrichtigung bei Eskalationen, empfohlen wird ein separater Bot-Account.
  - Es ist kein eigener Scheduler eingebaut.
  - Das Zeitlimit pro Beat (20 Minuten) ist fest eingestellt.
- **Qualität der Doku:** gut. Die Doku unter `references/` (z. B. `beat-economics.md`) ist sauber durchdacht, etwa beim Lock-Ownership und bei den Stop-Gründen.
- Inspiriert von **Paperclip** (paperclipai). WoterClip ist im Grunde ein schlanker, server-loser Nachbau davon.

### 3.2 typesafe-jev-tools (eigenes Repo, 1 ★, MIT)

<https://github.com/wotai-dev/typesafe-jev-tools>

- **Claude-Code-Hook** (`PreToolUse` bei Write/Edit), der beim Schreiben von LLM-Aufrufen oder Klassifizierer-Funktionen eine Rückfrage in den Kontext einfügt: Reicht hier normaler Code (Regex, DB-Abfrage), braucht es ein Frontier-Modell, oder genügt ein schnelles Klein-Modell?
- Der Hook blockiert nie. Er ist ein reines Bash-Skript und in 2 Minuten eingebaut.
- Ein optionaler Judge-Modus schickt Code an die API von **typesafe.ai** (Modell „Jev“, kostenpflichtig, ca. 42 $ pro 1 Mrd. Input-Tokens).
- Enthält einen gemessenen Benchmark mit 150 Texten, 16 Modellen und 2.400 Aufrufen: Jev und Claude Haiku 4.5 erreichen beide 66 % Genauigkeit, Jev ist schneller, sagt aber deutlich häufiger „unsicher“.
- Im Unterordner `minecraft/` liegt ein TypeScript-Testprojekt für den Benchmark.
- Eher ein Nebenprojekt für einen Drittanbieter (TypeSafe) als ein WotAI-Produkt.

### 3.3 Forks (kein eigener Mehrwert, dann besser das Original nehmen)

| Fork | Original | Kurz |
|---|---|---|
| n8n-as-code | EtienneLescot/n8n-as-code | Gibt KI-Agents n8n-Wissen: 537 Node-Schemas, 7.700+ Templates, Git-artiger Sync, Workflows in TypeScript |
| paperclip | paperclipai/paperclip | Orchestrierung für „Zero-Human Companies“ |
| paperclip-companies | paperclipai/companies | Firmen-Templates für Paperclip |
| openclaw | openclaw/openclaw | Persönlicher KI-Assistent für mehrere Plattformen |
| wotai-openmontage | calesthio/OpenMontage | Agentische Videoproduktion (AGPL-3.0) |

## 4. Kostenlose Alternativen zu den Bezahlprodukten

- **Statt WotAI Flow (19 $/Monat):**
  - **n8n-as-code** (Original von EtienneLescot, MIT): Damit kennt Claude Code bzw. ein Agent alle n8n-Nodes mit Schema. Das ist genau der Vorteil, mit dem Flow wirbt, nur kostenlos und lokal.
  - Dazu kommen der eingebaute AI-Workflow-Builder von n8n selbst und freie Template-Sammlungen (n8n.io/workflows, wotai.co/resources, n8n-library.com).
- **Statt ECHO:** ECHO ist im Kern ein Bündel aus n8n-Workflows plus NocoDB-Schema. Mit n8n-as-code und den freien Templates lässt sich so etwas selbst nachbauen. Eine Open-Source-Version von ECHO selbst gibt es nicht.
- **WoterClip** ist bereits kostenlos. Das Persona-Pack braucht man nicht, die 4 Standard-Personas liegen als Vorlage im Repo (`templates/personas/`).

## 5. Was man damit anfangen kann

1. **WoterClip ausprobieren** – das interessanteste frei verfügbare Stück.
   - Testrepo mit ein paar Issues anlegen, `/woterclip-init`, dann `/heartbeat --dry-run`.
   - Für unbeaufsichtigten Betrieb einen separaten GitHub-Bot-Account nutzen, sonst gehen Eskalationen unbemerkt unter.
   - Eigene Personas bauen, z. B. „Doku“, „Tests“, „Review“.
2. **n8n-as-code (Original) als Flow-Ersatz** in Claude Code einbinden und n8n-Workflows direkt generieren und versionieren lassen.
3. **typesafe-jev-tools-Hook** als Gedankenstütze übernehmen: Er erinnert daran, nicht für jede Kleinigkeit ein LLM aufzurufen. Den Judge-Modus mit der kostenpflichtigen API weglassen.
4. **Kombination:** WoterClip-Personas, die über n8n-as-code Workflows bauen. Die Issues sind dann gleichzeitig das Pflichtenheft und das Protokoll.

## 6. Einschätzung

- WotAI ist eine kleine Beratung mit einem Gründer im Mittelpunkt und starkem n8n-Fokus. Die Produkte sind jung (Flow wurde im März 2026 gestartet) und es gibt keine unabhängigen Reviews.
- Die Preise für ECHO sind inkonsistent, deshalb vor einem Kauf unbedingt die aktuelle Seite prüfen.
- Der eigentliche Wert auf GitHub ist **WoterClip**: frei, gut dokumentiert und sofort nutzbar. Der Rest auf GitHub sind Forks, dort sollte man das jeweilige Original verwenden.

## Quellen

- <https://wotai.co/> · <https://wotai.co/products> · <https://wotai.co/products/flow> · <https://wotai.co/echo> · <https://echo.wotai.co/> · <https://wotai.co/community> · <https://wotai.co/resources> · <https://wotai.co/about>
- <https://wotai.co/blog/flow-ai-n8n-workflow-generator-launch>
- <https://github.com/wotai-dev> · <https://github.com/wotai-dev/woterclip> · <https://github.com/wotai-dev/typesafe-jev-tools>
- <https://experts.n8n.io/partner/wotai> · <https://www.skool.com/wotai/about> · <https://www.linkedin.com/in/alexanderkim/>
- <https://awesomeclaudeplugins.com/wotai-dev/woterclip> · <https://alexk1919.gumroad.com/l/n8n-workflow-generator-v1>
