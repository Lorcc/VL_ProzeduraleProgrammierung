<!--

author:   Sebastian Zug & André Dietrich & Galina Rudolf
email:    sebastian.zug@informatik.tu-freiberg.de & andre.dietrich@ovgu.de & Galina.Rudolf@informatik.tu-freiberg.de
version:  1.0.2
language: de
narrator: Deutsch Female

comment: Einführung in die Programmierung für Nicht-Informatiker
logo: ./img/LogoCodeExample.png

import: https://github.com/liascript/CodeRunner
        https://github.com/LiaTemplates/AVR8js/main/README.md#10
        https://raw.githubusercontent.com/liascript-templates/plantUML/master/README.md

-->

# Kontrollstrukturen

**Inhalt der heutigen Veranstaltung**

* Fortsetzung der Besprechung der Operatoren
* Einführung der Kontrollflusskonstrukte

**Fragen an die heutige Veranstaltung ...**

* Welche Fallstricke lauern bei expliziter und impliziter Typumwandlung?
* Wann sollte man eine explizite Umwandlung vornehmen?
* Wie lassen sich Kontrollflüsse grafisch darstellen?
* Welche Konfigurationen erlaubt die `for`-Schleife?
* In welchen Funktionen (Verzweigungen, Schleifen) ist Ihnen das Schlüsselwort
  `break` bekannt?
* Worin liegt der zentrale Unterschied der `while` und `do-while` Schleife?
* Recherchieren Sie Beispiele, in denen `goto`-Anweisungen Bugs generierten.

## Cast-Operatoren

> *Casting* beschreibt die Konvertierung eines Datentypen in einen anderen. Dies
> kann entweder automatisch durch den Compiler vorgenommen oder durch den
> Programmierer angefordert werden.

Im erst genannten Fall spricht man von

* impliziten Typumwandlung, im zweiten von
* expliziter Typumwandlung.

Es wird bei Methoden vorausgesetzt, dass der Compiler eine Typumwandlung auch
wirklich unterstützt. Eine Typumwandlung kann einschränkend oder erweiternd
sein!

### Implizite Datentypumwandlung

Operanden dürfen in C eine Variable mit unterschiedlichen Datentyp verknüpfen.
Die implizite Typumwandlung generiert einen gemeinsamen Datentyp, der in einer
Rangfolge am weitesten oben steht. Das Ergebnis ist ebenfalls von diesem Typ.

1. `char` -> `short` -> `int` -> `long` -> `long long` / `float` -> `double` -> `long double`
2. Die Rangfolge bei ganzzahligen Typen ist unabhängig vom Vorzeichen.
3. Standarddatentypen haben einen höheren Rang als erweiterte Ganzzahl-Typen aus
   `<stdint.h>` wie `int_least32_t`, obwohl beide die gleiche Breite besitzen.

Dabei sind einschränkende Konvertierungskonfigurationen kritisch zu sehen:

* Bei der Umwandlung von höherwertigen Datentypen in niederwertigere Datentypen
  kann es zu Informationsverlust kommen.
* Der Verleich von `signed`- und `unsigned`-Typen kann zum falschen Ergebnis
  führen. So kann beispielsweise `-1 > 1U` wahr sein.

{{1}}
**Konvertierung zu `_Bool`**

{{1}}
> *6.3.1.2  When any scalar value is converted to _Bool, the result is 0 if the*
> *value compares equal to 0; otherwise, the result is 1*
>
> C99 Standard

{{1}}
**Konvertierung zum `int`-Typ**

{{1}}
Beim Konvertieren des Wertes von einem in das andere `int`-Typ

{{1}}
* bleibt der Wert unverändert, wenn die Darstellung im Zieltyp möglich ist,
* anderenfalls ist das Ergebnis implementierungabhängig bzw. führt zu einer
  Fehlermeldung

{{2}}
**Vermischen von Ganzzahl und Gleitkommawerten**

{{2}}
* Die Division zweier `int`-Werte gibt immer nur einen Ganzzahlanteil zurück.
  Hier findet keine automatische Konvertierung in eine Gleitpunktzahl statt.
* Bei der Umwandlung von ganz großen Zahlen (beispielsweise `long long`) in
  einen Gleitpunkttyp kann es passieren, dass die Zahl nicht mehr darzustellbar ist.

