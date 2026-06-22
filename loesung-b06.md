# Lösung zu Blatt 06

## Überblick

In Blatt 06: Visitor vs. Pattern Matching, Records bearbeite ich das Projekt Filter-DSL. Dabei geht es um JUnit-Tests, das Visitor-Pattern, Pattern Matching, Records, AST-Normalisierung, Approval Testing und Property-based Testing.

## Bearbeitete Aufgaben

* Aufgabe 1: JUnit-Testfälle
* Aufgabe 2: AST-Builder mit dem Visitor-Pattern
* Aufgabe 3: AST-Builder mit Pattern Matching
* Aufgabe 4: Vergleich Visitor-Pattern und Pattern Matching
* Aufgabe 5: AST-Normalisierung
* Aufgabe 6: Approval Testing
* Aufgabe 7: Property-based Testing

## Repository

Filter-DSL:

`https://github.com/Exoriy/prog2-b06-filterdsl`



---

## Aufgabe 1: JUnit-Testfälle

In dieser Aufgabe habe ich JUnit-Tests für die beiden AST-Builder geschrieben.

Die Tests prüfen verschiedene Filter-Abfragen. Dazu gehören Vergleiche mit Strings und Zahlen, der Operator `in`, der Operator `not` und Verbindungen mit `and` und `or`.

In jedem Test habe ich zuerst den erwarteten AST erstellt. Danach wird dieselbe Query mit dem `AstBuilderVisitor` und mit dem `AstBuilderPattern` verarbeitet. Anschließend werden beide Ergebnisse mit dem erwarteten AST verglichen.

Am Anfang waren die Tests noch nicht erfolgreich, weil die beiden Builder noch nicht implementiert waren. Nach Aufgabe 2 und Aufgabe 3 konnten die Tests erfolgreich ausgeführt werden.



---

## Aufgabe 2: AstBuilderVisitor

In dieser Aufgabe habe ich den `AstBuilderVisitor` implementiert.

Der Visitor durchläuft den Parse-Tree, der von ANTLR erzeugt wird. Für die Zwischenergebnisse werden verschiedene Stacks benutzt. Ein Stack speichert Expressions, ein weiterer einzelne Values und ein dritter Listen von Values.

Die Visit-Methoden erstellen die passenden AST-Knoten für Vergleiche, Listen sowie die Operatoren `not`, `and` und `or`.

Am schwierigsten war für mich das Arbeiten mit `push` und `pop`. Man muss immer darauf achten, welcher Wert gerade oben auf einem Stack liegt. Nach einigen Tests konnte ich aber besser verstehen, wie der Visitor die Ergebnisse Schritt für Schritt zusammensetzt.




---

## Aufgabe 3: AstBuilderPattern

In dieser Aufgabe habe ich den zweiten AST-Builder implementiert.

Der `AstBuilderPattern` benutzt keine Stacks. Die Methoden geben ihre Ergebnisse direkt zurück und rufen sich gegenseitig auf. Dadurch werden die AST-Knoten für `Comparison`, `InList`, `Not`, `And` und `Or` erstellt.

Für Strings und Zahlen habe ich einen `switch` mit Pattern Matching benutzt. Strings werden zu `Value.Str` und Zahlen zu `Value.Num`.

Dieser Builder war für mich einfacher zu verstehen, weil die Ergebnisse direkt zurückgegeben werden. Dadurch konnte ich den Ablauf leichter nachvollziehen.




---

## Aufgabe 4: Vergleich der beiden AST-Builder

Beide Builder erzeugen am Ende denselben AST, arbeiten aber unterschiedlich.

Der `AstBuilderVisitor` passt gut zu ANTLR, weil für viele Regeln der Grammatik eigene Visit-Methoden vorhanden sind. Für mich war er aber etwas schwieriger, weil mehrere Stacks benutzt werden.

Der `AstBuilderPattern` war übersichtlicher. Die Methoden geben ihre Ergebnisse direkt zurück und benötigen keine Stacks. Dafür muss man die Traversierung durch den Parse-Tree selbst schreiben.

Für diese Aufgabe fand ich den `AstBuilderPattern` einfacher. Mit den JUnit-Tests habe ich geprüft, dass beide Builder bei denselben Queries auch denselben AST erzeugen.




---

## Aufgabe 5: AST-Normalisierung

In dieser Aufgabe habe ich die Methode `AstBuilders.simplify` ergänzt.

Die Methode vereinfacht einen AST. Dabei wird zum Beispiel ein doppeltes `not` entfernt.

Aus:

`not not artist == "Beatles"`

wird:

`artist == "Beatles"`

Dafür habe ich einen `switch` mit Pattern Matching benutzt. Die verschiedenen AST-Knoten werden erkannt und ihre inneren Ausdrücke werden rekursiv vereinfacht.

Ich habe zusätzlich zwei JUnit-Tests geschrieben. Ein Test prüft einen doppelten `not` am Anfang des AST. Der zweite Test prüft einen doppelten `not` innerhalb eines `And`-Ausdrucks.




---

## Aufgabe 6: Approval Testing

In dieser Aufgabe habe ich Approval Tests für die beiden AST-Builder geschrieben.

Dafür werden verschiedene Queries zuerst in einen AST umgewandelt. Danach wird der AST mit dem `AstPrinter` als Text ausgegeben.

Beim ersten Testlauf wurden `.received.txt`-Dateien erstellt. Ich habe die Ergebnisse geprüft und danach als `.approved.txt` gespeichert.

Bei späteren Testläufen wird der neue Text mit dem gespeicherten Ergebnis verglichen. Wenn sich die Ausgabe unerwartet verändert, schlägt der Test fehl.

Ich habe einfache und komplexere Queries getestet. Außerdem wird geprüft, ob der `AstBuilderVisitor` und der `AstBuilderPattern` dieselbe Ausgabe erzeugen.




---

## Aufgabe 7: Property-based Testing

In dieser Aufgabe habe ich Property-based Tests mit jqwik geschrieben.

Anders als bei normalen JUnit-Tests werden die Queries nicht alle von Hand geschrieben. jqwik erzeugt automatisch viele verschiedene Werte und Filter-Abfragen.

Ich habe unter anderem diese Eigenschaften geprüft:

* Ein AST bleibt nach dem Ausgeben und erneuten Einlesen gleich.
* Der Roundtrip funktioniert mit dem `AstBuilderVisitor`.
* Der Roundtrip funktioniert mit dem `AstBuilderPattern`.
* Ein AST vom Visitor kann vom Pattern-Builder wieder eingelesen werden.
* `A and A` hat dasselbe Ergebnis wie `A`.
* `A and B` hat dasselbe Ergebnis wie `B and A`.

Dadurch konnten beide AST-Builder mit vielen automatisch erzeugten Queries getestet werden.


