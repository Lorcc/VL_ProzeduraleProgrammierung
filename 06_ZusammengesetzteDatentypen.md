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

# Zusammengesetzte Datentypen

**Inhalt der heutigen Veranstaltung**

* Nutzung von Präkompiler-Direktiven
* Definition eigener Typen
* Abbildung zusammengesetzter Datentypen in C

**Fragen an die heutige Veranstaltung ...**

* Was sind *Arrays*, `struct`s und `enum`s?
* Erklären Sie den Unterschied zwischen Initalisierung und Zuweisung von Variablen!
* Wie vergleichen Sie zwei `structs`?

## Exkurs: Präcompiler Direktiven

Ein Präprozessor (seltener auch Präcompiler) ist ein Computerprogramm, das Eingabedaten vorbereitet und zur weiteren Bearbeitung an ein anderes Programm weitergibt. Der Präprozessor wird häufig von Compilern oder Interpretern dazu verwendet, einen Eingabetext zu konvertieren und das Ergebnis im eigentlichen Programm weiter zu verarbeiten.

Da sich der C-Präprozessor nicht auf die Beschreibung der Sprache C stützt, sondern ausschließlich seine ihm bekannten Anweisungen erkennt und bearbeitet, kann er auch als reiner Textersetzer für andere Zwecke verwendet werden.

![instruction-set](./images/00_Einfuehrung/compilerElements.png)<!-- width="100%" -->

Der C-Präprozessor realisiert dabei die

+ Zusammenfassung von Strings
+ Löschung von Zeilenumbrüchen und Kommentaren (Ersetzung durch Leerzeichen)
+ Whitespace-Zeichen zwischen Tokens werden gelöscht.
+ Kopieren der Header- und Quelldateien in den Quelltext kopieren (`#include`)
+ Einbinden von Konstanten (`#define`)
+ Extrahieren von Codebereichen mit einer bedingten Kompilierung (`#ifdef`, `#elseif`, ...)

Letztgenannte 3 Abläufe werden durch den Entwickler spezifiziert. Dazu bedient er
sich sogenannter Direktiven. Sie beginnen mit # und müssen nicht mit einem Semikolon abgeschlossen werden. Eventuell vorkommende Sonderzeichen in den Parametern müssen nicht escaped werden.

```c
#Direktive Parameter
```

### #include

Include-Direktiven kennen Sie bereits aus unseren Beispielprogrammen. Damit binden
wir Standardbibliotheken oder eigenen Source-Datei ein.
Es gibt zwei Arten der `#include`-Direktive, nämlich

```c
#include <Datei.h>
#include "Datei.h"
```

Die erste Anweisung sucht die Datei im Standard-Include-Verzeichnis des Compilers, die zweite Anweisung sucht die Datei zuerst im Verzeichnis, in der sich die aktuelle Sourcedatei befindet; sollte dort keine Datei mit diesem Namen vorhanden sein, sucht sie ebenfalls im Standard-Include-Verzeichnis.

Mit dem Präprozessoraufruf werden die Inhalte der Header-Files in unseren Code
kopiert. Dieser wird dadurch um ein vielfaches größer, umfasst nun aber alle
Funktionsdeklarationen, die genutzt werden sollen.

 1 "/usr/include/x86_64-linux-gnu/bits/types.h" 1 3 4
 27 "/usr/include/x86_64-linux-gnu/bits/types.h" 3 4
 1 "/usr/include/x86_64-linux-gnu/bits/wordsize.h" 1 3 4
 28 "/usr/include/x86_64-linux-gnu/bits/types.h" 2 3 4

Warum muss vermieden werden, dass headerfiles kreuzweise eingebunden werden?

### #define

`#define` kann in drei verschiedenen Arten genutzt werden, um ein Symbol überhaupt
zu definieren, einen konkreten Wert zuzuordnen oder aber


