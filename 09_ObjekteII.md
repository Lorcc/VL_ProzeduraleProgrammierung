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

# Memberfunktionen und Konstruktoren

**Inhalt der heutigen Veranstaltung**

* Implementierung von Konstruktoren
* Überladen von Funktionen
* Anwendung von Klassen

**Fragen an die heutige Veranstaltung ...**

* Welche Aufgabe erfüllt ein Konstruktor?
* Was geschieht, wenn kein expliziter Konstruktor durch den Entwickler vorgesehen wurde?
* Wann ist ein Dekonstruktor erforderlich?
* Was bedeutet das "Überladen" von Funktionen?
* Nach welchen Kriterien werden überladene Funktionen differenziert?

## Parallelität von Klassen und Strukturen in C++

> **Merke: ** Während `struct` in C nur eine Datenstruktur beschreibt, kann in C++ damit ein Objekt spezifiziert werden!

> **Merke: ** Klassen und Strukturen unterscheiden sich unter C++ durch die Default-Zugriffsrechte und die Möglichkeit der Vererbung.

> Im Folgenden fokussieren die Ausführungen Klassen, eine analoge Anwendung mit Strukturen ist aber zumeist möglich.

## Memberfunktionen

Mit der Integration einer Funktion in eine Klasse wird diese zur _Methode_ oder _Memberfunktion_. Der Mechanismus bleibt zwar der gleiche, es erfolgt der Aufruf, ggf mit Parametern, die Abarbeitung realisiert Berechnungen, Ausgaben usw. und ein optionaler Rückgabewert bzw. geänderte Parameter (bei Call-by-Referenz Aufrufen) werden zurückgegeben.

Worin liegt der technische Unterschied?

```cpp                     ApplicationOfStructs.cpp
#include <iostream>

class Student{
  public:
    std::string name;  // "-"
    int alter;
    std::string ort;

    void ausgabeMethode(){
        std::cout << name << " " << ort << " " << alter  << std::endl;
    }
};

void ausgabeFunktion(Student studentA){
    std::cout << studentA.name << " " << studentA.ort << " " << studentA.alter  << std::endl;
}

int main()
{
  Student bernhard {"Cotta", 25, "Zillbach"};
  bernhard.ausgabeMethode();

  ausgabeFunktion(bernhard);

  return EXIT_SUCCESS;
}
```
@LIA.eval(`["main.c"]`, `g++ -Wall main.c -o a.out`, `./a.out`)

Methoden können, wie wir es bereits im Beispiel der vergangenen Woche gesehen werden auch von der Klassendefinition getrennt werden.

Diesen Ansatz kann man weiter treiben und die Aufteilung auf einzelne Dateien realisieren. Der Code einer inline-Funktion wird vom Compiler direkt an der Stelle eingefügt, wo der Aufruf stattfindet. Die inline-Funktionen sollten aber möglichst klein gehalten werden. In der Klasse definierte Funktionen sind alle inline, d.h. umfangreiche Funktionen sollen sinnvollerweise außerhalb der Klasse definiert werden und in der Klasse nur deklariert. Es bietet sich weiterhin die Verteilung auf .h und .cpp-Dateien.

Was gab es noch mal an unserer `ausgabeMethode()` zu meckern? In der aktuellen Version geben wir das Gerät, auf das die Ausgabe umgesetzt wird explizt im Code an. Was aber, wenn jemand die Implementierung nutzen möchte und das Resultat eben nicht in der Konsole sondern auf dem Drucker, einem Speicher usw. ausgegeben wissen möchte?

Das "Zieldevice" wird als Referenz übergeben (vgl. Vorlesung 08). Damit erzeugen wir keine Kopie sondern können auf der ursprünglichen Instanz den Zustand verändern (Ausgabedaten übergeben).

### Überladung von Methoden

C verbietet Funktionen die einen gleichen Namen haben. Damit ist die variable Verwendung von Funktionen in Abhängigkeit von der Signatur der Funktion nicht möglich.

Beachten Sie einen zweiten Effekt im oben genannten Beispiel! Wenn Sie die Zeilen 7 bis 9 auskommentieren, kommt eine implizierter Cast-Operation also eine Typumwandlung zur Wirkung. Der Compiler bildet unsere Gleitkommazahlen auf Ganzzahldarstellungen ab und wendet die Funktion an. Das entspricht aber nicht unserer Intention in diesem Abschnitt - wir wollten ja zwei Funktionen für unterschiedliche Datentypen entwerfen.

C++ eröffnet mit dem Überladen von Funktionen neue Möglichkeiten. Dieser Mechanismus lässt sich auf Funktionen und Methoden anwenden.


