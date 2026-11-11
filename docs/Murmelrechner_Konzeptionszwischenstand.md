# Murmelrechner – geprüfter Konzeption-Zwischenstand

**Zweck:** Ausgangspunkt für die nächste Entwurfsrunde. Das Dokument trifft Architekturentscheidungen, trennt belegte Theorie von technischen Hypothesen und lässt nur Punkte offen, die tatsächlich einen konkreten Mechanik-Prototyp oder eine formale Konstruktion benötigen. Es ersetzt weder die ursprüngliche Ideensammlung noch behauptet es, dass bereits ein funktionierender Rechner entworfen oder getestet wurde.

## 1. Kurzentscheidung

Der passendste Ausgangspunkt für Falkos konkrete Bewegungs-Idee ist **kein unverändert übernommenes klassisches Turingband**, sondern ein **deterministischer FIFO-/Warteschlangenrechner (Queue-Maschine)**:

- Die **Murmeln bilden eine geordnete Symbolfolge**.
- Die Folge bewegt sich durch Schwerkraft an einer **feststehenden Lese-/Bearbeitungsstelle** vorbei.
- Ein Schritt liest das vorderste Symbol, ändert den endlichen mechanischen Steuerzustand und gibt null oder mehrere neue Symbole am Ende der Folge aus.
- Das **Programm ist als Symbolfolge auf dem Murmelband** codiert und wird von einem fest eingebauten universellen Interpreter ausgeführt. Der Interpreter ist die feste Mechanik; das konkrete Programm ist austauschbarer Bandinhalt.
- Für die erste Ausbaustufe wird **ein Programmablauf seriell** ausgeführt. Es dürfen viele Murmeln gleichzeitig in der Bahn sein, aber nicht mehrere unabhängige Berechnungen gleichzeitig laufen.
- Als vorläufige Symbolcodierung wird **eine Murmel pro balanciertem Ternärsymbol** gewählt: klein = −1, mittel = 0, groß = +1. Die früher erwogene Zweikugel-Kodierung wird nicht als Grundlage verwendet.
- Der Motor ist ausschließlich Transportenergie für den Rückhub. Kamera/Tablet sind ausschließlich Ausgabe und Beobachtung, ohne Rückwirkung auf die Berechnung.

Queue-Speicher passen natürlicher zu einem stets vorwärts laufenden Symbolstrom als die klassische Turingmaschine, deren Kopf links, rechts oder auf derselben Bandzelle stehen bleiben kann.[1] Geeignete deterministische Queue-Automaten sind theoretisch gleich mächtig wie Turingmaschinen; das beweist aber noch nicht, dass eine konkrete, kompakte mechanische Universal-Queue-Maschine bereits feststeht.[10]

**Wichtig:** Universalität gilt nur für das idealisierte Modell mit unbegrenzter Queue, Symbolversorgung und Laufzeit. Ein realer Druck mit endlichem Band, endlichem Murmelvorrat und endlichen Puffern ist stets kapazitätsbegrenzt. Die Maschine kann eine skalierbare universelle *Architektur* anstreben; ein konkreter Aufbau kann nur bis zu seiner physischen Grenze rechnen.

## 2. Festgehaltene Anforderungen und bewusste Auslegungen

