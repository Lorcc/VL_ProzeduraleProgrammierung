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

# Standardalgorithmen in C

**Inhalt der heutigen Veranstaltung**

* Zusammenfassender Überblick zum bisher gelernten
* Darstellung von Basisalgorithmen in C
* Realisierung von deren Implementierung in der Standardbibliothek

**Fragen an die heutige Veranstaltung ...**

* Was ist ein Algorithmus und über welche Merkmale lässt er sich ausdrücken.
* Nennen Sie Beispiele für Algorithmen aus dem täglichen Leben.
* Wie erfolgt die Transformation des Algorithmus auf eine Programmiersprache?
* Was bedeutet der Begriff der Komplexität eines Algorithmus?
* Welchem fundamentalen Konzept der Informatik unterliegen der Quicksort Algorithmus und die binäre Suche?

## Rekursion

Die Rekursion ist ein Aufruf einer Funktionen aus sich selbst heraus. Da bei einem Aufruf sich die Funktion wieder selbst aufruft, benötigt die Funktion wie bei den Schleifen eine Abbruchbedingung, damit die Selbstaufrufe nicht endlos sind.

```cpp      Rekursion1.c
#include<stdio.h>

printLines(int x) {
	if(x > 0) {
		printf("\nZeile Nr. %d", x);
		printLines(x-1);
	}
}

int main() {
	printLines(5);
	return 0;
}
```
@LIA.eval(`["main.c"]`, `gcc -Wall main.c -o a.out`, `./a.out`)

Dieses Programm ist langsamer als eine konventionelle Darstellung in einer Schleife, weil mit dem Aufruf jeder Funktion ein eigener Speicherplatz zum Anlegen von Parametern, lokalen Variablen, Rückgabewerten und Rücksprungadressen belegt wird.

Gleichzeitig steigt aber die Lesbarkeit und Kompaktheit des Codes!

## Algorithmusbegriff

                   {{0-4}}
********************************************************************************

Ein Algorithmus gibt eine strukturierte Vorgehensweise vor, um ein Problem zu lösen. Er implementiert Einzelschritte zur Abbildung von Eingabedaten auf Ausgabedaten.
Algorithmen bilden die Grundlage der Programmierung und sind **unabhängig** von einer konkreten Programmiersprache. Algorithmen werden nicht nur maschinell durch einen Rechner ausgeführt sondern können auch von Menschen in „natürlicher“ Sprache formuliert und abgearbeitet werden.

********************************************************************************

                  {{1-4}}
********************************************************************************

1. Beispiel - Nassi-Shneiderman-Diagramm - Prüfung von Mineralen

![Smaragdtest](./images/07_Algorithmen/Struckto_smaragd.jpg)<!-- width="60%" -->[^1]

[^1]: Anton Kubala, https://wiki.zum.de/wiki/Hauptseite

********************************************************************************

                  {{2-4}}
********************************************************************************

 2. Beispiel - Funktionsdarstellung - Berechnung der Position

$$ s(t) = \int_{0}^{t} v(t) dt + s_0 $$

********************************************************************************

                  {{3-4}}
********************************************************************************

 3. Beispiel - Verbale Darstellung - Rezept

*"Nehmen Sie ... Schneiden Sie ... Lassen Sie alles gut abkühlen ..."*

********************************************************************************

                  {{4-5}}
********************************************************************************

Algorithmen umfassen Sequenzen (Kompositionen), Wiederholungen (Iterationen) und Verzweigungen (Selektionen) von Handlungsanweisungen.
und besitzen die folgenden charakteristischen Eigenschaften:

+ Eindeutigkeit: ein Algorithmus darf keine widersprüchliche Beschreibung haben. Diese muss eindeutig sein.
+ Ausführbarkeit: jeder Einzelschritt muss ausführbar sein.
+ Finitheit (= Endlichkeit): die Beschreibung des Algorithmus ist von endlicher Länge (statische Finitheit) und belegt zu jedem Zeitpunkt nur eine endliche Menge von Ressourcen (dynamische Finitheit).
+ Terminierung: nach endlich vielen Schritten muss der Algorithmus enden und ein Ergebnis liefern.
+ Determiniertheit: der Algorithmus muss bei gleichen Voraussetzungen stets das gleiche Ergebnis liefern.
+ Determinismus: zu jedem Zeitpunkt der Ausführung besteht höchstens eine Möglichkeit der Fortsetzung. Der Folgeschritt ist also eindeutig bestimmt.

