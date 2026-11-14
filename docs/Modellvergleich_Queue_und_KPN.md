# Rechenmodelle im Vergleich: Queue-Maschine und Kahn-Prozessnetz

**Status:** Entscheidungsgrundlage, keine Architekturentscheidung. Diese Seite vergleicht die zwei bislang ernst genommenen Modelle anhand Falkos tatsächlicher Mechanik: Schwerkraft bewegt Murmeln vorwärts; ein fester Kopf liest/ändert sie; ein Motor hebt sie zurück; das Programm soll als Murmelband codiert sein. Elektronik darf nur Ausgabe/Beobachtung leisten.

## Kurzfassung

Die Queue-Maschine und ein Kahn-Prozessnetz (KPN) beantworten zunächst unterschiedliche Fragen:

- **Queue-Maschine:** *Wie führt ein einzelner mechanischer Rechner einen Schritt aus?* Eine endliche Steuerung liest das vorderste Symbol einer FIFO-Warteschlange, wechselt ihren Zustand und hängt eine Symbolfolge hinten an. Das ist der direktere abstrakte Fit für ein vorwärts laufendes Programmband.[1]
- **KPN:** *Wie laufen mehrere unabhängige Rechenprozesse gleichzeitig und wie kommunizieren sie?* Ein Netz aus Prozessen arbeitet auf mehreren FIFO-Kanälen. Das passt besser zu echten parallelen Bahnen, Abzweigungen und getrennten Prozessmodulen. Klassische KPN-Semantik nimmt unbegrenzte Kanäle an; Lesen blockiert bei leerem Kanal, Schreiben ist nicht blockierend.[3]

**Die Modelle schließen einander nicht aus.** Ein KPN mit einem Prozess, endlichem Steuerzustand und einer FIFO-Rückkopplung kann eine Queue-Maschine enthalten. Queue-Automaten sind im abstrakten Modell genauso ausdrucksstark wie Turingmaschinen.[1] Ein KPN ist also nicht automatisch „leistungsfähiger“; sein Hauptvorteil ist die natürliche Beschreibung verteilter, potenziell paralleler Prozesse.

Falkos neue Idee, einen vollen Puffer durch eine zusätzliche Bahn am Ende zu erweitern, ist ein wichtiger Fortschritt: Sie kann die künstliche Blockierung eines endlichen Kanals vermeiden und nähert die Mechanik der idealisierten KPN-Semantik an. Sie macht Puffer jedoch nicht wirklich unendlich, braucht einen zuverlässigen Erweiterungsmechanismus und behebt keine Blockierung, die aus einer logischen Abhängigkeit oder einem fehlenden Anfangstoken entsteht. Parks zeigt außerdem, dass die Kapazität, die einzelne Prozesse brauchen, programmabhängig und im allgemeinen KPN-Modell nicht für alle Programme entscheidbar ist; selbst das Erhöhen nur des kleinsten vollen Puffers kann in eine weitere Blockierung führen.[4]

**Vorläufige Empfehlung:** Wenn *ein austauschbares Programm auf einem Murmelband* die wichtigste Identität des Rechners ist, sollte die Queue-Maschine der Programm-Ausführungs-Kern bleiben. Wenn sichtbar parallele Berechnungen auf mehreren Bahnen ein gleichrangiges Ziel sind, ist KPN eine ernsthafte Alternative. Ein möglicher Mittelweg ist ein KPN als physisches Prozessnetz, in dem ein universeller Queue-Interpreter das bandcodierte Programm ausführt. Der Mittelweg ist aber komplexer und muss in einer Simulation belegt werden, nicht nur auf dem Papier plausibel wirken.

## 1. Gemeinsame Ausgangslage und Begriffe

### 1.1 Queue-Maschine

Eine Queue-Maschine besteht aus:

- einem endlichen Steuerzustand `q` (mechanisch etwa Schieber-/Rastzustände),
- einem FIFO-Speicher `Q` aus Symbolen,
- einer Übergangsregel, die vom Steuerzustand und dem vordersten Symbol abhängt.

Eine Regel liest/entfernt vorne ein Symbol, wechselt den Steuerzustand und hängt null oder mehrere Symbole hinten an. Formal kann ein Schritt etwa so aussehen:

```text
(q, a · rest)  →  (q_neu, rest · ausgabe)
```

Das ist eine knappe Abstraktion für „Murmeln laufen vorwärts am festen Kopf vorbei; der Kopf entscheidet, was am Ende der Folge wieder eingesetzt wird“. Geeignete deterministische Queue-Automaten sind im unbeschränkten mathematischen Modell universell.[1]

### 1.2 Kahn-Prozessnetz (KPN)

Ein KPN besteht aus einem gerichteten Netz:

- **Prozesse/Aktoren** sind eigenständige, sequentielle Programme mit eigenem Zustand.
- **Kanäle** sind gerichtete FIFO-Warteschlangen, durch die die Prozesse ausschließlich Daten austauschen.
- Prozesse laufen asynchron. Ein Prozess wartet beim Lesen, wenn sein Eingabekanal leer ist. Im abstrakten Modell kann ein Schreiben nicht daran scheitern, dass der Kanal voll ist, weil die Kanäle unbegrenzte Kapazität haben.[2][3]

Die Prozesse müssen deterministisch sein und den KPN-Kommunikationsregeln folgen. Dann hängt die Folge der erzeugten Kanalwerte nicht von der zufälligen relativen Geschwindigkeit oder der Reihenfolge der Prozessausführung ab; eine Kahn-MacQueen-Arbeit hält fest, dass dieselbe Ein-/Ausgabe entsteht, ob das Netz sequentiell oder parallel ausgeführt wird.[2][4]

Das ist **nicht** dasselbe wie „alle Kugeln sind synchron getaktet“. Es ist gerade ein asynchrones Modell: Jeder Prozess macht weiter, wenn die für seinen nächsten Schritt benötigten Eingaben da sind.

### 1.3 Ein KPN kann eine Queue-Maschine enthalten

Nimmt man einen deterministischen Prozess mit endlichem Steuerzustand, einer Eingabe und einer Ausgabe, und verbindet seine Ausgabe FIFO-mäßig wieder mit seiner Eingabe, erhält man im Wesentlichen eine Queue-Maschine: vorne lesen, Zustand ändern, Ausgabe hinten anfügen. Das folgt aus den Übergangsregeln der Queue-Maschine und dem FIFO-Kanalmodell.[1][3]

Daher ist die eigentliche Auswahl nicht „welches Modell kann überhaupt rechnen?“, sondern eher:

> Soll die Maschine primär *eine programmierte Symbolfolge an einem Verarbeitungskopf* ausführen, oder soll sie primär *ein Netz miteinander kommunizierender Prozesse* sein?

## 2. Wie die Modelle zu Murmeln und Bahnen passen

| Frage | Queue-Maschine | KPN |
|---|---|---|
| Grundstruktur | Eine geordnete Symbolfolge und ein zentraler Schritt-für-Schritt-Übergang. | Ein Graph aus Prozessen und mehreren gerichteten FIFO-Kanälen. |
| Wo ist der Zustand? | In der endlichen Kopfsteuerung und in der Reihenfolge/Inhalt der Queue. | In jedem Prozess sowie in den Puffern der Kanäle. |
| Bewegungsrichtung | Vorwärts am festen Kopf vorbei passt unmittelbar. Der logische Queue-Anfang und das Ende müssen bei einem Umlaufband trotzdem eindeutig markiert/realisiert werden. | Jeder Kanal hat Produzent und Konsument; Schwerkraft kann die Tokens von Prozess zu Prozess transportieren. Verzweigungen und Zusammenführungen sind sichtbare Teile des Programms. |
| Parallele Berechnung | Ein Queue-Schritt wird zentral verarbeitet. Viele Murmeln können gleichzeitig unterwegs sein, aber das ist noch keine Parallelität der Rechenlogik. | Unabhängige Prozesse können gleichzeitig Fortschritt machen. Die Ergebnisse sind in den einzelnen Kanälen deterministisch, aber ihre Wandzeit-Reihenfolge muss nicht fest sein.[2][4] |
| Programmträger | Ein Programmband als Eingabe für einen festen universellen Interpreter ist eine natürliche Zielvorstellung. Der konkrete Interpreter und dessen Bandgrammatik müssen noch gebaut werden. | Üblicherweise ist das Prozessnetz selbst das Programm: Prozessmodule und ihre Verbindungen beschreiben den Ablauf. Ein Programm *als Datenband* braucht einen Interpreter-Prozess; ein KPN macht das nicht automatisch. |
| Speicherzugriff | FIFO und sequenziell; kein beliebiger Direktzugriff auf eine frühere Stelle. | Mehrere unabhängige FIFO-Kanäle; ebenfalls kein RAM-artiger Direktzugriff, aber mehrere Teilströme können getrennt gespeichert und verarbeitet werden. |
| Mechanische Komplexität | Ein Kopf, eine Übergangstabelle, eine Queue-Grenze sowie Lesen/Entnehmen/Einfügen. | Mehr Prozesszustände, mehr Kanäle, mehr Puffergrenzen, Zusammenführungen und ein globales Ressourcen-/Blockierungsproblem. Dafür modularer und besser parallelisierbar. |
| Fehlerdiagnose | Ein Fehler lässt sich oft auf den zuletzt verarbeiteten Queue-Schritt zurückführen; der zentrale Ablauf kann langsam sein. | Ein Stillstand kann an einem beliebigen Prozess oder Kanal liegen. Eine sichtbare Pufferfüllung hilft, ersetzt aber keine Diagnose der Abhängigkeiten. |