| Thema | Zwischenstands-Entscheidung |
|---|---|
| Charakter | Schaustück/„Zen-Computer“, kein Lernmodell; Korrektheit und sichtbarer Ablauf wichtiger als Geschwindigkeit. |
| Rechenart | Rein mechanische Logik. Elektronik darf nur die Kamera-/Tablet-Ausgabe und den nötigen Motorantrieb betreffen; sie darf keine Zustände berechnen oder Weichen steuern. |
| Antrieb | Schwerkraft treibt den Rechenfluss; Motor hebt Murmeln zurück. Die Zickzack-/Sägezahn-Treppe ist eine plausible Aufzugsidee, aber noch kein validiertes Bauteil. |
| Kopf und Band | Der Kopf bleibt fest; das Murmelband bewegt sich darunter hindurch. Das ist eine harte Architekturvorgabe, keine bloße Darstellungsvariante. |
| Mehrere Murmeln | Viele Murmeln dürfen gleichzeitig im System unterwegs sein. V1 verarbeitet jedoch jeweils ein Symbolereignis an der Bearbeitungsstelle; echte parallele Prozesse werden zurückgestellt. |
| Programm | Das austauschbare Programm ist Murmelband-Inhalt. Es wird nicht fest in die Mechanik verdrahtet. Eine feste mechanische Steuerung interpretiert die codierte Programmbeschreibung. |
| Ausgabe | Kamera liest die tatsächlichen Ausgabesymbole; die App zeigt den Verlauf und wandelt die gewählte Zahlendarstellung in Dezimalzahlen um. Sie ist ein Beobachter, kein Rechnerteil. Bei balanciertem Ternär zeigt sie also Ternär-zu-Dezimal an, nicht zwingend Binär-zu-Dezimal. |
| CPU/ALU/Takt | Keine getrennte klassische CPU/ALU und kein zeitbasierter globaler Takt in V1. Die feste mechanische Steuerung hält einen endlichen Zustand; die FIFO-Übergänge führen die Rechenschritte aus. Addition/Vergleich werden zunächst als Programmabläufe über Symbole realisiert. Ein Ereignis und eine mechanische Rückstellung trennen Verarbeitungsschritte. |
| Beispielanwendung | Erstes verbindliches Belastungsziel: vollständiges, ausführbares Programm, das Primzahlen bis einschließlich **N = 30** ausgibt. N = 100 ist ein späterer Ausbau. Keine Laufzeitvorgabe; langsamer Betrieb ist akzeptabel. |
| Fertigung | 3D-Druck für die modularen Mechanikteile. Als vernünftige Arbeitsannahme sind handelsübliche Murmeln/Kugeln sowie passive Achsen, Stifte, Lager und Federn erlaubt. Elektronische Rechen- oder Steuersensoren sind ausgeschlossen. |

Die Festlegung auf N = 30 ist eine Projektentscheidung zur Begrenzung des ersten vollständigen Tests, kein vom Nutzer vorgegebenes Ziel. Die Referenzausgabe lautet: **2, 3, 5, 7, 11, 13, 17, 19, 23, 29**. Das klassische Sieb des Eratosthenes benötigt veränderbare Markierungen; Standardvarianten haben je nach Variante Speicherbedarf proportional zu N oder können segmentiert werden.[9] Die konkrete Murmelprogrammierung muss noch zeigen, ob dieses Sieb oder ein gleichwertiger Primzahlalgorithmus besser zur Queue-Architektur passt.

## 3. Warum Queue-Maschine statt klassischer Turingmaschine?

### 3.1 Klassische Turingmaschine: programmierbar, aber Bewegungsmodell passt nicht direkt

Eine klassische Turingmaschine hat ein Band mit einzelnen Zellen und einen Lese-/Schreibkopf, der pro Schritt links, rechts oder gar nicht bewegt wird.[1] Ein universeller Interpreter kann eine Beschreibung der simulierten Maschine und deren Eingabe als Daten codiert verarbeiten.[2] Das bestätigt den Grundgedanken „Programm auf dem Band“.

Der Haken ist mechanisch: Bei Falkos Vorgabe bewegt sich das Band durch Schwerkraft **in eine Richtung** unter einem festen Kopf. Das ist nicht automatisch derselbe Mechanismus wie ein Kopf, der frei nach links und rechts läuft. Man könnte eine Turingmaschine durch Umkehrung oder wiederholtes Scannen simulieren, aber das wäre ein zusätzlicher, noch zu konstruierender Mechanismus. Die bisherige Claude-Analyse empfahl daher zu früh „Turingband/Zwei-Stapel“, ohne die feste Leserichtung und den stationären Kopf in eine konkrete Übergangsmechanik zu übersetzen.

### 3.2 Queue-Maschine: das natürlichere Rechenmodell für Vorwärtsfluss