In allen Fällen erfolgt lediglich eine Textersetzung im Programmcode! Dies kann
auch auf weitergehende Codefragmente ausgedehnt werden.

Im Standard-C müssen bereits einige Makros im Präprozessor vordefiniert sein. Die Namen der vordefinierten Makros beginnen und enden jeweils mit zwei Unterstrichen. Die wichtigsten vordefinierten Makros sind in der folgenden Tabelle aufgelistet.

| Define    | Bedeutung                                         |
|:----------|:--------------------------------------------------|
|`__LINE__` 	| Zeilennummer innerhalb der aktuellen Quellcodedatei |
|`__FILE__` 	|Name der aktuellen Quellcodedatei  |
|`__DATE__` 	|Datum, wann das Programm compiliert wurde (als Zeichenkette)|
|`__STDC__` 	|Liefert eine 1, wenn sich der Compiler nach dem Standard-C richtet. |
|`__STDC_VERSION__` | 	Liefert die Zahl 199409L, wenn sich der Compiler nach dem  C95-Standard richtet; die Zahl 199901L, wenn sich der Compiler nach dem C99-Standard richtet. Ansonsten ist dieses Makro nicht definiert.|

Daneben gibt es weitere vordefinierte Makros, die das Betriebssystem zurückgeben.

```c       SystemMacros.c
#include <stdio.h>
#include <stdlib.h>

#define TAUSCHE(a, b, typ) { typ temp; temp=b; b=a; a=temp; }

int main(void) {
  printf("Programm wurde compiliert am ");
  printf("%s um %s.\n", __DATE__, __TIME__);

  printf("Diese Programmzeile steht in Zeile ");
  printf("%d in der Datei %s.\n", __LINE__, __FILE__);

  #ifdef __STDC__
  printf("Standard-C-Compiler!\n");
  #else
  printf("Kein Standard-C-Compiler!\n");
  #endif
  return EXIT_SUCCESS;
}
```
@LIA.eval(`["main.c"]`, `gcc -Wall main.c -o a.out`, `./a.out`)


## Aufzählungen

Enumerationen, kurz `enum`, dienen der Definition bestimmter Sets von Elementen,
die eine Variable überhaupt annehmen kann. Wenn wir zum Beispiel die Farben
einer Ampel in einem Programm handhaben wollen, sind dies lediglich "Rot", "Gelb"
und "Grün". Im Schachspiel sind nur die Figuren "Bauer", "Pferd", "Springer",
"Turm", "Dame" und "König" definiert.

Nun, dass können wir doch wunderbar mit unseren Präprozessordirektiven umsetzen!



Allerdings ist das Ganze recht unflexibel und der Evaluation des Compilers entzogen.

Die Definition eines Aufzählungsdatentyps `enum` hat die Form wie im folgenden
Beispiel:

Möglicherweise sollen den Karten aber auch konkrete Werte zugeordnet werden,
die bestimmte Wertigkeiten reflektieren.

An dieser Stelle sind Sie aber frei, was die eigentlichen Werte angeht. Es sind
zum Beispiel Konfigurationen möglich wie

## Typdefinition mit `typedef`

Mit Hilfe des Schlüsselworts `typedef` kann für einen Datentyp, einschließlich
eines `struct`-Datentyps, ein neuer Bezeichner definiert werden:

```c
typedef Typendefinition Bezeichner;
```

Zum Beispiel kann der Datentyp `String` definiert und zur einfacheren
Variablendeklaration verwendet werden:

```
typedef char* string;
sring s = "Hallo";
```

> **Achtung!** Hinter `string` verbirgt sich noch immer ein `char *` und nicht etwa ein "echter" String-Datentyp.

Auf ein `enum` angewandt vermeidet man damit die Wiederholung des Keywords `enum`

Und konkret am Beispiel:

```cpp    enumApp.c
#include <stdio.h>

enum textformat
{
    RED = 1,
    BOLD = 2,
    ITALIC = 4,
    EXCLAMATION = 8
};

typedef enum textformat format;

void printColor(const char text[], format chosenFormat)
{
    if (chosenFormat & RED){
        printf("Rot - ");
    }
    if (chosenFormat & BOLD){
        printf("Fett - ");
    }
    if (chosenFormat & ITALIC){
        printf("Kursiv -");
    }
    printf("%s", text);
    if (chosenFormat & EXCLAMATION)
    {
        printf(" !!!\n");
    }
}

int main(void) {
  const char text[]  = "Bergakademie Freiberg";
  format chosenFormat = RED | EXCLAMATION;
  printColor(text, chosenFormat);
  return 0;
}
```
@LIA.eval(`["main.c"]`, `gcc -Wall main.c -o a.out`, `./a.out`)

## Strukturen

Mit `struct` werden zusammengehörige Variablen unterschiedlicher Datentypen und
Bedeutung in einem Konstrukt zusammengefasst. Damit wird für den Entwickler der
Zusammenhang deutlich. Die Variablen der Struktur werden als Komponenten (engl.
members) bezeichnet.

Beispiele:

```cpp
struct datum
{
  int tag;
  char monat[10];
  int jahr;
};
```

### Deklaration, Definition, Initialisierung und Zugriff

Und wie erzeuge ich Variablen dieses erweiterten Types, wie werden diese
initialisiert und wie kann ich auf die einzelnen Komponenten zugreifen?

Der Bespiel zeigt, dass die Definition der Variable unmittelbar nach der
`struct`-Definition oder mit einer gesonderten Anweisung mit einer vollständigen,
partiellen Initialisierung bzw. ohne Initialisierung erfolgen kann.

Die nachträgliche Veränderung einzelner Komponenten ist über Zugriff mit Hilfe
des Punkt-Operators möglich.

### Vergleich von `struct`-Variablen

Der C-Standard kennt keine Methodik um `struct`s in einem Rutsch auf Gleichheit
zu prüfen. Entsprechend ist es an jedem Entwickler eine eigene Funktion dafür zu
schreiben. Diese kann unterschiedliche Aspekte des `struct`s adressieren.

Im nachfolgenden Beispiel werden zur Überprüfung der Gleichheit die `tag`und
`monat` vergliehen. Der Vergleich wird dadurch vereinfacht, dass
wir für die Repräsentation des Monats ein `enum` verwenden. Damit entfällt
aufwändigerer Vergleich der Strings.

### Arrays und Funktionen

Wie erfolgt die Übergabe von `struct`s an Funktionen?

### Arrays von Strukturen

Natürlich lassen sich die beiden erweiterten Datenformate auf der Basis von
`struct` und Arrays miteinander kombinieren.

```cpp      ArrayOfStruct.c
#include <stdio.h>
#include <string.h>

#define ENTRIES 3

int main() {

  enum {Jan, Feb, Maerz, April, Mai, Juni, Juli, Aug, Sept, Okt, Nov, Dez};

  char months[12][20] = {"Januar", "Februar", "Maerz", "April",
                         "Mai", "Juni", "Juli", "August", "September",
                         "Oktober", "November", "Dezember"};

  struct datum
  {
      unsigned char tag;     // wert < 31
      unsigned char monat;   // wert < 12
      short jahr;            // wert < 2048
  };

  struct datum geburtstage [ENTRIES] = {{18, April, 1986},
                                        {12, Mai, 1820}};

  geburtstage[2].tag = 5;
  geburtstage[2].monat = Sept;
  geburtstage[2].jahr = 1905;

  for (int i=0; i<ENTRIES; i++)
    printf("%2d. %-10s %4d\n", geburtstage[i].tag,
                               months[geburtstage[i].monat],
                               geburtstage[i].jahr);

  return 0;
}
```
@LIA.eval(`["main.c"]`, `gcc -Wall main.c -o a.out`, `./a.out`)