Der erste für einen Computer gedachte Algorithmus (zur Berechnung von Bernoullizahlen) wurde 1843 von Ada Lovelace in ihren Notizen zu Charles Babbages Analytical Engine festgehalten. Sie gilt deshalb als die erste Programmiererin. Weil Charles Babbage seine Analytical Engine nicht vollenden konnte, wurde Ada Lovelaces Algorithmus allerdings nie darauf implementiert.

*"„Die Grenzen der Arithmetik wurden in dem Augenblick überschritten, in dem die Idee zur Verwendung der [Programmier]Karten entstand, und die Analytical Engine hat keine Gemeinsamkeit mit schlichten Rechenmaschinen. Sie ist einmalig, und die Möglichkeiten, die sie andeutet, sind höchst interessant.“*

********************************************************************************

## Suche des Maximums
<!--
  comment: Compare3ValuesWithMacro.cpp
  ..............................................................................
      1. Ersetzen Sie das Makros durch eine Funktionen!
  ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
-->

Bestimmen Sie aus drei Zahlenwerten den größten und geben Sie diesen aus $max(n_0, n_1, n_2)$.


Darfs auch etwas mehr sein? Wie lösen wir die gleiche Aufgabe für größere Mengen
von Zahlenwerten $max(n_0, ... n_k)$ ? Entwerfen Sie dazu folgende Funktionen:

+ `void generateRandomArray(int * ptr)`
+ `int countMaxValue(int *ptr, int n_samples)`

die zunächst gleichverteilte Werte zwischen `MAXVALUE` und `MINVALUE` befüllt
und dann die Häufigkeit des größten Wertes ermittelt.


## Sortieren

              {{0-1}}
********************************************************************************

Lassen Sie uns die Idee der Max-Funktion nutzen, um das Array insgesamt zu
sortieren. Dazu wird in einer Schleife (Zeile 42) der maximale Wert bestimmt,
wobei dessen Eintrag aus dem bestehenden Array mit einer -1 überschrieben
wird.

Welche Nachteile sehen Sie in diesem Konzept?

```cpp                     Duration.c
#include <stdio.h>
#include <stdlib.h>
#include <math.h>
#include <time.h>

#define MAXVALUE 100
#define MINVALUE 5
#define SAMPLES 1000

void generateRandomArray(int * ptr){
  srand(time(NULL));
  for (int i = 0; i< SAMPLES; i++){
      ptr[i] = rand() % (MAXVALUE - MINVALUE + 1) + MINVALUE;
  }
}

int maxValue(int *ptr){
  int max = 0;
  int max_index = 0;
  for (int i = 0; i< SAMPLES; i++){
      if (ptr[i] > max) {
        max = ptr[i];
        max_index = i;
      }
  }
  ptr[max_index] = -1;
  return max;
}

void printArray(int *ptr){
  for (int i = 0; i< SAMPLES; i++){
    if ((i>0) && (i%10==0)) {
      printf("\n");
    }
    printf(" %3d ", ptr[i]);
  }
}

int main(void){
  int samples[SAMPLES] = {0};
  generateRandomArray(samples);
  clock_t start = clock();
  int sorted[SAMPLES] = {0};
  for (int i = 0; i< SAMPLES; i++){
    sorted[i] = maxValue(samples);
  }
  clock_t end = clock();
  printArray(sorted);
  double cpu_time_used = ((double) (end - start)) / CLOCKS_PER_SEC;

  printf("Der Rechner benötigt für %d Samples %f Sekunden \n", SAMPLES,cpu_time_used);

  return(EXIT_SUCCESS);
}
```
@LIA.eval(`["main.c"]`, `gcc -Wall main.c -o a.out`, `./a.out`)

* Das Ursprungsarray wird beim Sortiervorgang zerstört, am Ende umfasst es ausschließlich -1-Einträge
* Die Ausführungsdauer wird durch `SAMPLES` x `SAMPLES` Vergleichsoperationen bestimmt.

Welche Konsequenz hat dieses Verhalten?