Eine Queue-Maschine hat endlichen Steuerzustand und FIFO-Speicher: Symbole werden in Eingangsreihenfolge gelesen; neue Symbole werden hinten angefügt.[3] In der formalen Übergangsdefinition kann der Automat das vorderste Queue-Symbol entfernen, einen Zustand wechseln und eine endliche Symbolfolge hinten anhängen.[10] Deterministische Queue-Automaten sind im abstrakten Modell genauso ausdrucksstark wie Turingmaschinen.[10]

Das passt zur gewünschten Mechanik: Murmeln laufen vorwärts, die feste Station liest die vorderste Murmel, und erzeugte Symbole werden am hinteren Ende wieder eingespeist. Der Rechner muss nicht versuchen, eine Murmelbahn wie einen frei beweglichen bidirektionalen Turingkopf zu behandeln.

**Programm auf dem Band:** Das Ziel ist eine feste universelle Queue-Steuerung, die eine Programmbeschreibung als Teil des anfänglichen Queue-Inhalts interpretiert. Das Prinzip, eine Programmbeschreibung samt Eingabe als Daten einzuspeisen, ist für universelle Maschinen etabliert.[2] Für dieses Projekt muss aber noch eine konkrete universelle Queue-Interpreter-Konstruktion mit Symbolalphabet, Anfangsformat und Simulation bewiesen bzw. aus einer belegten Konstruktion übernommen werden. Bis dahin ist „Queue-Maschine“ eine begründete Architekturwahl, noch keine fertige Universalitätsimplementierung.

### 3.3 Warum die anderen Kandidaten nicht V1 werden

| Kandidat | Faktencheck und Entscheidung |
|---|---|
| **Zwei-Zähler-/Minsky-Maschine** | Geeignete Zwei-Zähler-Modelle sind universell; gebräuchliche Befehle sind Zähler erhöhen sowie bedingt verringern/bei Null verzweigen.[5] Als Murmelzähler braucht jeder Inkrementvorgang eine verfügbare Murmel, und ein zuverlässiger Nulltest verlangt eine eindeutig ruhende, abgegrenzte Zählerbahn. Unärspeicherung und Vorratsverwaltung machen das weniger passend als einen Symbolstrom. Nicht V1. |
| **Tag-/Post-System** | Historisch eng mit universellen Queue-Produktionen verbunden; es kann ein später konkreter Kandidat für den Interpreter sein.[4] Noch keine Entscheidung für eine bestimmte Tag-System-Variante, weil deren Regeln und Programmcodierung erst festgelegt werden müssen. |
| **Kahn-Prozessnetz** | Gut für echte Parallelität, aber die klassische Semantik nutzt FIFO-Kanäle unbegrenzter Kapazität; Lesen blockiert, Schreiben ist nicht blockierend.[8] Endliche mechanische Puffer können Rückstau verursachen; daraus folgt nicht automatisch immer ein Deadlock, aber die klassische Garantie kann nicht unbesehen auf endliche Murmelbahnen übertragen werden. Parallelität daher nicht V1. |
| **Regel 110** | Der bekannte Universalitätsnachweis definiert eine unendliche eindimensionale Zellreihe mit synchroner Aktualisierung.[6] Ein fest dimensionierter endlicher Ring hat nur endlich viele Konfigurationen und kann nicht unbeschränkt universell sein. Mit wachsendem Speicher wäre das ein anderes, erheblich schwieriger zu erweiterndes System. Nicht V1. |
| **Klassische Turingmaschine / Zwei Stapel** | Eine klassische Turingmaschine ist theoretisch universell.[1] Die Zwei-Stapel-Idee benötigt Push/Pop an den jeweiligen Speicherenden; das ist nicht die vorgegebene Einwegbewegung. Nicht die direkteste physische Abstraktion. |

## 4. Informationscodierung: Entscheidung für eine Murmel pro balanciertem Trit

### 4.1 V1-Codierung

Für die erste Konzeption gilt vorläufig:

- kleine Murmel = −1
- mittlere Murmel = 0
- große Murmel = +1
- eine Murmel codiert genau ein Trit; eine Zahl oder ein Befehl ist eine Folge von Murmeln
- Folge-Reihenfolge ist Information; exakte Zeitabstände zwischen Symbolen sind es nicht

Balanciertes Ternär verwendet Ziffern {−1, 0, +1}; Vorzeichenwechsel entsteht durch stellenweises Vertauschen von +1 und −1. Für gebrochene Zahlen hat Abschneiden der unbedeutenden Stellen eine symmetrische Rundungseigenschaft mit Fehler unter einer halben ULP.[7] Diese mathematischen Vorteile beweisen nicht, dass drei Kugelgrößen mechanisch besser als zwei sind.

Für die formale Erstfassung wird eine Murmel pro Trit als Arbeitsbaseline verwendet: Sie passt zur seriellen Queue, vermeidet Paargrenzen und benötigt nur ein Symbol pro Trit. Die frühere Zweikugel-Kodierung mit zwei Typen ist aber nicht widerlegt: Sie braucht nur zwei Größenklassen, kann also beim Sortieren Vorteile haben. Ein mögliches Mapping ist `00 = 0`, `01 = +1`, `10 = −1`, `11 = Sonderwert`; dann bildet Vertauschen der zwei Symbole +1 und −1 aufeinander ab. Physisch müsste dafür allerdings das Paar gepuffert und in umgekehrter Reihenfolge wieder ausgegeben werden — ein möglicher Vorteil, kein bereits bewiesener einfacher Mechanismus. Die Paarvariante verdoppelt die Kugelzahl pro Trit und braucht zuverlässige Paar-Ausrichtung bzw. Wiederherstellung nach einem Verlust/Einfügen. Ob drei Größen oder zwei Größen in Paaren mechanisch besser sind, kann nur derselbe Lese-/Schreib-Prototyp entscheiden.

Ein einfacher Größen-Sortierer braucht für drei Größen zwei mechanische Entscheidungsschwellen. Das ist aufwendiger als eine Binärweiche, aber grundsätzlich konstruierbar. **Nicht belegt ist**, dass die drei Größen auf dem konkreten Drucker und in der gewählten Rinne zuverlässig genug unterschieden werden. Falls der 3-Größen-Prototyp scheitert, wird die Zweikugel-Ternärkodierung gegen Ein-Murmel-Binär getestet; ein automatischer Rückfall auf eine Variante ist noch nicht begründet.

Reine Wertstellenbelegung ohne Rahmen reicht nicht: Programm, Zahl, Parameter und Ende müssen eindeutig erkennbar sein. V1 muss deshalb eine formale, selbstabgrenzende Datensatzgrammatik verwenden (z. B. explizite Feldlängen und definierte Opcode-Arity). Ein doppeltes Sonderzeichen allein ist keine ausreichende Spezifikation, solange nicht ausgeschlossen ist, dass dieselbe Folge in Nutzdaten vorkommt. Die konkrete Trit-Folge für OpCodes/Framing wird im nächsten Spezifikationsschritt festgelegt.

### 4.2 Binärvergleich ohne falschen Absolutheitsanspruch

Binär benötigt zwei Symbolgrößen und ist mechanisch einfacher zu klassifizieren. Ternär benötigt drei, kann aber Werte kompakter darstellen. Beispiel nur für den Zahlenwert 0 bis 30, ohne Programmkopf, Vorzeichen-/Feldrahmen oder Trennzeichen: 5 Binärstellen reichen für 30; 4 balancierte Trits decken −40 bis +40 ab. Damit ist die reine Zahlenfolge hier um eine Stelle kürzer. Diese 20-%-Ersparnis auf Zahlenstellen allein beweist keinen geringeren Gesamtaufwand: Größe-Sortierung, Steuerwörter und Fehlerkorrektur sind nicht eingerechnet.