{{2}}
> *6.3.1.4 Real floating and integer - When a finite value of real floating type*
> *is converted to an integer type other than `_Bool`, the fractional part is*
> *discarded (i.e., the value is truncated toward zero). If the value of the*
> *integral part cannot be represented by the integer type, the behavior is*
> *undefined.*
>
> C99 Standard

{{3}}
> **Achtung:** Implizite Typumwandlungen bergen erhebliche Risiken in sich!

{{3}}
Die Headerdatei `<fenv.h>` definiert verschiedene Einstellungen für das Rechnen
mit Gleitpunktzahlen. Unter anderem können Sie das Rundungsverhalten von
Gleitpunkt-Arithmetiken über entsprechende Makros anpassen.



### Explizite Datentypumwandlung

Anders als bei der impliziten Typumwandlung wird bei der expliziten
Typumwandlung der Zieldatentyp konkret im Code angegeben.

Im Beispiel wird in der ersten `printf`-Anweisung das Ergebnis der ganzzahlegen
Division `i/j` in `float` konvertiert, in der zweiten `printf`-Anweisung die
Variable `i`.

## Kontrollfluss

Bisher haben wir Programme entworfen, die eine sequenzielle Abfolge von
Anweisungen enthielt.

![Modelle](https://www.plantuml.com/plantuml/png/SoWkIImgAStDuG8pkAmSIulJ3k_9A2hDIozEoibFpbOmjCOcQeHLr1Yhg8arqFJy0Yw7rBmKe5K0)<!-- width="40%" --> [Lineare Ausführungskette]

Diese Einschränkung wollen wir nun mit Hilfe weiterer Anweisungen überwinden:

1. **Verzweigungen (Selektion)**: In Abhängigkeit von einer Bedingung wird der
   Programmfluss an unterschiedlichen Stellen fortgesetzt.

   Beispiel: Wenn bei einer Flächenberechnung ein Ergebnis kleiner Null
   generiert wird, erfolgt eine Fehlerausgabe. Sonst wird im Programm
   fortgefahren.

2. **Schleifen (Iteration)**: Ein Anweisungsblock wird so oft wiederholt, bis
   eine Abbruchbedingung erfüllt wird.

   Beispiel: Ein Datensatz wird durchlaufen um die Gesamtsumme einer Spalte zu
   bestimmen. Wenn der letzte Eintrag erreicht ist, wird der Durchlauf
   abgebrochen und das Ergebnis ausgegeben.

3. Des Weiteren verfügt C über **Sprünge**: die Programmausführung wird mit Hilfe von Sprungmarken an einer anderen Position fortgesetzt. Formal sind sie jedoch nicht notwendig. Statt die nächste Anweisung auszuführen, wird (zunächst) an eine ganz andere Stelle im Code gesprungen.

### Verzweigungen

Verzweigungen entfalten mehrere mögliche Pfade für die Ausführung des Programms.

Darstellungsbeispiele für mehrstufige Verzweigungen (`switch`)

![Modelle](https://www.plantuml.com/plantuml/png/SoWkIImgAStDuG8pk3BJ53IIy_DICaioy_CK73KLIZ9IynGqAbEBDRaK5An2KYjA50ojkL9pYbDHbJfXLMfa3MGMb-GNOD7XZ5M7CJR3NR0gDO4eLT38oo_9oCnBHyY0X86IGErfN63R7O1ia2S1)<!-- width="60%" --> [Flussdiagramm]

![instruction-set](./images/03_Kontrollfluss/NassiMehrfAusw1.png)<!-- width="80%" --> [Nassi-Shneiderman-Diagramm][^1]

[^1]: [Nassi-Shneiderman-Diagramm (Autor Renzsorf)](https://de.wikipedia.org/wiki/Nassi-Shneiderman-Diagramm)

#### `if`-Anweisungen

Im einfachsten Fall enthält die `if`-Anweisung eine einzelne bedingte
Anweisung oder einen Anweisungsblock. Sie kann mit `else` um eine Alternative
erweitert werden.

Zum Anweisungsblock werden die Anweisungen mit geschweiften Klammern (`{` und
`}`) zusammengefasst.

Optional kann eine alternative Anweisung angegeben werden, wenn die Bedingung
nicht erfüllt wird:

Mehrere Fälle können verschachtelt abgefragt werden:

> **Merke**: An diesem Beispiel wird deutlich, dass die Klammern für die
> Zuordnung elementar wichtig sind. Die letzte Anweisung gehört NICHT zum zweite
> `else` Zweig sondern zum ersten!

************************************************************************
**Weitere Beispiele für Bedingungen**

Die Bedingungen können als logische UND arithmetische Ausdrücke
formuliert werden.

| Ausdruck     | Bedeutung                                       |
| ------------ | ----------------------------------------------- |
| `if (a)`     | `if (a != 0)`                                   |
| `if (!a)`    | `if (a == 0)`                                   |
| `if (a > b)` | `if (!(a <= b))`                                |
| `if ((a-b))` | `ìf (a != b)`                                   |
| `if (a & b)` | $a>0$, $b>0$, mindestens ein $i$ mit $a_i==b_i$ |

************************************************************************

**Mögliche Fehlerquellen**

1. Zuweisungs- statt Vergleichsoperator in der Bedingung (kein Compilerfehler)
2. Bedingung ohne Klammern (Compilerfehler)
3. `;` hinter der Bedingung (kein Compilerfehler)
4. Multiple Anweisungen ohne Anweisungsblock
5. Komplexität der Statements

************************************************************************

#### `switch`-Anweisungen

> [*Too many ifs - I think I switch* ](http://www.peacesoftware.de/ckurs7.html)
>
> Berndt Wischnewski

Eine übersichtlichere Art der Verzweigung für viele, sich ausschließende
Bedingungen wird durch die `switch`-Anweisung bereitgestellt. Sie wird in der
Regel verwendet, wenn eine oder einige unter vielen Bedingungen ausgewählt
werden sollen. Das Ergebnis der "expression"-Auswertung soll eine Ganzzahl
(oder `char`-Wert) sein. Stimmt es mit einem "const_expr"-Wert
überein, wird die Ausführung an dem entsprechenden `case`-Zweig fortgesetzt.
Trifft keine der Bedingungen zu, wird der `default`-Fall aktiviert.

Im Unterschied zu einer `if`-Abfrage wird in den unterschiedlichen Fällen immer
nur auf Gleichheit geprüft! Eine abgefragte Konstante darf zudem nur einmal
abgefragt werden und muss ganzzahlig oder `char` sein.

{{3}}
Und wozu brauche ich das `break`? Ohne das `break` am Ende eines Falls werden
alle darauf folgenden Fälle bis zum Ende des `switch` oder dem nächsten `break`
zwingend ausgeführt.

{{3}}
Unter Ausnutzung von `break` können Kategorien definiert werden, die aufeinander
aufbauen und dann übergreifend "aktiviert" werden.

### Schleifen

Schleifen dienen der Wiederholung von Anweisungsblöcken – dem sogenannten
Schleifenrumpf oder Schleifenkörper – solange die Schleifenbedingung als
Laufbedingung gültig bleibt bzw. als Abbruchbedingung nicht eintritt. Schleifen,
deren Schleifenbedingung immer zur Fortsetzung führt oder die keine
Schleifenbedingung haben, sind *Endlosschleifen*.

Schleifen können verschachtelt werden, d.h. innerhalb eines Schleifenkörpers
können weitere Schleifen erzeugt und ausgeführt werden. Zur Beschleunigung des
Programmablaufs werden Schleifen oft durch den Compiler entrollt (*Enrollment*).

Grafisch lassen sich die wichtigsten Formen in mit der Nassi-Shneiderman
Diagrammen wie folgt darstellen:

* Iterationssymbol

<!--
style="width: 80%; min-width: 420px; max-width: 720px;"
-->
```ascii
   +---------------------------------------------------------------------+
   |                                                                     |
   |  zähle [Variable] von [Startwert] bis [Endwert], mit [Schrittweite] |
   | +-------------------------------------------------------------------+
   | |                                                                   |
   | |  Anweisungsblock 1                                                |
   +-+-------------------------------------------------------------------+     .
```

* Wiederholungsstruktur mit vorausgehender Bedingungsprüfung

<!--
style="width: 80%; min-width: 420px; max-width: 720px;"
-->
```ascii
   +--------------------------+
   |                          |
   |  solange Bedingung wahr  |
   | +------------------------+
   | |                        |
   | |  Anweisungsblock 1     |
   +-+------------------------+                                                .
```

* Wiederholungsstruktur mit nachfolgender Bedingungsprüfung

<!--
style="width: 80%; min-width: 420px; max-width: 720px;"
-->
```ascii
   +-+------------------------+
   | |                        |
   | |  Anweisungsblock 1     |
   | +------------------------+
   |                          |
   |   solange Bedingung wahr |
   +--------------------------+                                                .
```

Die Programmiersprache C kennt diese drei Formen über die Schleifenkonstrukte
`for`, `while` und `do while`.

#### `for`-Schleife

Der Parametersatz der `for`-Schleife besteht aus zwei Anweisungsblöcken und einer
Bedingung, die durch Semikolons getrennt werden. Mit diesen wird ein
**Schleifenzähler** initiert, dessen
Manipulation spezifiziert und das Abbruchkriterium festgelegt. Häufig wird die
Variable mit jedem Durchgang inkrementiert oder dekrementiert, um dann anhand
eines Ausdrucks evaluiert zu werden. Es wird überprüft, ob die Schleife
fortgesetzt oder abgebrochen werden soll. Letzterer Fall tritt ein, wenn dieser
den Wert 0 annimmt – also der Ausdruck false (falsch) ist.

**Beliebte Fehlerquellen**

* Semikolon hinter der schließenden Klammer von `for`
* Kommas anstatt Semikolons zwischen den Parametern von `for`
* fehlerhafte Konfiguration von Zählschleifen
* Nichtberücksichtigung der Tatsache, dass die Zählvariable nach dem Ende der
  Schleife über dem Abbruchkriterium liegt

#### `while`-Schleife

Während bei der `for`-Schleife auf ein n-maliges Durchlaufen Anweisungsfolge
konfiguriert wird, definiert die `while`-Schleife nur eine Bedingung für den
Fortführung/Abbruch.

Dabei soll erwähnt werden, dass eine `while`-Schleife eine `for`-Schleife
ersetzen kann.


#### `do-while`-Schleife

Im Gegensatz zur `while`-Schleife führt die `do-while`-Schleife die Überprüfung
des Abbruchkriteriums erst am Schleifenende aus.

Welche Konsequenz hat das? Die `do-while`-Schleife wird in jedem Fall einmal
ausgeführt.

### Kontrolliertes Verlassen der Anweisungen

Bei allen drei Arten der Schleifen kann zum vorzeitigen Verlassen der Schleife
 `break` benutzt werden. Damit wird aber nur die unmittelbar umgebende Schleife
 beendet!

Eine weitere wichtige Eingriffsmöglichkeit für Schleifenkonstrukte bietet
`continue`. Damit wird nicht die Schleife insgesamt, sondern nur der aktuelle
Durchgang gestoppt.

Durch `return`- Anweisung wird das Verlassen einer Funktion veranlasst (genaues
in der Vorlesung zu Funktionen).

### GoTo or not GoTo?

`Goto` erlaubt es Sprungmarken (Labels) zu definieren und bei der Anweisung, die
diese referenziert, die Ausführung fortzusetzen. Konsequenterweise ist damit aber
eine nahezu beliebige Auflösung der Ordnung des Codes möglich.

>  *Use of `goto` statement is highly discouraged in any programming language*
>  *because it makes difficult to trace the control flow of a program, making*
>  *the program hard to understand and hard to modify. Any program that uses a*
> * `goto` can be rewritten to avoid them.*
>
> [Tutorialspoint](https://www.tutorialspoint.com/cprogramming/c_goto_statement.html)

Ein wichtiger Fehler, der häufig immer mit goto in Verbindung gebracht wird, hat
aber eigentlich nichts damit zu tun
[Apple-SSL Bug](http://www.spiegel.de/netzwelt/web/goto-fail-apples-furchtbarer-fehler-a-955154.html)


## Darstellung von Algorithmen

* Nassi-Shneiderman-Diagramme
* [Flußdiagramme](https://de.wikipedia.org/wiki/Programmablaufplan)

Beispiel - Wechseln der Räder

![Programmablaufplan.png](https://www.plantuml.com/plantuml/png/LL2_JeSm4Dxx5ETlWe74mOc6oAIBamukBYTSxKKev7gD2G_cvCQBMQk6S5Fx_VdwzVgeA9hcoPI30MWPEhYn1lAmW-gPWv88iQC0EB-4E_IoKNgxhK5znggmr0RA2As4-dV9KK-35qolMJJjdv62FQX7744nnS6VuCE1OMCwazmqzlGIV7YU22hkkkjSXsCfywkXCB8pnGSFoUaeQNY7LVOlzn_QNkwJ4lnyIAykraHTLjDdOzx7Dm00)<!--
style="width: 50%;
       max-width: 330px;
       display: block;
       margin-left: auto;
       margin-right: auto;" -->

