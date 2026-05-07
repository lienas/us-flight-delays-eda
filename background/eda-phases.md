# Die vier Phasen der Explorativen Datenanalyse

Die explorative Datenanalyse (EDA) folgt einem klassischen Workflow, der von John W. Tukey 1977 in seinem Buch *Exploratory Data Analysis* geprägt wurde. Die vier Phasen sind nicht beliebig austauschbar — sie bauen systematisch von „Was haben wir?" zu „Was beeinflusst was?" auf.

---

## 1. Preliminary Wrangling — *„Was haben wir überhaupt?"*

**Ziel:** Den Rohdatensatz in einen analysefähigen Zustand bringen und seine Grundstruktur verstehen.

**Typische Tätigkeiten:**

- Datenherkunft und -definition prüfen (Codebook lesen, Spalten-Glossar verstehen)
- Spalten **auswählen** (welche sind für die Fragestellung relevant?)
- **Datentypen** korrigieren (Datumsfelder als `datetime`, Codes als `category`)
- **Missing values** und Sonderwerte identifizieren
- **Feature Engineering** (z. B. eine zusammengesetzte Zeitangabe in eine reine Stunde umwandeln)
- **Filtern** auf den relevanten Ausschnitt (z. B. einen vollständigen Zeitraum)
- **Umbenennen** kryptischer Codes in lesbare Bezeichnungen

**Warum zuerst?** Jede spätere Visualisierung steht und fällt mit der Datenqualität. *Garbage in, garbage out.*

---

## 2. Univariate Exploration — *„Wie sieht jede Variable für sich aus?"*

**Ziel:** Die Verteilung **jeder einzelnen Variable** verstehen — eine nach der anderen, isoliert.

**Typische Fragen:**

- Wie ist die Variable verteilt? (normal, schief, bimodal?)
- Welcher Wertebereich, welche Quantile?
- Gibt es **Ausreißer** oder unplausible Werte?
- Brauche ich eine **Transformation** (Log, Clipping, Standardisierung)?

**Typische Plots:** Histogramm, Boxplot, KDE, Bar-Chart für kategorische Variablen.

**Warum vor bivariat?** Wenn eine Variable z. B. stark rechtsschief ist, weiß man, dass für sinnvolle Aggregate **Median statt Mean** zu verwenden ist. Die univariate Analyse liefert die methodische Grundlage für alle weiteren Schritte.

---

## 3. Bivariate Exploration — *„Wie hängen je zwei Variablen zusammen?"*

**Ziel:** Den Einfluss **einer** erklärenden Variable auf die **Outcome-Variable** (oder zwischen zwei beliebigen Variablen) untersuchen.

**Typische Fragen:**

- Hängt das Outcome von einer kategorischen Eingangsgröße ab?
- Korrelieren zwei numerische Variablen?
- Wie unterscheiden sich Gruppen?

**Typische Plots:** Scatterplot (numerisch × numerisch), Boxplot/Violin (kategorisch × numerisch), gestapeltes Bar-Chart (kategorisch × kategorisch), Liniendiagramme über die Zeit.

**Warum nach univariat?** Erst muss jede Variable einzeln bekannt sein, sonst werden Beziehungen falsch interpretiert. Klassisches Beispiel: Wenn eine erklärende Variable bimodal ist, mischt ein einfaches Mittelwert-Vergleich zwei sehr unterschiedliche Subpopulationen und liefert irreführende Ergebnisse.

---

## 4. Multivariate Exploration — *„Wie wirken drei oder mehr Variablen zusammen?"*

**Ziel:** **Interaktionseffekte** entdecken, die in bivariaten Sichten unsichtbar bleiben.

**Typische Fragen:**

- Verstärkt eine zweite Variable den Effekt einer ersten? Oder hebt sie ihn auf?
- Gibt es Subgruppen, in denen die bivariate Beziehung anders aussieht als im Gesamtbild?
- Wie hat sich eine Größe über die Zeit hinweg für verschiedene Gruppen entwickelt?

**Typische Plots:** Heatmap, Facet-Grid, farbkodierte Scatterplots, gestapelte Bars, kleine Multiples (Small Multiples).