**Entscheidung:** Für die formale Spezifikation ist Ein-Murmel-pro-balanciertem-Trit die Arbeitsbaseline, passend zur Nutzerpräferenz. Die physische Kodierung ist damit **noch nicht endgültig festgeschrieben**: Vor der endgültigen Geometrie werden Ein-Murmel-Ternär, Zwei-Murmel-Ternär und Ein-Murmel-Binär an derselben Lese-/Schreibaufgabe verglichen. Kriterien sind Fehlerrate, Wieder-Ausrichtung nach Störung, erforderliche Bauteile, Kugelzahl und Rücktransport.

## 5. Speicher, Schreiben und Kugelerhaltung

### 5.1 Queue-Speicher ist sequenziell, nicht RAM

Ein FIFO-Speicher kann lang werden, aber er bietet keinen beliebigen Direktzugriff. Daten werden in Reihenfolge verarbeitet; eine bestimmte frühere Stelle ist nicht wie eine Speicheradresse sofort verfügbar. Das ist eine bewusste Architekturentscheidung und passt zur bewegten Murmelbahn. Algorithmen müssen deshalb als Durchläufe, Warteschlangenoperationen und ggf. wiederholte Scans formuliert werden.

Die Programm- und Datenbereiche können logisch auf demselben Band liegen; sie brauchen aber ein eindeutiges Format, damit Nutzdaten nicht versehentlich als ausführbarer Code interpretiert werden. V1 verwendet einen festen Interpreter und einen klar markierten Programm-/Eingabeblock. Selbstverändernder Code bleibt ein späterer Modus.

### 5.2 Kugeln werden nicht verwandelt

„Schreiben“ darf nicht bedeuten, dass sich eine kleine Murmel magisch in eine große verwandelt. Für eine Ausgabefolge mit geändertem Symbol muss die alte Murmel in einen Vorrat zurückgeführt und eine Murmel des gewünschten Typs aus einem Vorrat ausgegeben werden. Erforderlich sind daher:

1. aktiver Queue-Inhalt,
2. Vorrat nach Symboltyp,
3. temporäre Puffer für gelesene/ausgegebene Murmeln,
4. definierter Zustand bei leerem Symbolvorrat oder voller Queue.

Die Zahl jeder Kugelgröße bleibt im Gesamtsystem konstant. Reicht der Vorrat nicht, muss die Maschine sichtbar und reproduzierbar anhalten; sie darf nicht still falsch weiterrechnen. Die physische Maschine bleibt endlich, auch wenn das abstrakte Queue-Modell unbeschränkt ist.

### 5.3 Erforderliche Speicher-/Transportmechanik

Für ein umlaufendes Band braucht es eine eindeutige logische Grenze zwischen Queue-Anfang und Queue-Ende, sonst ist eine Ringfolge nicht von selbst eine FIFO-Warteschlange. Außerdem muss der Mechanismus folgende Operationen einzeln demonstrieren:

- vorderste Murmel erkennen und auslesen,
- alte Murmel aus der aktiven Folge entnehmen oder in den Vorrat zurückführen,
- null oder mehrere Ausgabemurmeln aus dem passenden Vorrat bereitstellen,
- Ausgabe am Queue-Ende einfügen,
- Reihenfolge der übrigen Murmeln erhalten,
- Überlauf, Unterlauf und Stau sichtbar behandeln.

Das ist die zentrale mechanische Machbarkeitsfrage. Eine reine Schwerkraft-Rückführschleife beweist noch nicht, dass Queue-Operationen funktionieren.

## 6. Motor, Größenunterschiede und Zuverlässigkeit

Die Sägezahn-/Treppenidee für den Aufzug ist plausibel und wird nicht verworfen. Eine Stufe kann prinzipiell so gestaltet werden, dass sie eine bestimmte Größenbandbreite hebt. Zu testen ist aber, ob die kleinste Kugel nicht durchrutscht, die größte nicht klemmt, die Reihenfolge erhalten bleibt und die Zuführung keine zweite Kugel zu früh freigibt. „Der Aufzug ist unproblematisch“ ist daher noch keine geprüfte Tatsache.