```cpp                     ostream.cpp
#include <iostream>
#include <fstream>

class Seminar{
  public:
    std::string name;
    bool passed;
};

class Lecture{
  public:
    std::string name;
    float mark;
};

class Student{
  public:
    std::string name;  // "-"
    int alter;
    std::string ort;

    void printCertificate(std::ostream& os, Seminar sem);
    void printCertificate(std::ostream& os, Lecture sem);
};

void Student::printCertificate(std::ostream& os, Seminar sem){
  std::string comment = " nicht bestanden";
  if (sem.passed)
    comment = " bestanden!";
  os << "\n" << name << " hat das Seminar " << sem.name <<  comment;
}

void Student::printCertificate(std::ostream& os, Lecture lect){
  os << "\n" << name << " hat in der Vorlesung " << lect.name << " die Note " << lect.mark << " erreicht";
}

int main()
{
  Student bernhard {"Cotta", 25, "Zillbach"};
  Seminar roboticSeminar {"Robotik-Seminar", false};
  Lecture ProzProg {"Prozedurale Programmierung", 1.3};

  bernhard.printCertificate(std::cout, roboticSeminar);
  bernhard.printCertificate(std::cout, ProzProg);

  return EXIT_SUCCESS;
}
```
@LIA.eval(`["main.c"]`, `g++ -Wall main.c -o a.out`, `./a.out`)

> **Merke: ** Der Rückgabedatentyp trägt nicht zur Unterscheidung der Methoden bei. Unterscheidet sich die Signatur nur in diesem Punkt, "meckert" der Compiler.

## Initalisierung/Zerstören eines Objektes

Die Klasse spezifiziert unter anderem (!) welche Daten in den Instanzen/Objekten zusammenfasst werden. Wie aber erfolgt die Initialisierung? Bisher haben wir die Daten bei der Erzeugung der Instanz übergeben.


Es entstehen 3 Instanzen der Klasse `Student`, die sich im Variablennamen `bernhard`, `alexander` und `unbekannt` und den Daten unterscheiden.

Im Grunde können wir unsere drei Datenfelder im Beispiel in vier
Kombinationen  initialisieren:

```
{name, alter, ort}
{name, alter}
{name}
{}
```

### Konstruktoren

Konstruktoren dienen der Koordination der Initialisierung der Instanz einer Klasse. Sie werden entweder implizit über den Compiler erzeugt oder explizit durch den Programmierer angelegt.

> **Merke: ** Ein Konstruktor hat keinen Rückgabetyp!

Beim Aufruf `Student bernhard {"Cotta", 25, "Zillbach"};` erzeugt der Compiler eine Methode `Student::Student(std::string, int, std::string)`, die die Initialisierungsparameter entgegennimmt und diese der Reihenfolge nach an die Membervariablen übergibt. Sobald wir nur einen explizten Konstruktor integrieren, weist der Compiler diese Verantwortung von sich.

Dabei sind innerhalb des Konstruktors zwei Schreibweisen möglich:

```cpp
//Initalisierung
Student(std::string name, int alter, std::string ort): name(name), alter(alter), ort(ort)
{
}

// Zuweisung innerhalb des Konstruktors
Student(std::string name, int alter, std::string ort){
    this->name = name;
    this->alter = alter;
    this->ort = ort;
}
```

Die zuvor beschriebene Methodenüberladung kann auch auf die Konstruktoren angewandt werden. Entsprechend stehen dann eigene Aufrufmethoden für verschiedene Datenkonfigurationen zur Verfügung. In diesem Fall können wir auf drei verschiedenen Wegen Default-Werte setzen:

+ ohne spezfische Vorgabe wird der Standardinitialisierungswert verwendt (Ganzzahlen 0, Gleitkomma 0.0, Strings "")
+ die Vorgabe eines indivduellen Default-Wertes (vgl. Zeile 5)

Delegierende Konstruktoren rufen einen weiteren Konstruktor für die teilweise
Initialisierung auf. Damit lassen sich Codeduplikationen, die sich aus der
Kombination aller Parameter ergeben, minimieren.

```cpp
Student(std::string n, int a, std::string o): name{n}, alter{a}, ort{o} { }
Student(std::string n) : Student (n, 18, "Freiberg") {};
Student(int a, std::string o): Student ("unknown", a, o) {};
```

### Destruktoren

```cpp                     Destructor.cpp
#include <iostream>

class Student{
  public:
    std::string name;
    int alter;
    std::string ort;

    Student(std::string n, int a, std::string o);
    ~Student();
};

Student::Student(std::string n, int a, std::string o): name{n}, alter{a}, ort{o} {}

Student::~Student(){
  std::cout << "Destructing object of type 'Student' with name = '" << this->name << "'\n";
}

int main()
{
  Student max {"Maier", 19, "Dresden"};
  std::cout << "End...\n";
  return 0;
}
```
@LIA.eval(`["main.c"]`, `g++ -Wall main.c -o a.out`, `./a.out`)

Destruktoren werden aufgerufen, wenn eines der folgenden Ereignisse eintritt:

* Das Programm verlässt den Gültigkeitsbereich (*Scope*, d.h. einen Bereich der mit `{...}` umschlossen ist) eines lokalen Objektes.
* Ein Objekt, das `new`-erzeugt wurde, wird mithilfe von `delete` explizit aufgehoben (Speicherung auf dem Heap)
* Ein Programm endet und es sind globale oder statische Objekte vorhanden.
* Der Destruktor wird unter Verwendung des vollqualifizierten Namens der Funktion explizit aufgerufen.

Einen Destruktor explizit aufzurufen, ist selten notwendig (oder gar eine gute Idee!).