## 3. Die wichtigste Programmierfrage: „Programm als Band“

### 3.1 Queue-Maschine: Bandinhalt ist der direkte Weg zum Programm

Bei der Queue-Variante kann das auswechselbare Band als Eingabe für einen festen universellen Interpreter gedacht werden. Der Interpreter ist die unveränderliche Mechanik; auf dem Band liegen die beschriebene Maschine bzw. das konkrete Programm sowie dessen Eingabe. Das passt zur Idee, dass nicht jede neue Berechnung eine neue Weichenlandschaft erfordert.

Der Preis dafür ist erheblich: Die Queue-Maschine muss die Programmbeschreibung korrekt parsen, ihren Ausführungszustand verwalten und Änderungen am Arbeitsinhalt darstellen. Das theoretische Ergebnis „Queue-Automaten sind universell“ beweist nicht, dass ein kleiner, praktischer, mechanisch implementierter Universalinterpreter bereits feststeht.[1] Die Übergangstabelle, das Symbolalphabet, die Rahmung der Daten und ein Simulator bleiben Vorbedingungen für eine ausführbare Primzahlsieb-Implementierung.

### 3.2 KPN: Das Prozessnetz ist oft selbst der Programmplan

In einem KPN sind Prozesse und Verbindungskanäle die Struktur des Programms. Ein Generator kann Kandidatenzahlen erzeugen, Filterprozesse können Werte verbrauchen oder weitergeben, und ein Ausgabemodul kann Ergebnisse sammeln. Kahn und MacQueen beschreiben Prozessnetze als eine natürliche Struktur für unabhängige Prozesse, deren Ein-/Ausgabeverhalten bei sequentieller oder paralleler Ausführung gleich bleibt.[2]

Für den Murmelrechner gibt es dann zwei grundsätzlich verschiedene Programmbegriffe:

1. **Programm ist die Netzgeometrie:** Die Bahnen, Weichen und Prozessmodule bilden das Programm. Das ist sehr anschaulich und modular, aber ein neues Programm kann bedeuten, die Mechanik anders zu stecken oder andere Prozessmodule zu laden.
2. **Programm ist ein Murmelband:** Ein fester Interpreter-Prozess liest Befehle vom Programmband und bedient Arbeitskanäle. Damit bleibt der gewünschte Band-Programmierstil erhalten, aber der Interpreter ist ein zusätzliches, komplexes Rechenmodell innerhalb des KPN.

Diese Unterscheidung muss vor einer KPN-Konstruktion ausdrücklich getroffen werden. Andernfalls kann die Maschine zwar sichtbar parallel rechnen, aber die *austauschbare Programmausführung auf einem Band* bleibt nur behauptet.

### 3.3 Unterprogramme und dynamische Prozesse

