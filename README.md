# FCCU – SPC & Alarmmetriken (X̄-R + I‑MR) mit `pyshewhart` und Plotly

Dieses Repository enthält ein didaktisch aufgebautes Jupyter-Notebook zur **Statistical Process Control (SPC)** in der Prozessautomation – mit Fokus auf:
- **Phase‑I / Phase‑II** Vorgehen (Limits schätzen → Monitoring)
- **X̄‑R‑Charts** (Subgruppen) und **I‑MR‑Charts** (Einzelwerte)
- **Downsampling/Subgrouping** als zentrale Stellhebel gegen Fehlalarme (Autokorrelation/Quantisierung)
- **Alarmmetriken** als Grundlage, um später SPC mit **ML/DL** fair zu vergleichen (z. B. First Alarm Index, Detection Delay, False‑Alarm‑Rate)

## Inhalt

- **Notebook:** `01_FCCU_SPC_Alarmmetriken_pyshewhart_v12_*.ipynb`
- **Requirements:** `Requirements.md` (Pakete/Umgebung)
- **Datenordner:** `data/fccu/` (CSV-Dateien; nicht zwingend im Repo enthalten)

## Schnellstart

### 1) Repository klonen
```bash
git clone <DEIN_REPO_URL>
cd <DEIN_REPO_ORDNER>
```

### 2) Virtuelle Umgebung + Installation (Beispiel mit `venv`)
```bash
python -m venv .venv

# Windows:
.venv\Scripts\activate
# macOS/Linux:
source .venv/bin/activate

python -m pip install --upgrade pip
python -m pip install numpy pandas plotly pyshewhart jupyterlab ipykernel
```

> Alternativen (conda/mamba) und optionale Pakete (z. B. `kaleido` für Plot‑Export) findest du in **Requirements.md**.

### 3) Daten ablegen
Lege die FCCU‑CSV‑Dateien in folgendem Ordner ab:
```text
data/fccu/
  NOC_stableFeedFlow_outputs.csv
  NOC_varyingFeedFlow_outputs.csv
  UAf_decrease_outputs.csv
  condEff_decrease_outputs.csv
  deltaP_increase_outputs.csv
  CAB_valveLeak_outputs.csv
  Fhn_sensorDrift_outputs.csv
```

**Wichtig:** Die Rohdaten werden im Notebook ohne Header geladen (`header=None`), daher sind die Spalten **0‑basiert** per Index adressiert.

### 4) Notebook starten
```bash
jupyter lab
```
Öffne anschließend das Notebook und führe **Kernel → Restart Kernel and Run All** aus.

## Notebook‑Logik (Kurzüberblick)

### Phase I / Phase II
- **Phase I (Normalbetrieb/NOC):** Limits werden aus einer NOC‑Teilmenge geschätzt (Standard: `NOC_stableFeedFlow_outputs.csv`).
- **Phase II (Monitoring):** dieselben Limits werden auf Fault‑Daten angewendet (Standard: `Fhn_sensorDrift_outputs.csv`).

### Zentrale Parameter (im CONFIG‑Block)
Im Notebook gibt es einen CONFIG‑Block, u. a. mit:
- `data_dir`: Datenpfad (Standard `data/fccu`)
- `max_rows_per_file`: Importlimit pro Datei (Performance)
- `target_col_index`: Zielvariable als **Spaltenindex**
- `downsample_step`: Downsampling‑Schrittweite
- `subgroup_size`: Subgroup‑Größe für X̄‑R
- `mr_span`: Moving‑Range‑Fenster für I‑MR

### Alarmmetriken (Basis)
Im Notebook werden Alarmmetriken ausgegeben, z. B.:
- **False‑Alarm‑Rate (Phase I)**: Anteil OOC in Phase I
- **First Alarm Index (Phase II)**: erste OOC‑Subgruppe bzw. erster OOC‑Punkt
- **Detection Delay (Näherung)**: Rückrechnung auf Sample‑Index (über `n * downsample_step`)

Diese Metriken sind bewusst so definiert, dass sie später **identisch** für ML/DL‑Modelle genutzt werden können.

## I‑MR vs. X̄‑R (Hinweis zur Vergleichbarkeit)
- **I‑MR** arbeitet auf Einzelwerten (oder einer „Individuals“-Reihe).
- **X̄‑R** arbeitet auf Subgruppenmitteln/Spannweiten (Aggregation).

Für einen „fairen“ Vergleich kann man I‑MR auch auf **Subgruppenmitteln** \(\bar{x}_i\) anwenden (I‑MR auf Subgruppenebene). Das Notebook enthält dazu einen optionalen Demonstrationsblock.

## Export (optional)
Das Notebook kann zentrale Hilfsfunktionen als `.py` exportieren (z. B. `spc_phase12_xbarr.py`), um sie in weiteren Notebooks wiederzuverwenden.

## Troubleshooting

### Matheformeln werden nicht gerendert
Im Notebook wird eine robuste Inline‑Math‑Schreibweise verwendet:
- `\\( ... \\)` und `\\[ ... \\]`

Falls du Textblöcke per Copy/Paste einfügst, achte darauf, keine verschachtelten `\\( ... \\)` zu erzeugen.

### Viele Fehlalarme trotz NOC
Typische Ursachen:
- starke Autokorrelation
- Quantisierung (viele identische Werte)
- ungeeignete Subgruppenbildung

Gegenmaßnahmen im Notebook:
- `downsample_step` erhöhen
- `subgroup_size` moderat anpassen
- Phase‑I‑Slice (optional) auf stabilen Bereich setzen

## Lizenz & Attribution (bitte im Repo ergänzen)
- Lege eine passende **LICENSE** Datei an (z. B. MIT/Apache‑2.0 oder CC‑BY für Inhalte).
- Prüfe die Lizenzbedingungen der verwendeten Bibliotheken und des Datensatzes.
- Ergänze bei Bedarf eine kurze **Zitations-/Quellenangabe** im Notebook‑Header und/oder hier im README.

---

### Kontakt / Beitrag
Wenn du das Notebook weiterentwickeln möchtest (z. B. PCA/T²/SPE, Klassifikation, DL), empfiehlt sich:
- neue Notebooks in einem eigenen Ordner `notebooks/`
- wiederverwendbare Metrik‑Funktionen in `src/` (optional)
- eine kleine „Benchmark‑Tabelle“ (CSV) für Vergleich SPC ↔ ML/DL