**BubbleSort**

Die Informatik kennt eine Vielzahl von Sortierverfahren, die unterschiedliche
Eigenschaften aufweisen. Ein sehr einfacher Ansatz ist BubbleSort, der
namensgebend die größten oder kleinsten Zahlen Gasblasen gleich aufsteigen lässt.

Worin unterscheidet sich dieser Ansatz von dem vorhergehenden?

Um das erste (und größte) Element $n$ ganz nach rechts zu bewegen, werden $n − 1$
Vertauschungen vorgenommen, für das nächstfolgende $n-2$ usw. Für die Gesamtanzahl
muss also die Summe über $k$ von 1 bis n-1 gebildet werden. Mit der Summenformel
von Gauss kann gezeigt werden, dass im Falle der umgekehrt sortierten Liste werden maximal $\frac{n\cdot (n-1)}{2}$ Vertauschungen zuführen sind.

Welches Optimierungspotential sehen Sie?

In den Code sollte ein Abbruchkriterium integriert werden, wenn während eines
Durchlaufes keine Änderungen vollzogen werden. Im günstigsten Fall lässt sich
damit das Verfahren nach einem Durchlauf beenden.



**Quicksort**

Quicksort ist ein rekursiver Sortieralgorithmus, der die zu sortierende Liste in zwei Teillisten unterteilt und alle Elemente, die kleiner sind als das Pivot-Element, in die linke Teilliste, alle anderen in die rechte Teilliste einsortiert.

Die Buchstabenfolge „einbeispiel“ soll alphabetisch sortiert werden.

Ausgangssituation nach Initialisierung von i und j, das Element rechts ("l") ist das Pivotelement:

```
  e i n b e i s p i e l
  ^                 ^
  i                 j
```

Nach der ersten Suche in den inneren Schleifen hat i auf einem Element $>= l$ und j auf einem Element $<= l$ gehalten:

```
  e i n b e i s p i e l
      ^             ^
      i             j
```

Nach dem Tauschen der Elemente bei i und j:

```
  e i e b e i s p i n l
      ^             ^
      i             j
```

Nach der nächsten Suche und Tauschen:

```
  e i e b e i i p s n l
              ^   ^
              i   j
```

Nach einer weiteren Suche sind die Indizes aneinander vorbeigelaufen:

```
  e i e b e i i p s n l
              ^ ^
              j i
```

Nach dem Tauschen von i und Pivot bezeichnet i die Trennstelle der Teillisten. Bei i steht das Pivot-Element, links davon sind nur Elemente ≤ Pivot und rechts nur solche > Pivot:

```
  e i e b e i i l s n p
                ^
                i
```
Darauf aufbauend wird der Algorithmus nun auf die beiden Teile "eiebeii" und "snp" angewand.

Obwohl Quicksort im schlechtesten Fall quadratische Laufzeit hat, ist er in der Praxis einer der schnellsten Sortieralgorithmen.

Die C-Standardbibliothek umfasst in der `stdlib.h` eine Implementierung von quicksort - `qsort()` an. Sie wurde in der vorangegangenen Vorlesung besprochen. Ein Anwendungsbeispiel finden Sie im nachfolgenden Abschnitt.

********************************************************************************

## Suchen
<!--
  comment: Search.cpp
  ..............................................................................
      1. Ersetzen Sie die Konventionelle Suche durch eine rekursive Baumsuche!
      ```cpp
      int binsearch (int *ptr, int links, int rechts, int wert) {
          if (links > rechts) {
              return -1;
          }
          int mitte = (rechts+links)/2;
          if (ptr[mitte] == wert) {
              return mitte;
          }
          if (wert < ptr[mitte]) {
              return binsearch(ptr, links, mitte - 1, wert);
          } else {
              return binsearch(ptr, mitte + 1, rechts, wert);
          }
      }
      ```
  ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
-->
Suchen beschreibt die Identifikation von bestimmten Mustern in Daten. Das Spektrum kann dabei von einzelne Zahlenwerten oder Buchstaben bis hin zu komplexen zusammengesetzten Datentypen reichen.

Wie würden Sie vorgehen, um in einer sortierten List einen bestimmten Eintrag zu
finden?

