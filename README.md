# Murmelrechner – Konzeption

Dieses Repository enthält den quellenbelegten Konzeptionszwischenstand für einen modularen, 3D-gedruckten Murmelrechner.

## Zielbild

Ein mechanischer Rechner, in dem Schwerkraft den Murmelstrom antreibt und ein Motor nur die Murmeln zurückhebt. Das austauschbare Programm ist als Murmelband codiert und läuft unter einem feststehenden mechanischen Kopf hindurch. Eine Kamera-/Tablet-App ist ausschließlich für Anzeige und Verlauf vorgesehen, nicht für die Berechnung oder Steuerung.

## Aktueller Architekturstand

Als passendstes theoretisches Modell wird eine vorwärts arbeitende Queue-Maschine untersucht. Als Codierungsbaseline dient vorläufig eine Murmel pro balanciertem Trit; Zwei-Murmel-Ternär und Ein-Murmel-Binär bleiben mechanische Vergleichsvarianten. Das Dokument trennt gesicherte Theorie, Designentscheidungen und noch zu prototypisierende Mechanik.

## Status und nächste Schritte

Dies ist ein Konzept, kein getesteter Bauplan. Es gibt noch keinen konkreten Queue-Interpreter, Simulator oder ausführbaren Primzahlprogramm-Code. Der nächste Schritt ist die formale Interpreter-Spezifikation samt Simulator; danach folgen vergleichbare Prototypen für Lesen, Schreiben, Rückführung und die verschiedenen Murmelcodierungen.

## Dokument

- [Murmelrechner-Konzeptionszwischenstand](docs/Murmelrechner_Konzeptionszwischenstand.md)