Die bisher im Entwurf genannten pauschalen Werte wie „FDM-Toleranz ±0,2 mm“, Kanalbreite „Kugeldurchmesser plus einige Zehntel“ oder ein fixes Gefälle werden **nicht** als Konstruktionsdaten übernommen. Drucker, Material, Ausrichtung, Kugelmaß und Oberflächenqualität sind projektspezifisch; die passenden Werte müssen durch Teststücke bestimmt werden.

Zeit darf nicht als Datensymbol dienen. Die Mechanik ist nicht exakt getaktet genug, um „nach 0,3 Sekunden“ als zuverlässige Information zu behandeln. Stattdessen wird jedes Symbol durch ein Ereignis verarbeitet; eine Sperre/Einzelzuführung muss die Station wieder in einen definierten Zustand bringen, bevor das nächste Symbol sie auslöst. Variable Abstände zwischen Murmeln sind damit zulässig, aber die Mechanik braucht dennoch genügend Zeit bzw. eine Verriegelung zum Rücksetzen.

Fehlerziel für Prototypen: verlorene, doppelte oder fehlgeleitete Murmeln dürfen nicht unbemerkt bleiben. Bei Stau/Überlauf muss ein sichtbarer Halt möglich sein. Die im Claude-Entwurf vorgeschlagenen 1.000 Zyklen ohne Fehler sind als erster Abnahmetest verwendbar, aber noch kein statistischer Nachweis für lange Programme.

## 7. Parallelität, Unterprogramme und Selbstmodifikation

V1 ist ein **deterministischer serieller Queue-Interpreter**. Mehrere Murmeln können gleichzeitig unterwegs sein, aber das bedeutet nicht, dass mehrere Berechnungen synchron laufen. Das löst die bisherige Mehrdeutigkeit zwischen „mehrere Kugeln im Schaustück“ und „echte parallele Subprogramme“.

- Keine nebenläufigen Prozesse und kein Scheduler in V1.
- Keine Kind-Subprogramme, die gleichzeitig weiterlaufen.
- Unterprogramme, sofern sie für das erste Programm nötig sind, laufen synchron und geben zurück, bevor der Aufrufer fortfährt; andernfalls wird das Beispielprogramm ohne Unterprogramme formuliert.
- Selbstmodifikation ist keine Voraussetzung für Universalität. Sie wird nach dem ersten stabilen Interpreter ergänzt, weil Schreibzugriff auf Programmcode die Fehlerdiagnose und Framing deutlich erschwert.
- Code aus berechneten Zahlen wird erst dann freigegeben, wenn die Zahlen-zu-Programm-Codierung formal festgelegt und durch den Simulator getestet ist.

## 8. Primzahlprogramm und realistischer V1-Abnahmepunkt

Der erste End-to-End-Test ist ein **vollständiges Programm** für Primzahlen bis N = 30, nicht nur ein Pseudocode-Beispiel. Die Ausgabe muss exakt die zehn Referenzwerte im Abschnitt 2 enthalten und einen definierten Abschlusszustand erreichen.

Empfohlene Vorgehensweise:

1. Einen exakten universellen Queue-Interpreter und sein Eingabe-/Programmbandformat festlegen.
2. Diese Regeln als Simulator implementieren.
3. Ein Primzahlprogramm (bevorzugt Eratosthenes-Sieb; falls dessen Speicherzugriff in diesem Modell unnötig aufwendig ist, einen klar begründeten gleichwertigen Algorithmus) in der tatsächlichen Programmsprache schreiben.
4. Simulator muss dieselben Ausgabeereignisse und dieselbe Terminierung wie die Spezifikation liefern.
5. Erst danach die minimale Lese-/Schreib-/Weichenmechanik für die tatsächlich verwendeten Übergänge bauen.
6. Mechanische Tests und Simulator-Trace anhand einzelner Murmelereignisse vergleichen.

Die 1.000-Zyklen-Prüfung aus dem Entwurf kann als erster Bauteiltest dienen. Für die Integration werden zusätzlich Queue-Überlauf, leerer Vorrat, Stau, Halt/Neustart und reproduzierbares Ergebnis getestet. Laufzeit ist zunächst keine Abnahmebedingung.