Ein KPN-Netz stellt parallele Prozesse bereit; es bedeutet nicht automatisch, dass ein beliebiger Prozess jederzeit ein Kindprogramm erzeugen darf. Prozess-Erzeugung, Kennungen, Rückgabekanäle, Speicherzuteilung und Lebensdauer müssten zusätzliche Regeln erhalten. Kahn und MacQueen untersuchen dynamisch sich entwickelnde Prozessnetzwerke in ihrem Sprach-/Coroutine-Kontext,[2] doch für die mechanische Maschine darf das nicht als kostenlose Standardfunktion vorausgesetzt werden.

Für V1 ist die klare Abgrenzung:

- **Queue-Kandidat:** zunächst ein serieller Interpreter; Unterprogramme synchron, Parallelität nicht vorausgesetzt.
- **KPN-Kandidat:** parallele Prozessmodule und Kanäle; Prozessnetz ist zunächst fest verdrahtet. Dynamisches Erzeugen weiterer Prozesse ist eine Erweiterung, keine Grundannahme.
- **KPN mit Band-Interpreter:** Das Band enthält den Anwendercode, während der feste KPN-Graph den Interpreter und die Arbeitskanäle bereitstellt. Das ist der mögliche Hybrid, aber die Komplexität muss im Simulator gemessen werden.

## 4. Parallelität: echter Vorteil, aber kein kostenloses Tempo

### 4.1 Was KPN wirklich hinzufügt

In einem KPN können zwei Prozesse unabhängig an ihren nächsten verfügbaren Tokens arbeiten. Beispielsweise kann ein Generator die nächsten Kandidaten erzeugen, während ein Filter bereits frühere Kandidaten verarbeitet. Mehrere Äste können zugleich Murmeln führen. Kahn-MacQueen beschreiben, dass entsprechende Netze dasselbe Ein-/Ausgabeverhalten sequenziell wie parallel zeigen.[2]

Das ist ein echter Vorteil für ein Schaustück: Die Maschine kann mehrere sichtbare Rechenstränge besitzen, deren Murmeln gleichzeitig laufen. Der Vorteil ist aber zuerst **Struktur und Überlappung**, nicht automatisch eine kürzere Wandzeit. Ein Prozess mit langsamer Lese-/Schreibmechanik, ein enger Kanal oder ein voller Ausgabepuffer kann den gesamten Fluss begrenzen.

### 4.2 Deterministisch heißt nicht „immer dieselbe zeitliche Animation“

KPN-Determinismus betrifft die erzeugten Datenströme/Ergebnisse, nicht zwingend die exakte zeitliche Reihenfolge, in der verschiedene Prozesse ihre Ausgaben produzieren. Eine Kamera kann denselben Kanalinhalt beobachten, aber die Reihenfolge der Zeitstempel über zwei unabhängige Kanäle kann je nach realen Reibungs- und Laufzeitunterschieden variieren. Eine Verlaufs-App sollte daher pro Kanal bzw. Prozess protokollieren und keinen nicht vorhandenen globalen Takt vortäuschen.

### 4.3 Was ein Queue-Rechner dennoch parallel zeigen kann

Ein Queue-Rechner kann viele Murmeln gleichzeitig in der Bahn haben, eine Rückführung und eine Ausgabespur parallel bewegen und damit visuell lebendig wirken. Das ist jedoch **Transportparallelität**, keine parallele Berechnung: Die zentrale Übergangslogik verarbeitet weiterhin jeweils einen Queue-Schritt.

Wenn für Falko „mehrere Kugeln laufen gleichzeitig“ genügt, kann der Queue-Kern ausreichen. Wenn mehrere unabhängige Rechenprozesse gleichzeitig eigene Zustände fortschreiben müssen, ist KPN die passendere Semantik.

## 5. Deine Idee: volle Puffer durch zusätzliche Bahn erweitern

Das ist nicht nur ein Ausweg, sondern kann ein zentraler Architekturbaustein werden. Die Standard-KPN-Abstraktion nimmt unbegrenzte Kanäle an und deshalb keine blockierenden Voll-Puffer-Schreibvorgänge.[3] Thomas Parks untersucht ausdrücklich bounded scheduling: Kapazitäten werden begrenzt und bei Bedarf erhöht, um Deadlocks zu vermeiden.[4] Das bestätigt die Richtung deiner Idee, mit einer wichtigen Einschränkung.

