# Murmelrechner – Konzeption

Dieses Repository enthält den quellenbelegten Konzeptionszwischenstand für einen modularen, 3D-gedruckten Murmelrechner.

## Zielbild

Ein mechanischer Rechner, in dem Schwerkraft den Murmelstrom antreibt und ein Motor nur die Murmeln zurückhebt. Das austauschbare Programm ist als Murmelband codiert und läuft unter einem feststehenden mechanischen Kopf hindurch. Eine Kamera-/Tablet-App ist ausschließlich für Anzeige und Verlauf vorgesehen, nicht für die Berechnung oder Steuerung.

## Aktueller Architekturstand

Zwei Rechenmodelle stehen ernsthaft zur Auswahl: eine vorwärts arbeitende **Queue-Maschine** und ein **Kahn-Prozessnetz (KPN)** mit mehreren Prozessen und FIFO-Kanälen. Die Queue-Maschine ist die einfachere Arbeitsbaseline für „Programm auf dem Band“; KPN wird vor allem durch die Idee attraktiv, volle Puffer durch zusätzliche Bahnsegmente dynamisch zu erweitern. Als Codierungsbaseline dient vorläufig eine Murmel pro balanciertem Trit; Zwei-Murmel-Ternär und Ein-Murmel-Binär bleiben Vergleichsvarianten.

## Status und nächste Schritte

Dies ist ein Konzept, kein getesteter Bauplan. Es gibt noch keinen konkreten Interpreter, Simulator oder ausführbaren Primzahlprogramm-Code. Der nächste Schritt ist die Modellauswahl anhand des Vergleichs, dann die konkrete Interpreter-/Prozessgraph-Spezifikation samt Simulator und danach vergleichbare Prototypen für Lesen, Schreiben, Rückführung, erweiterbare Puffer und die verschiedenen Murmelcodierungen.

## Dokumente

- [Murmelrechner-Konzeptionszwischenstand](docs/Murmelrechner_Konzeptionszwischenstand.md)
- [Rechenmodelle im Vergleich: Queue-Maschine und KPN](docs/Modellvergleich_Queue_und_KPN.md)