**Nicht behauptet:** Ein vollständiges ausführbares Primzahlsieb liegt in diesem Zwischenstand noch nicht vor. Es wäre fachlich falsch, das zu behaupten, bevor Programmiersprache und konkreter Queue-Interpreter existieren.

## 9. Prüfung und Korrekturen am gelieferten Claude-Review

### Belastbare bzw. nützliche Punkte

- Kugelerhaltung, endliche Ressourcen, sequenzieller Zugriff und der Unterschied zwischen Kapazität und Adressierbarkeit wurden richtig als Kernprobleme erkannt.
- Die Warnung, Universalität nicht mit praktischer Effizienz zu verwechseln, ist richtig.
- Die Empfehlung, erst ein Rechenmodell zu definieren, dann zu simulieren und erst danach die Mechanik festzulegen, ist sinnvoll.
- Die zwei Zähler sind in geeigneten idealisierten Varianten tatsächlich universell, aber die Übertragung auf Murmelzählung verlangt unbegrenzten Vorrat und einen sicheren Nulltest.[5]
- Der Regel-110-Hinweis ist im Kern korrekt: Cook behandelt eine unendliche Reihe und synchrone Aktualisierung.[6]
- Balanciert ternär hat echte mathematische Eigenschaften; diese sind kein Beweis für mechanische Überlegenheit.[7]

### Korrekturen / Präzisierungen

1. **Architektur-Widerspruch:** Das Review schlägt als Priorität eine Turing-/Zwei-Stapel-Maschine vor, definiert aber nicht, wie ein ausschließlich vorwärts durch Schwerkraft bewegtes Band einen links/rechts wandernden Lese-/Schreibkopf simuliert. Die Queue-Architektur ist näher an der tatsächlichen Kinematik.
2. **Turing-Programm auf Band:** Das ist nicht grundsätzlich falsch. Eine universelle Maschine kann Programm- und Eingabebeschreibung als Daten erhalten.[2] Entscheidend ist aber, dass die feste Interpreter-Mechanik definiert wird und das Bandformat eindeutig ist.
3. **Begrenzte Puffer:** Begrenzte Puffer führen nicht automatisch immer zu Deadlock. Die klassische Kahn-Semantik setzt jedoch unbegrenzte Kanäle voraus; eine endliche Mechanik muss daher separat auf Rückstau und Blockierung analysiert werden.[8]
4. **Leere Zelle:** „Keine Murmel“ ist in einer losen, nachrückenden Schlange kein zuverlässiges Symbol. In einer mechanisch abgegrenzten, festen Tasche wäre Abwesenheit dagegen ein stabil erkennbarer Zustand. V1 braucht keine leeren Datenzellen, wenn es als Queue arbeitet; leere Kapazität ist ein physischer Pufferzustand.
5. **Signum bei balanciertem Ternär:** Das Vorzeichen ergibt sich aus dem höchstwertigen Nichtnull-Trit. Bei einer niederwertig-zuerst gelesenen Folge kennt der Kopf das Vorzeichen nicht sofort, muss aber nicht zwingend die ganze Zahl in einem separaten Speicher kopieren: Ein Durchlauf kann es feststellen, wenn die Datenorganisation das erlaubt.
6. **FDM-Toleranzen:** Die angegebenen ±0,2 mm sind keine universelle Konstruktionsgarantie. Sie werden nicht als Fakt übernommen.
7. **„Abschneiden = Runden“:** Als Eigenschaft der üblichen balanciert-ternären gebrochenen Darstellung gilt eine symmetrische Rundungseigenschaft mit Fehler unter ½ ULP; die Aussage sollte nicht ohne Präzisions-/Formatkontext auf jede Art von „Abschneiden“ verallgemeinert werden.[7]
8. **Primzahlsieb-Komplexität:** Übliche asymptotische Angaben beruhen auf abstrakten Kostenmodellen für Speicher und arithmetische Operationen. Sie sagen nicht direkt voraus, wie viele reale Murmelbewegungen oder Weichenbetätigungen unser Rechner benötigt.[9]