**Warum am Schluss?**

1. **Komplexität wächst exponentiell.** Drei Variablen brauchen entweder eine zusätzliche Encoding-Dimension (Farbe, Form, Größe, Facette) oder Aggregation.
2. **Interaktionen sind nur sinnvoll, wenn die Haupteffekte verstanden sind.** Eine Interaktion ist per Definition eine *Abweichung* vom additiven Verhalten zweier Haupteffekte — diese muss man also zuerst kennen.

---

## Warum diese Reihenfolge zwingend ist

```diagram
╭──────────────────────╮
│  Preliminary         │  ← Datenqualität sichern
│  Wrangling           │
╰──────────┬───────────╯
           │
           ▼
╭──────────────────────╮
│  Univariate          │  ← Verteilungen kennenlernen
│  Exploration         │     → Methodische Wahl (Median? Log? Clipping?)
╰──────────┬───────────╯
           │
           ▼
╭──────────────────────╮
│  Bivariate           │  ← Haupteffekte messen
│  Exploration         │     → Welche Variablen sind überhaupt relevant?
╰──────────┬───────────╯
           │
           ▼
╭──────────────────────╮
│  Multivariate        │  ← Interaktionen entdecken
│  Exploration         │     → Wie wirken die relevanten Variablen zusammen?
╰──────────────────────╯
```

Jede Stufe **filtert und fokussiert** den Aufwand der nächsten:

- Nach Wrangling weiß man, **welche Daten** verwendet werden.
- Nach Univariate weiß man, **welche Plots und Statistiken** angemessen sind.
- Nach Bivariate weiß man, **welche Variablen** überhaupt einen Effekt haben — man muss nicht alle möglichen Variablenpaare in höheren Dimensionen plotten.
- Multivariate konzentriert sich dann gezielt auf die wenigen interessanten Kombinationen.

Würde man die Reihenfolge umdrehen (z. B. mit einer Heatmap vor dem Verständnis der Verteilungen beginnen), produziert man schnell hübsche, aber inhaltlich falsche oder irreführende Bilder.

---

## Das übergreifende Prinzip: **Question → Visualization → Observation**

Innerhalb jeder Phase wird die „QVO"-Schleife durchlaufen:

1. **Question** — eine konkrete Frage formulieren („Beeinflusst Variable X das Outcome Y?")
2. **Visualization** — den passenden Plot wählen, der genau diese Frage beantworten kann
3. **Observation** — schriftlich festhalten, was der Plot zeigt — und ob er **neue Fragen** aufwirft, die in der nächsten Phase aufgegriffen werden.

Dieses Schema verhindert „Plot-Spaghetti" (viele Diagramme, keine Erkenntnisse) und sorgt dafür, dass jede Visualisierung einen klaren Zweck hat.

---

## Abgrenzung: Was EDA *nicht* ist

EDA ist **nicht** das gleiche wie:

- **Confirmatory Data Analysis** (Hypothesentests, p-Werte) — kommt erst danach.
- **Modellierung** (Regression, Machine Learning) — Modelle sind erst sinnvoll, wenn die Daten verstanden sind.
- **Reporting / Storytelling** (Explanatory Analysis) — das ist eine eigene Disziplin mit anderen Regeln (Reduktion auf eine Kernaussage, polierte Visualisierungen, narrative Struktur). EDA dient der **Eigenerkundung**, Reporting der **Vermittlung**.

Beide Modi haben unterschiedliche Zielgruppen (Analystin vs. Stakeholder) und unterschiedliche Erfolgskriterien (Verständnistiefe vs. Überzeugungskraft).

---

## Fazit

Die vier Phasen Wrangling → Univariate → Bivariate → Multivariate sind **kein Korsett**, sondern ein **Trichter**: Sie reduzieren systematisch die Unsicherheit über die Daten und konzentrieren den Analyseaufwand dort, wo er den höchsten Erkenntnisgewinn liefert. Wer eine Phase überspringt, riskiert entweder, in irrelevanten Details zu versinken — oder offensichtliche Effekte zu übersehen, weil die Grundlagen fehlen.