### 5.1 Was dynamische Erweiterung löst

Wenn ein KPN-Prozess ein Token ausgeben will, sein Kanal aber physisch voll ist, kann eine Erweiterung des Kanals die Ausgabe wieder ermöglichen. Bleibt die Reihenfolge erhalten und kann die Erweiterung rechtzeitig aktiviert werden, entspricht das einer wachsenden FIFO-Kapazität. Das ist wesentlich näher an der idealisierten KPN-Semantik als ein fest dimensionierter kurzer Puffer.

Eine bessere physische Regel als „erst den Kanal vollständig zustauen lassen“ wäre:

1. Der Kanal hat einen klar erkennbaren letzten belegbaren Abschnitt.
2. Ein mechanischer Füllmarker bzw. Druckhebel erkennt die Annäherung an die Grenze.
3. Noch bevor ein Schreibvorgang blockiert, wird ein vorgehaltener Zusatzabschnitt in Reihe geschaltet.
4. Ein FIFO-End-/Füllmarker wandert mit; die neue Bahn wird ausschließlich am logischen Ende angefügt.
5. Ist kein Zusatzabschnitt mehr verfügbar, zeigt die Maschine **Kapazität erschöpft** und hält kontrolliert an.

Das kann man manuell oder automatisch realisieren:

- **Manuell:** Der Rechner hält an, Falko setzt ein zusätzliches Segment ein und startet weiter. Das ist mechanisch einfacher und erhält die Rechenkorrektheit, aber nicht den unbeaufsichtigten Dauerlauf.
- **Automatisch mit Vorrat:** Ein Stapel vorgefertigter Segmente wird durch eine mechanische Verriegelung nacheinander in den Kanal geschaltet. Das ist autonomer, braucht aber eine zuverlässige Voll-Erkennung, FIFO-Endführung und sichere Wiederanlaufsequenz.
- **Theoretisch unbegrenzt:** Nur ein idealisiertes Modell mit unbegrenzt vielen Segmenten entspricht wirklich unbegrenztem Speicher. Ein reales Vorratsmagazin bleibt endlich.

### 5.2 Was dynamische Erweiterung nicht löst

Es gibt mindestens drei unterschiedliche Arten von Stillstand:

1. **Voll-Puffer-Blockierung:** Ein Prozess möchte schreiben, aber der Kanal ist voll. Eine rechtzeitige Erweiterung kann genau dieses Problem lösen.
2. **Leere-Eingabe-Blockierung:** Ein Prozess wartet auf ein Token, das kein anderer Prozess mehr erzeugt. Mehr Kanalvolumen hilft nicht.
3. **Mechanischer Fehler:** Verklemmen, Fehlleitung, doppelte Auslösung oder ein falsch geschaltetes Erweiterungsstück. Zusätzliche Kapazität behebt das nicht.

Außerdem ist ein KPN ein Netz aus mehreren Kanälen. Es reicht nicht immer, nur den gerade offensichtlichsten vollen Puffer zu vergrößern: Parks dokumentiert eine Folge von Deadlocks, die entstehen, wenn jeweils nur der kleinste volle Puffer erhöht wird.[4] Das heißt nicht, dass deine Erweiterungsidee falsch ist; es heißt, dass das **System** entscheiden bzw. mechanisch ermöglichen muss, welcher Kanal wächst, und dass eine Erweiterung den Datenfluss tatsächlich freigibt.

Parks zeigt ferner, dass die Fragen, ob ein beliebiges KPN terminiert bzw. beschränkte Pufferkapazität genügt, im allgemeinen Modell nicht für alle Programme entscheidbar sind.[4] Für eine feste, simulierte Primzahlaufgabe können wir den tatsächlichen Speicherbedarf messen. Für *jedes beliebige Programm* kann es dagegen keine allgemeine Zusage geben, dass ein endliches Reservoir stets reicht.

### 5.3 Nicht nur KPN kann wachsen

Eine Queue-Maschine hat ebenfalls einen FIFO-Speicher. Dieselbe mechanische Erweiterung kann auch ihre zentrale Queue verlängern. Dynamisches Verlängern macht KPN attraktiver, weil es dessen viele Kanäle dem idealisierten Modell annähert; es ist jedoch kein exklusiver KPN-Vorteil.