## 10. Verbleibende Entscheidungen – bewusst auf das Nötige reduziert

Diese Punkte können nicht seriös durch Recherche allein „wegentschieden“ werden:

1. **Konkrete universelle Queue-Maschine:** Übergangstabelle, Programmdarstellung auf dem Band und Simulator müssen noch konkret festgelegt und verifiziert werden. Das ist die wichtigste verbleibende theoretische Aufgabe.
2. **Symbolgrößen-/Paarerkennung und Schreibvorgang:** Prototyp vergleicht Ein-Murmel-Ternär, Zwei-Murmel-Ternär und Ein-Murmel-Binär mit derselben Lese-/Schreibaufgabe; Entscheidung nach Fehlerrate, Wieder-Ausrichtung, Bauteilen und Kugelzahl.
3. **Physische Queue-Schleife:** Ein-/Ausgang, logischer Queue-Anfang/-Rand, Einfügen am Ende, Reihenfolge, Kapazität und Stauverhalten müssen als Mechanismus demonstriert werden.
4. **Siebprogramm im Simulator:** Erst nach Punkt 1 kann ein vollständiges Programm geschrieben und seine tatsächliche Token-/Schrittzahl gemessen werden.
5. **Abmessungen und Laufzeit:** Tisch-/Regalgröße und genaue Druckparameter werden erst aus den Prototypen und der gemessenen Queue-Kapazität abgeleitet. Sie sind keine konzeptionellen Blocker.

Nicht mehr offen für V1: kein beweglicher Kopf, kein elektronisches Rechnen, keine echte Parallelität, keine Festlegung auf Zweikugel-Trits als Baseline, keine Selbstmodifikation als Voraussetzung, keine festen FDM-Toleranzen als Annahme.

## 11. Ergebnis

Der sinnvollste nächste Stand ist ein **gravitationsbetriebener, motorisch rückgeführter, serieller Queue-Rechner** mit feststehender Lese-/Bearbeitungsstelle, austauschbarem Programm als Bandinhalt und vorläufig balanciert-ternärer Ein-Murmel-pro-Trit-Codierung. Er soll im Grenzmodell universell sein, wird aber in der ersten physischen Ausführung durch Murmelvorrat und Bahnlänge begrenzt.

Der entscheidende nächste Schritt ist nicht CPU-/ALU-Mimik oder eine große Murmelbahn, sondern die **konkrete formale Queue-Interpreter-Spezifikation samt Simulator**. Danach ist mit einem kleinen Prototyp zu testen, ob Größe erkennen, Symbol austauschen und hinten wieder einfügen zuverlässig funktionieren. Erst wenn beides klappt, lohnt es sich, das vollständige Primzahlprogramm mechanisch auszuführen.

## Sources

[1] https://plato.stanford.edu/entries/turing-machine
[2] https://jeffe.cs.illinois.edu/teaching/algorithms/models/08-universal.pdf
[3] https://link.springer.com/chapter/10.1007/978-3-319-73216-9_19
[4] https://ar5iv.labs.arxiv.org/html/1310.6398
[5] https://drops.dagstuhl.de/storage/00lipics/lipics-vol228-fscd2022/LIPIcs.FSCD.2022.16/LIPIcs.FSCD.2022.16.pdf
[6] https://content.wolfram.com/sites/13/2023/02/15-1-1.pdf
[7] https://web.ece.ucsb.edu/~parhami/pubs_folder/parh15-ietcdt-trunc-ternary-mult.pdf
[8] https://ptolemy.berkeley.edu/publications/papers/95/processNets/proceedings.pdf
[9] https://www.ams.org/journals/mcom/2020-89-321/S0025-5718-2019-03438-5/mcom3438_AM.pdf
[10] https://ir.cwi.nl/pub/36315/36315.pdf — The Queue Automaton Revisited (Baeten & Luttik)
