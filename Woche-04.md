# Woche 04

In dieser Woche kommen wir - endlich - zur Vererbung! Als erstes Beispiel schauen wir uns Exceptions im Detail an.

## Exceptions und Vererbung

Das Konzept haben Sie bereits implizit beim Umgang mit Exceptions verwendet. Nehmen wir beispielsweise diesen Code, um eine Datei zu öffnen und den Inhalt auszugeben:

```java
import java.io.BufferedReader;
import java.io.File;
import java.io.FileReader;

public class FileDumper {
    public static void dumpMultilineFile(String filename) {
        try {
            BufferedReader reader = new BufferedReader(new FileReader(new File(filename)));
            while(reader.ready()) {
                System.out.println(reader.readLine());
            }
        } catch(Exception e) {
            System.out.println("Fehler beim Ausgeben von " + filename + ": " + e.getMessage());
        }
    }
}
```

In diesem Code können zwei Exceptions auftreten: Bei der Erstellung von ```BufferedReader``` eine ```FileNotFoundException``` (falls die Datei nicht vorhanden ist), und bei ```reader.readLine()``` eine ```IOException``` (bei diversen systembedingten Zugriffsfehlern). Beide diese Exception-Klassen leiten von der Exception-Oberklasse ```Exception``` auf, wodurch ein allgemeines Abfangen über ```catch(Exception e)``` möglich ist (tatsächlich leitet auch FileNotFoundException von IOException ab, entsprechend würde auch ```catch(IOException e)``` funktionieren).

Das ist nützlich, da man so auf unterschiedliche Typen von Exceptions, die in der Regel unterschiedliche Fehlerquellen beschreiben, unterschiedlich reagieren kann - oder auch (wie in diesem Beispiel) pauschal auf alle Exceptions gleich reagieren kann.

Beispielsweise könnte die Methode ```dumpMultilineFile``` explizit fordern, dass die übergebene Datei mehrere Zeilen hat - falls die Datei nur eine Zeile hat, könnte das ein Fehler sein, zu dessen spezieller Behandlung der aufrufende Code gezwungen werden soll. Dann könnte eine neue Exception definiert werden:

```java
public class LineNumberException extends Exception {
    public LineNumberException(String message) {
        super(message);
    }
}
```

Diese Exception kann dann in ```FileDumper``` verwendet werden: 

```java
import java.io.BufferedReader;
import java.io.File;
import java.io.FileReader;

public class FileDumper {
    public static void dumpMultilineFile(String filename) throws LineNumberException {
        try {
            BufferedReader reader = new BufferedReader(new FileReader(new File(filename)));
            int linesRead = 0;
            while(reader.ready()) {
                System.out.println(reader.readLine());
                linesRead ++;
            }
            // Wenn weniger als 2 Zeilen eingelesen wurden
            // Exception werfen
            if(linesRead <= 1) {
                throw new LineNumberException("Zu wenige Zeilen in " + filename);
            }
        } catch(IOException e) {
            System.out.println("Fehler beim Ausgeben von " + filename + ": " + e.getMessage());
        }
    }
}
```

In diesem Beispiel wird, wenn zu wenige Zeilen eingelesen wurden, eine neue ```LineNumberException``` mit der entsprechenden Fehlermeldung geworfen. Da in dem umgebenden try-catch-Block spezifisch ```IOException```s gefangen werden, aber ```LineNumberException``` direkt von ```Exception``` ableitet (also keine ```IOException``` ist), wird die ```LineNumberException``` von dem ```catch``` nicht erfasst sondern fliegt direkt weiter an den Code, der dumpMultilineFile aufgerufen hat (das wird durch das ```throws LineNumberException``` in der Funktionsdefinition beschrieben). Dort muss sie dann von einem entsprechenden try-catch-Block gefangen (entweder allgemein als ```Exception``` oder spezifisch als ```LineNumberException```) und behandelt werden.

Die Verwendung der Vererbung in Exceptions ist also integraler Bestandteil der Programmierung in Java: Viele Standard-Methoden werfen bei unterschiedlichen Fehlern Exceptions, da das bei der Fehlerbehandlung viel mehr Flexibilität erlaubt, als das Zurückgeben illegaler Werte wie ```null``` um einen Fehler zu signalisieren. Zudem können durch das Ableiten von ```Exception``` eigene Fehlertypen definiert und spezifisch behandelt werden.

Weitere Verwendungen der Vererbung sehen wir uns in der nächsten Woche an.