Der Unterschied ist der Aufwand:

- Queue-Kandidat: ein zentraler logischer Puffer braucht zusätzliche Bahnsegmente.
- KPN-Kandidat: jeder Kanal kann eine eigene Kapazität und Verlängerung benötigen. Das skaliert modular, aber es gibt mehr Füllstände, Endmarken, mögliche Erweiterungspunkte und Fehlersituationen.

## 6. Murmel-Erhaltung und Kopieren von Daten

Beide Modelle müssen der physischen Kugelerhaltung gehorchen.

- Eine Queue-Übergangsregel, die ein Symbol entfernt und zwei neue Symbole anhängt, braucht zwei Murmeln aus dem Vorrat; die entfernte Murmel muss in den Vorrat zurückkehren.
- Ein KPN-Prozess kann ein Token logisch an zwei Ausgänge schreiben (etwa ein Verteiler/Fork). Eine physische Murmel kann nicht gleichzeitig auf zwei getrennten Bahnen liegen. Das Netz benötigt deshalb eine zweite, gleich codierte Murmel aus dem Vorrat oder eine andere physische Kopieroperation.
- Ein KPN mit Verzweigungen kann also mehr Rechenstränge gleichzeitig zeigen, aber die Anzahl der benötigten Murmeln kann dadurch wachsen. Die Kugelzahl bzw. der Vorrat ist Teil des Rechenmodells, nicht bloß eine Förderfrage.
- Wenn ein Prozess einen Token „nur weiterleitet“, kann dieselbe Murmel durch eine Weiche auf den passenden Ausgang gelangen. Wenn er einen Token in mehrere unabhängige Ströme dupliziert, braucht es tatsächlich zusätzliche Informationsträger.

Beide Kandidaten brauchen außerdem klare Regeln für volle/leer gewordene Vorräte. Ohne passende Kugelgröße darf ein Schreibvorgang nicht stillschweigend ein falsches Symbol erzeugen.

## 7. Primzahlsieb als Vergleichsaufgabe

Das Primzahlsieb eignet sich gut, um die Modelle zu unterscheiden, weil es sowohl Datenfluss als auch veränderbaren Zustand benötigt.

### 7.1 KPN-Variante

Eine anschauliche KPN-Idee ist:

```text
Kandidaten-Generator → Filter 2 → Filter 3 → Filter 5 → Ausgabe
```

Für `N = 30` reicht ein fester Filterstrang für 2, 3 und 5, wenn die Filter korrekt initialisiert sind; die Prozessmodule könnten parallel an verschiedenen Murmeln arbeiten. Dabei steckt ein Teil des Programms aber in der Prozessgraph-Topologie und in den Filterparametern. Ein bandcodiertes Programm müsste diese Parameter laden oder einen Interpreter-Prozess anweisen, welche Filter/Regeln gelten.

Für größere N könnte das Netzwerk neue Filter für entdeckte Primzahlen brauchen. Das kann entweder einen dynamischen Prozess-Erzeugungsmechanismus verlangen oder die Berechnung in einem festen Interpreter bündeln. Ein fest verdrahteter Filterstrang ist ein sehr gutes KPN-Demonstrationsprogramm, beweist allein aber nicht, dass ein austauschbares Programmband beliebige Programme ausführt.

### 7.2 Queue-Variante

Der Queue-Interpreter kann ein Kandidaten-/Markierungsband sequenziell bearbeiten und die nötigen Wiederholungen als Programmschritte ausführen. Das dürfte langsamer und stärker sequenziell sein, ist aber direkter mit „ein Programm als Symbolfolge“ verbunden. Die genaue Sieb-Repräsentation muss im Simulator festgelegt werden; aus abstrakter Queue-Universalität folgt kein kleiner oder effizienter mechanischer Siebcode.

### 7.3 Fairer Vergleich

Verglichen werden sollte dieselbe vollständige Aufgabe: **Primzahlen bis einschließlich 30 korrekt ausgeben und definiert anhalten.** Vor den mechanischen Prototypen sollten beide Simulatoren mindestens protokollieren:

- Anzahl der Murmeln je Symboltyp und maximale Gesamtzahl,
- maximale Füllung jedes Queue-/KPN-Kanals,
- maximale Anzahl gleichzeitig aktiver Prozesszustände,
- Übergänge/Filterfeuerungen, die tatsächliche Laufzeit beeinflussen,
- welcher Zustand bei leerem Vorrat, voller Erweiterungsreserve oder fehlendem Eingang entsteht,
- ob dieselbe Eingabe unabhängig vom Prozess-Tempo dieselbe Ausgabe liefert.

KPN sollte nicht nur gegen ein ineffizientes Queue-Programm antreten; Queue sollte auch nicht mit einem komplett vorverdrahteten KPN-Sieb verglichen werden. Beide müssen denselben Programmieranspruch erfüllen: Ist die Berechnung wirklich als austauschbare Bandfolge kodiert, oder ist die Funktion in der Mechanik festgelegt?

## 8. Korrektheit, Synchronisation und Fehlersuche

### Queue-Kern

- Es gibt eine klare Reihenfolge aller Symbole.
- Jeder Kopf-Schritt muss genau ein Eingabesymbol verarbeiten und seine Ausgabe am Ende einfügen.
- Ein verlorenes oder zusätzliches Symbol kann alle nachfolgenden Daten bzw. Paarkodierungen verschieben.
- Der zentrale Kopf ist ein natürlicher Debug-Punkt; Stillstand kann am letzten gelesenen Symbol untersucht werden.
- Der Hauptengpass ist die zentrale Lese-/Schreibstelle. Ein schneller Motor kann den einzelnen Rechenschritt nicht beschleunigen, wenn die mechanische Steuerung noch nicht zurückgesetzt ist.

### KPN

- Jeder Prozess braucht einen definierten internen Zustand und eine definierte Reihenfolge seiner Lese-/Schreibaktionen.
- Beim Lesen aus einem leeren Kanal wartet der Prozess. Dieses Warten ist normal und nicht automatisch ein Fehler.
- Der Prozess darf im klassischen KPN-Modell nicht über einen zeitabhängigen „wer zuerst ankommt“-Test beliebig zwischen Eingängen wählen; das würde die deterministische Semantik gefährden.[3][4]
- Unabhängige Prozesse haben keine fest vorgegebene globale Ereignisreihenfolge. Die App sollte Kanal-/Prozesszustände anzeigen und nicht jeden Zeitstempel als Bedeutung des Programms behandeln.
- Diagnose ist verteilter: Man muss unterscheiden, ob ein Prozess auf Eingabe wartet, ein Kanal wegen Kapazität blockiert, ein Vorrat erschöpft ist oder eine Bahn mechanisch klemmt.

Beide Modelle können ohne festen globalen Takt arbeiten. Die Queue braucht eine zuverlässige Ereignis- und Rückstellmechanik am Kopf; ein KPN braucht zusätzlich pro Actor einen verlässlichen Ablauf, der Eingaben annimmt, Zustände setzt und Ausgaben erzeugt, ohne Kugeln mehrfach auszuwerten.

## 9. Wahlhilfe: Welche Priorität führt zu welcher Variante?

| Wenn Falkos höchste Priorität ist … | Dann spricht mehr für … | Warum |
|---|---|---|
| „Das Programm selbst ist ein austauschbares Murmelband.“ | **Queue-Kern** | Die Queue ist direkt der sequenziell gelesene Code-/Datenstrom; ein fixer Interpreter ist konzeptionell passend. |
| „Mehrere Rechenpfade sollen wirklich gleichzeitig arbeiten.“ | **KPN** | Prozesse und Kanäle stellen parallele Abläufe als Grundform dar, nicht bloß als mehrere Kugeln unterwegs. |
| „Ich will möglichst wenige verschiedene Mechaniktypen und einen zentralen Debug-Punkt.“ | **Queue-Kern** | Ein Übergangskopf und eine Hauptqueue sind weniger verteilt. |
| „Abzweigungen, Filter und mehrere unabhängige Murmelströme sind das Schaustück.“ | **KPN** | Der Prozessgraph kann physisch als Netz aus Bahnen und Modulen sichtbar werden. |
| „Puffer sollen modular wachsen können.“ | **Beide profitieren** | Die Verlängerung erweitert sowohl eine Queue als auch KPN-Kanäle; bei KPN gibt es dafür mehr einzelne Speicherorte. |
| „Programme sollen Prozesse dynamisch starten.“ | **Keines ohne Zusatzdesign** | KPN bietet Parallelität, aber Ressourcen, IDs, Rückgaben und Prozesslebensdauer brauchen trotzdem Regeln; ein Queue-Interpreter braucht ebenfalls Scheduler-/Prozesscodierung. |

