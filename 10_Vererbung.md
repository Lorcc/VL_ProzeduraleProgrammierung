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

# Operatoren und Vererbung

**Wie weit waren wir gekommen?**

```text @plantUML.png
+-------------------------------------+  +-------------------------------------+
| API Implementierung des Herstellers |  | Eigene Klassenimplementierungen     |
| * Led-Klasse                        |  | * Filter-Klasse                     |
| * Drucksensor-Klasse                |  | * System-Monitor-Klasse             |
| * Serial-Klasse                     |  | * ....                              |
| * ....                        cCCB  |  | * ....                      cBFB    |
+-------------------------------------+  +-------------------------------------+
               |                                            |
               +-----------------------+--------------------+
                                       |
                                       v
                   +-------------------------------------+
                   | // Mein Programm                    |
                   | void setup{                         |
                   |   RGB_LED rgbLed(red, green, blue); |
                   | }                                   |
                   |                                     |
                   | void setup{                         |
                   |   rgbLed.setColor(255, 0, 0);       |
                   | }                      {d}cBFB      |
                   +-------------------------------------+                      
```

Für die Implementierung einer Ausgabe auf dem Display des MXCHIP Boards nutzen wir die Klassenimplementierung der API.

[Link](https://microsoft.github.io/azure-iot-developer-kit/docs/apis/display/)


**Inhalt der heutigen Veranstaltung**

* Vertiefung der Anwendung objektorientierter Konzepte
* Anwendung spezifischer Operatorenkonzepte
* Einführung der Vererbung

**Fragen an die heutige Veranstaltung ...**

* Was sind Operatoren?
* Warum werden eigene Operatoren für individuelle Klassen benötigt?
* Wann spricht man von Vererbung und warum wird sie angewendet?
* Welche Zugriffsattribute kennen Sie im Zusammenhang mit der Vererbung?

## Operatorüberladung
Motivation
-------------------

### Konzept

Das Überladen von Operatoren erlaubt die flexible klassenspezifische Nutzung von Arithmetischen- und Vergleichs-Symbolen wie  `+`, `-`, `*`, `=`. Damit kann deren Bedeutung für selbstdefinierter Klassen (fast) mit einer neuen Bedeutung versehen werden. Ausnahmen bilden   spezieller Operatoren, die nicht überladen werden dürfen (  ?: ,  :: ,  . ,  .* , typeid , sizeof und die Cast-Operatoren).

```
Matrix a, b;
Matrix c = a + b;    \\ hier wird mit dem Plus eine Matrixoperation ausgeführt

String a, b;
String c = a + b;    \\ hier werden mit dem Plus zwei Strings konkateniert
```

Operatorüberladung ist Funktionsüberladung, wobei die Funktionen durch eine spezielle Namensgebung gekennzeichnet sind. Diese beginnen mit dem Schlüsselwort `operator`, das von dem Token für den jeweiligen Operator gefolgt wird.

```cpp
class Matrix{
  public:
    Matrix operator+(Matrix zweiterOperand){ ... }
    Matrix operator/(Matrix zweiterOperand){ ... }
    Matrix operator*(Matrix zweiterOperand){ ... }
}

class String{
  public:
    String operator+(String zweiterString){ ... }
}
```

Operatoren können entweder als Methoden der Klasse oder als globale Funktionen überladen werden. Die Methodenbeispiele sind zuvor dargestellt, analoge Funktionen ergeben sich zu:

```cpp
class Matrix{
  public:
    ...
}

Matrix operator+(Matrix ersterOperand, Matrix zweiterOperand){ ... }
Matrix operator/(Matrix ersterOperand, Matrix zweiterOperand){ ... }
Matrix operator*(Matrix ersterOperand, Matrix zweiterOperand){ ... }
```

> **Merke:** Funktion oder Methode - welche Version sollte wann zum Einsatz kommen? Einstellige Operatoren `++` sollten Sie als Methode, zweistellige Operatoren ohne Manipulation der Operanden als Funktion implementieren. Für zweistellige Operatoren, die einen der Operanden verändern (`+=`), sollte als Methode realisiert werden.

Als Beispiel betrachten wir eine Klasse Rechteck und implementieren zwei Operatorüberladungen:

+ eine Vergleichsoperation
+ eine Additionsoperation die `A = A + B` oder abgekürzt `A+=B`

implementiert.

> **Merke:** Üblicherweise werden die Operanden bei der Operatorüberladung als Referenzen übergeben. Damit wird eine Kopie vermieden. In Kombination mit dem Schlüsselwort `const` kann dem Compiler angezeigt werden. dass keine Veränderung an den Daten vorgenommen wird. Sie müssen also nicht gespeichert werden.

```
bool operator>(const Rectangle& a, const Rectangle& b){
    if (a.area() > b.area()) return 1;
    else return 0;
}
```

### Anwendung

Im folgenden Beispiel wird der Vergleichsoperator `==` überladen. Dabei sehen
wir den Abgleich des Namens und des Alters als ausreichend an.

```cpp                     Comparison.cpp
#include <iostream>

class Student{
  public:
    std::string name;
    int alter;
    std::string ort;

    Student(const Student&);
    Student(std::string n);
    Student(std::string n, int a, std::string o);

    void ausgabeMethode(std::ostream& os); // Deklaration der Methode

    bool operator==(const Student&);
};

Student::Student(const Student& other){
  this->name = other.name;
  this->alter = other.alter;
  this->ort = other.ort;
}

Student::Student(std::string n): name{n}, alter{18}, ort{"Freiberg"}{}

Student::Student(std::string n, int a, std::string o): name{n}, alter{a}, ort{o} {}

void Student::ausgabeMethode(std::ostream& os){
    os << name << " " << ort << " " << alter << "\n";
}

bool Student::operator==(const Student& other){
  if ((this->name == other.name) && (this->alter == other.alter)){
    return true;
  }else{
    return false;
  }
}

int main()
{
  Student gustav = Student("Zeuner", 27, "Chemnitz");
  gustav.ausgabeMethode(std::cout);

  Student bernhard {"Cotta", 18, "Zillbach"};
  bernhard.ausgabeMethode(std::cout);

  Student NochMalBernhard = Student(bernhard);
  NochMalBernhard.ausgabeMethode(std::cout);

  if (bernhard == NochMalBernhard){
    std::cout << "Identische Studenten \n";
  }else{
    std::cout << "Ungleiche Identitäten \n";
  }
}
```

Eine besondere Form der Operatorüberladung ist der `<<`, mit dem die Ausgabe auf ein Streamobjekt realsiert werden kann.

```cpp                     streamOperator.cpp
#include <iostream>

class Student{
  public:
    std::string name;
    int alter;
    std::string ort;

    Student(const Student&);
    Student(std::string n);
    Student(std::string n, int a, std::string o);

    void ausgabeMethode(std::ostream& os); // Deklaration der Methode

    bool operator==(const Student&);
};

Student::Student(std::string n, int a, std::string o): name{n}, alter{a}, ort{o} {}

std::ostream& operator<<(std::ostream& os, const Student& student)
{
    os << student.name << '/' << student.alter << '/' << student.ort;
    return os;
}

int main()
{
  Student gustav = Student("Zeuner", 27, "Chemnitz");
  Student bernhard = Student( "Cotta", 18, "Zillbach");
  std::cout << gustav;
}
```

## Vererbung

### Motivation

> **Merke: ** Eine unserer Hauptmotivationen bei der "ordentlichen" Entwicklung von Code ist die Vermeidung von Codedopplungen!

In unserem Code entstehen Dopplungen, weil bestimmte Variablen oder Memberfunktionen usw. mehrfach für individuelle Klassen Implementiert werden. Dies wäre für viele Szenarien analog der Fall:

| Basisklasse | abgeleitete Klassen                 | Gemeinsamkeiten                                                  |
| ----------- | ----------------------------------- | ---------------------------------------------------------------- |
| Fahrzeug    | Flugzeug, Boot, Automobil           | Position, Geschwindigkeit, Zulassungsnummer, Führerscheinpflicht |
| Datei       | Foto, Textdokument, Datenbankauszug | Dateiname, Dateigröße, Speicherort                               |
| Nachricht   | Email, SMS, Chatmessage             | Adressat, Inhalt, Datum der Versendung                           |

> **Merke:**  Die _Vererbung_ ermöglicht die Erstellung neuer Klassen, die ein in existierenden Klassen definiertes Verhalten wieder verwenden, erweitern und ändern. Die Klasse, deren Member vererbt werden, wird Basisklasse genannt, die erbende Klasse als abgeleitete Klasse bezeichnet.

### Implementierung in C++

In C++ werden Vererbungsmechanismen folgendermaßen abgebildet:

Die generellere Klasse `Fahrzeug` liefert einen Bauplan für die spezifischeren, die die Vorgaben weiter ergänzen. Folglich müssen wir uns die Frage stellen, welche Daten oder Funktionalität übergreifend abgebildet werden soll und welche individuell realisiert werden sollen.

Dabei können ganze Ketten von Vererbungen entstehen, wenn aus einem sehr allgemeinen Objekt über mehrere Stufen ein spezifischeres Set von Membern umgesetzt wird.

```cpp  
class Fahrzeug{
};

class Automobil: public Fahrzeug{
};

class Hybrid: public Automobil{
};
```

Ein weiteres Beispiel greift den Klassiker der Einführung objektorientierter Programmierung auf, den Kanon der Haustiere :-)
Das Beispiel zeigt die Initialisierung der Membervariablen :

