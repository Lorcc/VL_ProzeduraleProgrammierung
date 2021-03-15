<!--

author:   Sebastian Zug & André Dietrich & Galina Rudolf
email:    sebastian.zug@informatik.tu-freiberg.de & andre.dietrich@ovgu.de & Galina.Rudolf@informatik.tu-freiberg.de
version:  1.0.2
language: de
narrator: Deutsch Female

comment: Einführung in die Programmierung für Nicht-Informatiker
logo: ./img/LogoCodeExample.png

import: https://github.com/liascript/CodeRunner
        https://raw.githubusercontent.com/liascript-templates/plantUML/master/README.md

-->

# Funktionen

Die interaktive Version des Kurses ist unter diesem [Link](https://liascript.github.io/course/?https://raw.githubusercontent.com/SebastianZug/VL_ProzeduraleProgrammierung/master/05_Funktionen.md#1) zu finden.

--------------------------------------------------------------------------------

>  Zur Erinnerung ... an die Schritte zur Realisierung einer Variablen

> * Deklaration ist nur die Vergabe eines Namens und eines Typs für die Variable.

> * Definition ist die Reservierung des Speicherplatzes.

> * Initialisierung ist die Zuweisung eines ersten Wertes.

--------------------------------------------------------------------------------

**Inhalt der heutigen Veranstaltung**

* Strukturierung von Code anhand von Funktionen und Prozeduren
* Konfiguration von Parametern und Rückgabewerten
* Verwendung von Funktionen beim Entwurf von Software

--------------------------------------------------------------------------------

**Fragen an die heutige Veranstaltung ...**

* Nennen Sie Vorteile prozeduraler Programmierung!
* Welche Komponenten beschreiben Definition einer Funktion?
* Welche unterschiedlichen Bedeutungen kann das Schlüsselwort `static`
  ausfüllen?
* Beschreiben Sie Gefahren bei der impliziten Typkonvertierung.
* Erläutern Sie die Begriffe Sichtbarkeit und Lebensdauer von Variablen.
* Welche kritischen Punkte sind bei der Verwendung globaler Variablen zu
  beachten.
* Warum ist es sinnvoll Funktionen in Look-Up-Tables abzubilden, letztendlich
  kostet das Ganze doch Speicherplatz?


## Motivation
{{1}}
**Prozedurale Programmierung Ideen und Konzepte**

{{1}}
*Bessere Lesbarkeit*

{{1}}
Der Quellcode eines Programms kann schnell mehrere tausend Zeilen umfassen. Beim
Linux Kernel sind es sogar über 15 Millionen Zeilen und Windows, das ebenfalls
zum Großteil in C geschrieben wurde, umfasst schätzungsweise auch mehrere
Millionen Zeilen. Um dennoch die Lesbarkeit des Programms zu gewährleisten, ist
die Modularisierung unerlässlich.

{{1}}
*Wiederverwendbarkeit*

{{1}}
In fast jedem Programm tauchen die gleichen Problemstellungen mehrmals auf. Oft
gilt dies auch für unterschiedliche Applikationen. Da nur Parameter und
Rückgabetyp für die Benutzung einer Funktion bekannt sein müssen, erleichtert
dies die Wiederverwendbarkeit. Um die Implementierungsdetails muss sich der
Entwickler dann nicht mehr kümmern.

{{1}}
*Wartbarkeit*

{{1}}
Fehler lassen sich durch die Modularisierung leichter finden und beheben.
Darüber hinaus ist es leichter, weitere Funktionalitäten hinzuzufügen oder zu
ändern.

{{1}}
In allen 3 Aspekten ist der Vorteil in der Kapselung der Funktionalität zu
suchen.

{{2}}
**Wie würden wir das vorhergehende Beispiel umstellen?**

{{2}}
Funktionen sind Unterprogramme, die ein Ausgangsproblem in kleine,
möglicherweise wiederverwendbare Codeelemente zerlegen.
{{3}}
**Wie findet sich diese Idee in großen Projekten wieder?**

{{3}}
> **Write Short Functions**


### Funktionsdefinition

```
[Spezifizierer] Rückgabedatentyp Funktionsname([Parameterliste]) {
   /* Anweisungsblock mit Anweisungen */
   [return Rückgabewert]
}
```

* Rückgabedatentyp - Welchen Datentyp hat der Rückgabewert?

  Eine Funktion ohne Rückgabewert wird vom Programmierer als `void`
  deklariert. Sollten Sie keinen Rückgabetyp angeben, so wird
  automatisch eine Funktion mit Rückgabewert vom Datentyp `int` erzeugt.

* Funktionsname - Dieser Bestandteil der Funktionsdefinition ist
  eine eindeutige Bezeichnung, die für den Aufruf der Funktion
  verwendet wird.

  Es gelten die gleichen Regeln für die Namensvergabe wie für
  Variablen. Logischerweise sollten keine Funktionsnamen der
  Laufzeitbibliothek verwenden, wie z. B. `printf()`.

* Parameterliste - Parameter sind Variablen (oder Pointer darauf) die durch
  einen Datentyp und einen Namen spezifiziert werden. Mehrere Parameter
  werden durch Kommas getrennt.

  Parameterliste ist optional, die Klammern jedoch nicht.  Alternative zur
  fehlenden Parameterliste ist die Liste aus einen Parameter vom Datentyp `void` ohne Angabe des Namen.

* Anweisungsblock - Der Anweisungsblock umfasst die im Rahmen der
  Funktion auszuführenden Anweisungen und Deklarationen. Er wird
  durch geschweifte Klammern gekapselt.

* Spezifizierer - Hier wird die Konfiguration bestimmter
  Speicherklassen eröffnet. Erlaubt sind Speicherklassen `extern` und `static`,
  wobei `static` bei Funktionen (und globalen Variablen) bewirkt, dass auf die
  Funktion (Variable) nur innerhalb einer Datei zugegriffen werden kann.

Die Funktionsdefinition wird für jede Funktion genau einmal benötigt.


### Aufruf der Funktion
> **Merke:** Die Funktion (mit der Ausnahme der `main`-Funktion) wird erst
> ausgeführt, wenn sie aufgerufen wird. Vor dem Aufruf muss die Funktion
> definiert oder deklariert werden.

Der Funktionsaufruf einer Funktionen mit dem Rückgabewert ist meistens Teil
einer Anweisung, z.B. einer Zuweisung oder einer Ausgabeanweisung.


### Fehler

**Rückgabewert ohne Rückgabedefintion**
**Erwartung eines Rückgabewertes**
**Implizite Convertierungen**
**Parameterübergabe ohne entsprechende Spezifikation**
**Anweisungen nach dem return-Schlüsselwort**
**Falsche Reihenfolgen der Parameter**

### Funktionsdeklaration

Damit der Compiler überhaupt von einer Funktion Kenntnis nimmt, muss diese vor
ihrem Aufruf bekannt gegeben werden. Im vorangegangenen Beispiel wird die
die Funktion erst nach dem Aufruf definiert. Hier erfolgt eine automatische
(implizite) Deklaration. Der Complier zeigt dies aber durch ein *Warning* an.

Das Ganze wird dann relevant, wenn Funktionen aus anderen Quellcodedateien
eingefügt werden sollen. Die Deklaration macht den Compiler mit dem Aussehen der
Funktion bekannt. Diese werden mit `extern` als Spezifizierer markiert.

```cpp
extern float berechneFlaeche(float breite, float hoehe);
```

### Parameterübergabe und Rückgabewerte

Bisher wurden Funktionen betrachtet, die skalere Werte als Parameter erhilten
und ebenfalls einen skalaren Wert als einen Rückgabewert lieferten. Allerdings
ist diese Möglichkeit sehr einschränkend.

Es wird in vielen Programmiersprachen, darunter in C, zwei Arten der
Parameterübergabe realisiert.

**call-by-value**

In allen Beispielen bis jetzt wurden Parameter an die Funktionen *call-by-value*,
übergeben. Das bedeutet, dass innerhalb der aufgerufenen Funktion mit einer
Kopie der Variable gearbeitet wird und die Änderungen sich nicht auf den
ursprünglichen Wert auswirken.

**call-by-reference**

Bei einer Übergabe als Referenz wirken sich Änderungen an den Parametern auf die
ursprünglichen Werte aus. *Call-by-reference* wird unbedigt notwendig, wenn eine
Funktion mehrere Rückgabewerte hat.

Mit Hilfe des Zeigers wird in C die "call-by-reference"- Parameterübergabe
realisiert. In der Liste der formalen Parameter wird ein Zeiger eines
passenden Typs definiert. Beim Funktionsaufruf wird als Argument statt
Variable eine Adresse übergeben. Beachten Sie, dass für den Zugriff auf den Inhalt des Zeigers (einer Adresse) der Inhaltsoperator `*` benötigt wird.

Die Adresse einer Variable wird mit dem Adressenoperator `&`
ermittelt. Weiterhin kann an den Zeiger-Parameter eine Array-Variable
übergeben werden.

Der Vorteil der Verwendung der Zeiger als Parameter besteht darin, dass
in der Funktion mehrere Variablen auf eine elegante Weise verändert
werden können. Die Funktion hat somit quasi mehrere Ergebnisse.

```c     ParameterIII.c
#include <stdio.h>
#include <stdlib.h>

void tauschen(char *anna, char *hanna){
  char aux=*anna;
  *anna=*hanna;
  *hanna=aux;
}

int main(void) {
  char anna='A',hanna='H';
  printf("%c und %c\n", anna,hanna);
  tauschen(&anna,&hanna);
  printf("%c und %c\n", anna,hanna);;
  return EXIT_SUCCESS;
}
```
@LIA.eval(`["main.c"]`, `gcc -Wall main.c -o a.out`, `./a.out`)

### Zeiger als Rückgabewerte

Analog zur Bereitstellung von Parametern entsprechend dem "call-by-reference"
Konzept können auch Rückgabewerte als Pointer vorgesehen sein. Allerdings
sollen Sie dabei aufpassen ...

Mit dem Beenden der Funktion werden deren lokale Variablen vom Stack gelöscht.
Um diese Situation zu handhaben können Sie zwei Lösungsansätze realisieren.

**Variante 1**  Sie übergeben den Rückgabewert in der Parameterliste.

**Variante 2** Rückgabezeiger adressiert mit `static` bezeichnete Variable. Aber Achtung, diese Lösung funktioniert nicht bei rekursiven Aufrufen.

{{1}}
```c                             PointerInsteadOfReturnI.c
#include <stdio.h>
#include <stdlib.h>

int* cumsum(int wert) {
  static int sum = 0;
  sum += wert;
  return &sum;
}

int main(void) {
  int wert = 2;
  int *sum;
  sum=cumsum(wert);
  sum=cumsum(wert);
  printf("Die Summe ist : %d\n", *sum);
  sum=cumsum(wert);
  printf("Die Summe ist : %d\n", *sum);
  return EXIT_SUCCESS;
}
```
@LIA.eval(`["main.c"]`, `gcc -Wall main.c -o a.out`, `./a.out`)

{{2}}
**Variante 3** Für den Rückgabezeiger wird der Speicherplatz mit `malloc` dynamisch angelegt (dazu später mehr).

{{2}}
```c                        PointerInsteadOfReturnII.c
#include <stdio.h>
#include <stdlib.h>
#include <math.h>

double* kreisflaeche(double durchmesser) {
  double *flaeche=(double*)malloc(sizeof(double));
  *flaeche = M_PI * pow(durchmesser / 2, 2);
  return flaeche;
}

int main(void) {
  double wert = 5.0;
  double *flaeche;
  flaeche=kreisflaeche(wert);
  printf("Die Kreisfläche beträgt für d=%3.1lf[m] %3.1lf[m²] \n", wert, *flaeche);
  return EXIT_SUCCESS;
}
```
@LIA.eval(`["main.c"]`, `gcc -Wall main.c -o a.out -lm`, `./a.out`)


### `main`-Funktion

In jedem Programm muss und darf nur ein `main`-Funktion geben. Diese Funktion
wird beim Programmstart automatisch ausgeführt.

Definition der `main`-Funktion entsprechend dem C99-Standard:

```c
int main(void) {
  /*Anweisungen*/
}
```

```c
int main(int argc, char *argv[]) {
  /*Anweisungen*/
}
```

Mehr zu den Parameter `argc` und `argv` in einer der folgenden Vorlesungen.

### `inline`-Funktionen

Der `inline`-Funktion wird das Schlüsselwort `inline` vorangestellt, z.B.:

```c
static inline void ausgabeBruch(int z, int n) {
   printf("%d / %d\n", z, n);
}
```

`inline`-Funktion wird vom Compiler direkt an der Stelle eingefügt, wo der Aufruf
stattfinden soll. Gegebenenfalls ist die Ausführung der `inline`-Funktion schneller,
da die mit dem Aufruf verbundenen Sicherung der Rücksprungadresse, der Sprung zur
Funktion und der Rücksprung nach Ausführung entfallen. Das Schlüsselwort `inline`
ist für den Compiler allerdings nur ein Hinweis und kein Befehl.

## Lebensdauer und Sichtbarkeit von Variablen

Im Zusammenhang mit Funktionen stellt sich die Frage nach der Sichtbarkeit und
der Lebensdauer einer Variablen um so mehr.

Zur Erinnerung: **globale**-Variable werden außerhalb jeder Funktionen definiert
und gelten in allen Funktionen, **lokale**-Variablen gelten nur in der Funktion,
in der sie definiert sind.

** Static-Variablen**

`static`-Variablen, definiert in einer Funktion, behalten ihren Wert auch nach
dem Verlassen des Funktionsblocks.

{{1}}
```cpp                static.c
#include <stdio.h>

int zaehler(){
  static int count = 0; //wird nur beim ersten Aufruf ausgeführt
  return ++count;       //wird nur beim jedem Aufruf ausgeführt
}

int main() {
   printf("%d \n", zaehler());
   printf("%d \n", zaehler());
   printf("%d \n", zaehler());
   return 0;
}
```
@LIA.eval(`["main.c"]`, `gcc -Wall main.c -o a.out`, `./a.out`)

## Beispiel des Tages

Eine Funktion, die sich selbst aufruft, wird als rekursive Funktion bezeichnet. Den Aufruf selbst nennt man Rekursion. Als Beispiel dient die  Fakultäts-Funktion `n!`, die sich rekursiv als $n(n-1)!$ definieren lässt (wobei $0! = 1$).

```cpp                fakultaet.c
#include <stdio.h>

int fakultaet (int a){
  if (a == 0)
    return 1;
  else
    return (a * fakultaet(a-1));
}

int main(){
  int eingabe;
  printf("Ganze Zahl eingeben: ");
  scanf("%d",&eingabe);
  printf("Fakultaet der Zahl: %d\n",fakultaet(eingabe));
  return 0;
}
```
@LIA.eval(`["main.c"]`, `gcc -Wall main.c -o a.out`, `./a.out`)