### Meine vorläufige Bewertung

Die neue Verlängerungsidee hebt einen wichtigen Einwand gegen KPN teilweise auf: feste, kurze Puffer sind keine zwingende Grenze, wenn Kanäle korrekt und geordnet erweiterbar sind. Das macht KPN **deutlich ernsthafter** als in der vorigen Zwischenfassung. Trotzdem entscheidet sie die Wahl nicht allein:

- Für *„eine Maschine führt austauschbaren Bandcode aus“* bleibt Queue die sauberere Rechensemantik.
- Für *„ein modularer, paralleler Murmel-Datenfluss-Rechner“* ist KPN die klarere Systemsemantik.
- Für beides zusammen ist eine **KPN-Hülle mit einem bandprogrammierbaren Queue-Interpreter als Rechenprozess** möglich. Sie bewahrt den Bandcode, erlaubt parallele Infrastruktur und kann später echte parallele Actors hinzufügen. Sie ist aber auch die komplexeste Option und darf nicht als kostenloser Kompromiss verkauft werden.

Ich würde jetzt noch keine der beiden Varianten endgültig festschreiben. Der sinnvollste Auswahltest ist ein gemeinsamer Simulator für das Primzahlsieb bis 30 plus je ein kleinster mechanischer Prototyp: (A) ein Queue-Kopf, der Symbole liest, austauscht und hinten einfügt; (B) zwei oder drei KPN-Actors mit FIFO-Kanälen, einem sichtbaren vollen/leer Zustand und einer tatsächlich erweiterbaren Kanalstrecke. Danach lassen sich echte Kugelzahl, Erweiterungsbedarf, Fehlerpunkte und Laufzeit vergleichen.

## 10. Noch zu entscheiden und konkret zu testen

1. **Welche Bedeutung hat „Programm als Band“?** Soll der Code allein in einer linearen Symbolfolge liegen, oder darf die physische Prozessgraph-Geometrie Teil des Programms sein?
2. **Wie viel echte Parallelität ist gewünscht?** Mehrere gleichzeitig rollende Murmeln allein zählen nicht als parallel laufende Rechenprozesse.
3. **Wie wird ein voller Kanal erweitert?** Manuelles Anhalten und Nachrüsten, ein Vorratsmagazin mit automatischer Umschaltung oder eine vorgeplante, gefaltete Erweiterungsbahn? Was passiert, wenn der Vorrat an Segmenten erschöpft ist?
4. **Wie wird KPN-Fortschritt angezeigt?** Pro Kanal/Prozesszustand statt vermeintlich globaler Schrittfolge.
5. **Welche N=30-Siebimplementation ist am fairsten?** Festes Filter-Netz für KPN versus bandcodierter Algorithmus; die Programmiersprache muss beide Anforderungen erfüllen.
6. **Welche Kugel-/Symbolversorgung steht für Verzweigung/Kopie zur Verfügung?** Ein KPN-Verteiler muss für echte Kopien zusätzliche Murmeln aus dem Vorrat bekommen.

Diese Punkte sind keine Gründe, KPN auszuschließen. Sie sind die genaue Form, in der seine Vorteile und Kosten mechanisch überprüfbar werden.

## Sources

[1] https://ir.cwi.nl/pub/36315/36315.pdf — Queue Automaton Revisited (Baeten & Luttik)
[2] https://pdos.csail.mit.edu/~rsc/kahn77parallel.pdf — Coroutines and Networks of Parallel Processes (Kahn & MacQueen, 1977)
[3] https://ptolemy.berkeley.edu/publications/papers/95/processNets/proceedings.pdf — Dataflow Process Networks (Lee & Parks, 1995)
[4] https://ptolemy.berkeley.edu/publications/papers/95/parksThesis/thesis.pdf — Bounded Scheduling of Process Networks (Parks, 1995)