+ der Basisklasse beim Aufruf des Konstruktors der erbenden Klasse
+ der Member der erbenden Klasse wie gewohnt

```cpp                     Animals.cpp
#include <iostream>

class Animal {
public:
    Animal(): name{"Animal"}, weight{0.0} {};
    Animal(std::string _name, double _weight): name{_name}, weight{_weight} {};
    void sleep () {
      std::cout << name << " is sleeping!" << std::endl;
    }
    std::string name;
    double weight;
};

class Dog : public Animal {
public:
    Dog(std::string name, double weight, int id): Animal(name, weight), id{id} {};
    void bark() {
      std::cout << "woof woof" << std::endl;
    }
    double top_speed() {
      return (weight < 40) ? 15.5 : (weight < 90) ? 17.0 : 16.2;
    }
    int id;
};

int main(){
    Dog dog = Dog("Rufus", 50.0, 2342);
    dog.sleep();
    dog.bark();
    std::cout << dog.top_speed() << std::endl;
}
```
@LIA.eval(`["main.c"]`, `g++ -Wall main.c -o a.out`, `./a.out`)

### Vererbungsattribute

Die Zugriffsattribute `public` und `private` kennen Sie bereits. Damit können wir Elemente unserer Implementierung vor dem Zugriff von außen Schützen.