```cpp Search.c
//shorted
// Rekursive Suche
int search (int *ptr, int pattern) {
  for (int i = 0; i< SAMPLES; i++){
    if (ptr[i] == pattern) return i;
  }
  return -1;
}

int main (void) {
  int samples[SAMPLES] = {0};
  generateRandomArray(samples);
  bubble(samples);
  printArray(samples);
  int pattern = 36;
  int index = search (samples, pattern);
  //int index = binsearch (samples, 0, SAMPLES-1, pattern);
  if (-1==index){
    printf("\nPattern %d nicht gefunden!", pattern);
  }else{
    printf("\nIndex von %d ist %d",pattern, index);
  }
  return(EXIT_SUCCESS);
}
```
@LIA.eval(`["main.c"]`, `gcc -Wall main.c -o a.out`, `./a.out`)

Die Suchtiefe kann mit $\lceil \log_2 (n+1) \rceil$ bestimmt werden.

## Implementierung in der Standardbibliothek

Die Standardbibliothek umfasst eine Suchfunktion, die es erlaubt Arrays nach
beliebigen Kriterien zu sortieren. Der Name `qsort()` deutet dabei an, dass der
Quicksort-Algorithmus zum Einsatz kommt. Die Vergleichsoperation, die vom
Anwender zu implementieren ist, akzeptiert als Übergabewerte Zeiger auf zwei
Einträge im Array und setzt diese in Beziehung. Bei einem negativen Rückgabewert ist das
erste Element kleiner, für einen positiven Wert größer als das zweite Übergabewert. Für den Wert 0 liegt
Gleichheit vor.

```c
void qsort(
   void *array,        // Anfangsadresse des Arrays
   size_t n,           // Anzahl der Elemente zum Sortieren
   size_t size,        // Größe des Datentyps, der sortiert wird
   int (*vergleich_func)(const void*, const void*)   );
```

Eine analoge Funktion steht für die Suche in sortierten Listen bereit. `bsearch()`
durchsucht diese und gibt einen Pointer zurück, der mit dem Suchkriterium
übereinstimmt.

Die binäre Suche ist in der `stdlib.h` als Funktion implementiert. Die Deklaration
erfasst folgende Parameter:

```cpp
void *bsearch(const void *key,
              const void *base,
              size_t nitems,
              size_t size,
              int (*compar)(const void *, const void *))
```
Analog zu `qsort()` wird ein Funktionspointer *compar() der den Vergleich des
`key` mit den Einträgen in `base` realisiert.

```cpp Search.c
#include <stdio.h>
#include <stdlib.h>
#include <time.h>

#define MAXVALUE 100
#define MINVALUE 85
#define SAMPLES 20

// Generieren der Zufallszahlen
void generateRandomArray(int *ptr) {
  srand(time(NULL));
  for (int i = 0; i< SAMPLES; i++){
      ptr[i] = rand() % (MAXVALUE - MINVALUE + 1) + MINVALUE;
  }
}

// Ausgabe
void printArray(int *ptr){
  for (int i = 0; i< SAMPLES; i++){
    if ((i>0) && (i%10==0)) {
      printf("\n");
    }
    printf("%3d ", ptr[i]);
  }
  printf("\n");
}

int cmpfunc (const void * a, const void * b) {
   return ( *(int*)a - *(int*)b );
}

int main (void) {
  int samples[SAMPLES] = {0};
  generateRandomArray(samples);
  qsort(samples, SAMPLES, sizeof(int), cmpfunc);
  printArray(samples);
  int pattern = 90;
  int *item;
  item = (int*) bsearch (&pattern, samples, SAMPLES, sizeof(int), cmpfunc);
  if( item != NULL ) {
    printf("%d an der Speicherstelle %p gefunden!\n", pattern, item);
    printf("Das Array beginnt im Speicher bei %p\n", samples);
    printf("Der Index von %d beträgt folglich %d\n", pattern, item - samples);
  }else{
    printf("%d nicht gefunden!", pattern);
  }
  return(EXIT_SUCCESS);
}
```
@LIA.eval(`["main.c"]`, `gcc -Wall main.c -o a.out`, `./a.out`)

`bsearch` evaluiert die Existenz eines Eintrages, dabei ist es wichtig, den Rückgabewert auf den richtigen Typ des Eintrages zu casten.