Wie wirkt sich das Ganze aber auf die Vererbung aus? Hierbei muss neben dem individuellen Zugriffsattribut auch der Status der Vererbung beachtet werden. Damit ergibt sich dann folgendes Bild:

```c++
class A
{
public:
    int x;
protected:
    int y;
private:
    int z;
};

class B : public A
{
    // x is public
    // y is protected
    // z is not accessible from B
};

class C : protected A
{
    // x is protected
    // y is protected
    // z is not accessible from C
};

class D : private A    // 'private' is default for classes
{
    // x is private
    // y is private
    // z is not accessible from D
};
```

Das Zugriffsattribut protected spielt nur bei der Vererbung eine Rolle. Innerhalb einer Klasse ist `protected` gleichbedeuted mit `private`. In der Basisklasse ist also ein Member geschützt und nicht von außen zugreifbar. Bei der Vererbung wird der Unterschied zwischen `private` und `protected` deutlich: Während `private` Member in erbenden Klassen nicht direkt verfügbar sind, kann auf die als `protected` deklariert zugegriffen werden.

Entsprechend muss man auch die Vererbungskonstellation berücksichtigen, wenn man festlegen möchte ob ein Member gar nicht (`private`), immer (`public`) oder nur im Vererbungsverlauf verfügbar sein (`protected`) soll.

```cpp                     Animals.cpp
#include <iostream>

class Animal {
public:
    Animal(): name{"Animal"}, weight{0.0} {};
    Animal(std::string _name, double _weight): name{_name}, weight{_weight} {};
    void sleep () {
      std::cout << name << " is sleeping!" << std::endl;
    }
    std::string name;
    double weight;
};

class Dog : public Animal {
public:
    Dog(std::string name, double weight, int id): Animal(name, weight), id{id} {};
    void bark() {
      std::cout << "woof woof" << std::endl;
    }
    double top_speed() {
      return (weight < 40) ? 15.5 : (weight < 90) ? 17.0 : 16.2;
    }
    int id;
};

int main(){
    Dog dog = Dog("Rufus", 50.0, 2342);
    dog.sleep();
    dog.bark();
    std::cout << dog.top_speed() << std::endl;
}
```
@LIA.eval(`["main.c"]`, `g++ -Wall main.c -o a.out`, `./a.out`)

********************************************************************************

### Überschreiben von Methoden

Die grundsätzlicher Idee bezieht sich auf die Implementierung "eigener" Methoden gleicher Signatur in den abgeleiteten Klassen. Diese implementieren dann das spezifische Verhalten der jeweiligen Objekte.

```cpp                     Inheritance.cpp
#include <iostream>

class Person{
  public:
    std::string name;
    std::string ort;

    Person(std::string n, std::string o): name{n}, ort{o} {};
    void printData(std::ostream& os){
        os << "Datensatz: "  << name << " " << ort << "\n";
    }
};

class Student : public Person{
  public:
    Student(std::string n, std::string o, std::string sg): Person(n, o), studiengang{sg}{};
    std::string studiengang;
    void printCertificate(std::ostream& os){
          os << "Studentendatensatz: "  << name << " " << ort << " " << studiengang << "\n";
    }
};

class Professor : public Person{
  public:
    Professor(std::string n, std::string o, int id): Person(n, o), id{id}{};
    int id;
};

int main()
{
  Student gustav = Student("Zeuner", "Chemnitz", "Mathematik");
  gustav.printData(std::cout);

  Professor winkler = Professor("Winkler", "Freiberg", 234234);
  winkler.printData(std::cout);
}
```
@LIA.eval(`["main.c"]`, `g++ -Wall main.c -o a.out`, `./a.out`)

Die Polymorphie (griechisch "Vielgestaltigkeit") der objektorientierten Programmierung ist eine Eigenschaft, die in Zusammenhang mit Vererbung einhergeht. Eine Methode ist genau dann polymorph, wenn sie von verschiedenen Klassen unterschiedlich genutzt wird. Wenn Sie mehr darüber wissen wollen, sind Sie herzlich zur Vorlesung Softwareentwicklung im Sommersemester eingeladen!

Dabei untersuchen wir unter anderem Konzepte, wie wir die erbenden Methoden zwingen können ein bestimmte Methode zu implementieren. Mit der Notation `virtual void printData(std::ostream& os) = 0;` wird aus unserer Implementierung eine abstrakte Methode, die in jedem Fall in den erbenden Klassen implementiert sein muss.  
