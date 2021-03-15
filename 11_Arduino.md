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
        https://github.com/LiaTemplates/Pyodide

-->

# Softwareentwicklung mit Arduino

Die interaktive Version des Kurses ist unter diesem [Link](https://liascript.github.io/course/?https://raw.githubusercontent.com/SebastianZug/VL_ProzeduraleProgrammierung/master/11_Arduino.md#1) zu finden.

**Wie weit waren wir gekommen?**

* Grundlagen der Programmierung mit C und C++
* Verständnis für den Aufbau und die Modellierung von Klassen  

**Inhalt der heutigen Veranstaltung**

* Ergänzende Anmerkungen zu C++
* Übertragung der Inhalte auf die Programmierung des Mikrocontrollers
* Grundlegende Abläufe bei der Programmierung und Nutzung der Sensordaten

**Fragen an die heutige Veranstaltung ...**

* Unterscheiden Sie zwischen der "erstmaligen Belegung" mittels Initialisierungsliste und Zuweisungen im Konstruktor!
* In welchem Speicherbereich werden die dynamisch erzeugten Objekte angelegt?
* Was unterscheidet die Verwendung von Variablen auf dem Stack und dem Heap?
* Warum braucht man ohnehin dynamische Objekte?

## Ergänzende Anmerkungen zur C++ Objekten

An dieser Stelle wollen wir zwei Aspekte vertiefen, um darauf aufbauend die Brücke zu den eingebetteten Systemen zu schlagen:

+ Nutzung des Konstruktors für die Belegung der Membervariablen (Wiederholung)
+ Verwendung von dynamisch angefordertem Speicher

### Konstruktor

Die Initialisierung mit Initialisierungliste ist effizienter als per Zuweisungen im Anweisungsblock.
Bei der Realisierung im Anweisungsblock sind Variablen bereits initialisiert und bekommen nur andere Werte zugewiesen.

> **Merke:** Konstanten können ausschließlich in der Initialisierungsliste gesetzt werden.

Grundsätzlich ist eine Initialisierung mittels Initialisierungsliste vorzuziehen.
Zu beachten ist bei der abhängigen Initialisierung, dass die Initialisierung in der Reihenfolge abläuft, in
der die Membervariablen in der Klasse deklariert sind, und nicht in der Reihenfolge
wie man sie in der Initialisierungsliste schreibt.

**Initialisierungsliste in einer abgeleiteten Klasse**

Mittels der Initialisierungsliste lässt sich ein Konstruktor der Basisklasse aufrufen.
Ohne Angabe des bestimmen Konstruktor der Basisklasse wird implizit Standardkonstruktor
aufgerufen.

```cpp                     Constructor.cpp
#include <iostream>
class Droide
{
    public:
        Droide(){std::cout<<"Standard-Droide"<<std::endl;}
        Droide(int id){std::cout<<"ID-Droide"<<std::endl;}
};

class Astronavigation
{
  public:
    Astronavigation(){std::cout<<"Astronavigation"<<std::endl;}
};

class Astromechdroide : Droide
{
  private:
    Astronavigation astronavigation;
  public:
      Astromechdroide()
      //explizite Verwendung von Droide() und Astronavigation()
      {}
      Astromechdroide(int id): Droide(id), astronavigation()
      // explizite Verwendung von Droide(int)
      // mit astronavigation() wird Std-Konstruktor der Klasse Astronavigation aufgerufen
      {}
};

int main()
{
  Astromechdroide amd;
}
```
@LIA.eval(`["main.c"]`, `g++ -Wall main.c -o a.out`, `./a.out`)

### Dynamisch erzeugte Objekte

Jede Variable bzw. jedes Objekt bekommt im Speicher eine Adresse zugewiesen.
Die Zugehörigkeit einer Variable einem Speicherbereich ist entscheidend für ihre Gültigkeit.
Die Entscheidung darüber trifft der Programmierer in dem er im Quellcode die Variable am
bestimmten Ort bzw. mit dem bestimmen Schlüsselwort definiert.

+ Wenn eine Variable innerhalb einer Funktion (Methode) definiert wird, ist sie nur ab dem Zeitpunkt der Definition bis zum Ende der Funktion gültig:

+ Die Klassenmembers sind innerhalb des gesamten Klassenbereich gültig und ihre Lebensdauer entspricht der Lebensdauer eines Objektes, in dem sie enthalten sind.

Die Ausnahme bilden
die static-Member. Sie sind innerhalb aller Objekte der Klasse gültig (und nur einmal vorhanden).

Globale Variablen werden außerhalb jeglicher Funktion definiert. Jede Funktion kann darauf
zugreifen und deren Inhalt verändern. Sie existieren bis zum Programmende.

Dynamisch erzeugte Variablen (Objekte) sind ebenfalls über die Grenzen einer Funktion
verfügbar. Sie werden mit  `new`-Operator angelegt und mit `delete` wieder gelöscht,
spätestens am Programmende.

Der Vorteil von dynamisch erzeugten Variablen besteht in der effizienten Speichernutzung.
Neben der Möglichkeit eines rechtzeitigen Löschens kann EIN
mit `new`-Operator angelegtes Objekt in mehreren Objekten einer anderen Klasse enthalten sein.

```cpp
class Droide
{
  public:
    Droide(){}

};

class Sternjaeger{
  private:
    Droide *droide;
  public:
    Sternjaeger(Droide *_droide):droide(_droide){}
};

int main()
{
  Droide *d=new Droide();
  Sternjaeger jaeger1(d), jaeger2(d);
  //beide Sternjaeger teilen sich einen Droiden
  //darum wird er nur einmal geloescht
  //und zwar hier, nicht in der Klasse Sternjaeger
  delete d;
}
```

Wenn jeder Sternjaeger über einen eigenen Droide verfügen soll:

```cpp
#include <iostream>
class Droide
{
  public:
    Droide(){}

};

class Sternjaeger{
  private:
    Droide *droide;
  public:
    Sternjaeger():droide(NULL)
    {
      droide=new Droide();
    }
    ~Sternjaeger(){ delete droide;}
};

int main()
{
  Sternjaeger jaeger1, jaeger2;
}
```
@LIA.eval(`["main.c"]`, `g++ -Wall main.c -o a.out`, `./a.out`)

Dynamische Speicherverwaltung bedeutet aber auch einen zusätzlichen Programmieraufwand und
eine Fehlerquelle. Dynamisch erzeugte Variablen können versehentlich überschrieben werden und dadurch
nicht mehr freigegeben werden (Speicherleck) oder es können Zeiger ohne Speicherbereich
entstehen (hängende Zeiger).

## Arduino Konzept

Das Arduino-Projekt wurde am _Interaction Design Institute Ivrea_ (IDII) in Ivrea, Italien, ins Leben gerufen. Ausgangspunkt war die Suche nach einem preiswerten, einfach zu handhabenen Mikrocontroller der insbesondere für die Ausbildung von Nicht-Informatikern geeignet ist. Das anfängliche Arduino-Kernteam bestand aus Massimo Banzi, David Cuartielles, Tom Igoe, Gianluca Martino und David Mellis.

Nach der Fertigstellung der Plattform wurden leichtere und preiswertere Versionen in der Open-Source-Community verbreitet. Mitte 2011 wurde geschätzt, dass über 300.000 offizielle Arduinos kommerziell produziert worden waren, zwischenzeitlich wurden mehrere Millionen produziert.

### Hardware

Das Arduino Projekt hat eine Vielzahl von unterschiedlichen Boards hervorgebracht, die eine unterschiedliche Leistungsfähigkeit und Ausstattung kennzeichnen.
Das Spektrum reicht von einfachen 8-Bit Controllern bis hin zu leistungsstarken ARM Controllern, die ein eingebettetes Linux ausführen.

> **Merke:** Es gibt nicht **den** Arduino Controller, sondern eine Vielzahl von verschiedenen Boards.


Unser Controller, ein 32 Bit System, auf den im nachfolgenden eingegangen wird, liegt im mittleren Segment der Leistungsfähigkeit.

Erweitert werden die Boards durch zusätzliche `Shields`, die den Funktionsumfang erweitern.

### Software

Die Entwicklungsumgebung fasst grundsätzliche Entwicklungswerkzeuge zusammen und richtet sich an Einsteiger.

Wichtige Grundeinstellungen:

+ Richtigen Port für den Programmiervorgang auswählen (`Tools` -> `Port`)
+ Richtigen Controller auswählen (`Tools` -> `Board`)
+ Richtige Baudrate für die Serielle Schnittstellen

Eine Beschreibung der Konfiguration für den spezifischen Controller folgt in den Übungen.

#### Grundaufbau eines Programmes

Jedes Arduino-Programm umfasst zwei zentrale Funktionen - `setup` und `loop`. Erstgenannte wird einmalig ausgeführt und dient der Konfiguration, die zweite wird kontinuierlich umgesetzt.

<div>
  <wokwi-led color="red" pin="13" port="B" label="13"></wokwi-led>
  <span id="simulation-time"></span>
</div>

```cpp       HelloWorld.cpp
# define LED_PIN 13                 // Pin number attached to LED.
//const int led_pin_red = 13;

void setup() {
    pinMode(LED_PIN, OUTPUT);       // Configure pin 13 to be a digital output.
}

void loop() {
    digitalWrite(LED_PIN, HIGH);    // Turn on the LED.
    delay(1000);                    // Wait 1 second (1000 milliseconds).
    digitalWrite(LED_PIN, LOW);     // Turn off the LED.
    delay(1000);                    // Wait 1 second.
}
```
@AVR8js.sketch

| Befehl                       | Bedeutung                                                                      |
| ---------------------------- | ------------------------------------------------------------------------------ |
| `pinMode(pin_id, direction)` | Festlegung der Konfiguration eines Pins als Input / Output (`INPUT`, `OUTPUT`) |
| `digitalWrite(pin_id, state)`| Schreiben eines Pins, daraufhin liegen entweder (ungefähr) 3.3 V `HIGH` oder 0V `LOW` an                                                |

> **Aufgabe:** Schreiben Sie ein Programm, dass ein Lauflicht aus LEDs realisiert.


| Befehl                       | Bedeutung                                                                      |
| ---------------------------- | ------------------------------------------------------------------------------ |
| `state = digitalRead(pin_id)`| Lesen des Pegels an einem Pin - 3.3 V `HIGH` oder 0V `LOW` an                                                |

Im Beispiel ist der grüne Button an Pin 3, der rote an Pin 2 angeschlossen.

<div>
  <wokwi-pushbutton color="green"   pin="3"  port="D"></wokwi-pushbutton>
  <wokwi-pushbutton color="red"   pin="2"  port="D"></wokwi-pushbutton>
  <wokwi-led color="red"   pin="8" port="B" label="8"></wokwi-led>
</div>

```cpp       ButtonLogic.cpp

void setup() {
  Serial.begin(115200);
  pinMode(2, INPUT);
  pinMode(3, INPUT);
  pinMode(4, INPUT);
  pinMode(13, OUTPUT);
}

void loop() {
  int button = digitalRead(3);

  if (button) {
    digitalWrite(13, HIGH);
  }
  delay(250);
}
```
@AVR8js.sketch

> **Aufgabe:** Passen Sie das Programm nun so an, dass die Led nach dem Drücken des roten Buttons für 3 Sekunden und nach dem Drücken des grünen Buttons für 1 Sekunde leuchtet.

Eine Allgemeine Übersicht zu den Arduinobefehlen finden Sie unter folgendem [Link](https://www.arduino.cc/reference/en/).

#### Ablauf der Programmiervorganges

Der Programmiervorgang für einen Mikrocontroller unterscheidet sich in einem Punkt signifikant von Ihren bisherigen C/C++ Aufgaben - die erstellten Programme sind auf dem Entwicklungssystem nicht ausführbar. Wir tauschen also den Compiler mit der Hardware aus. Dadurch "versteht" der Entwicklungsrechner die Anweisungen aber auch nicht.

Dabei zeigt sich aber auch der Vorteil der Hochsprachen C und C++, die grundsätzlichen Sprachinhalte sind prozessorunabhängig!

## "Unser" Controller

| Kategorie  | Features laut Webseite                  | Bedeutung                                                |
| ---------- | --------------------------------------- | -------------------------------------------------------- |
| Controller | _EMW3166 Wifi module_                   | Eingebetteter Controller mit WLAN Schnittstelle          |
| Peripherie | _Audio codec chip_                      | Hardware Audioverarbeitungseinheit                       |
|            | _Security encryption chip_              | Verschlüsselungsfeatures in einem separaten Controller   |
|            | _2 user button_                         |                                                          |
| Aktoren    | _1 RGB light_                           |                                                          |
|            | _3 working status indicator_            | _WiFi_, _Azure_, _User_ Leds auf der rechten Seite       |
|            | _Infrared emitter_                      | Infrarot Sender für die Nutzung als Fernbedienung        |
|            | _OLED,128×64_                           | Display mit einer Auflösung von 128 x 64 Pixeln          |
| Sensoren   | _Motion sensor_                         | Interialmessystem aus Beschleunigungssensor und Gyroskop |
|            | _Magnetometer sensor_                   |                                                          |
|            | _Atmospheric pressure sensor_           |                                                          |
|            | _Temperature and humidity sensor_       |                                                          |
|            | _Microphone_                            |                                                          |
| Anschlüsse | _Connecting finger extension interface_ |                                                          |


Worin unterscheidet sich der Controller von einem willkürlich ausgewählten Laptop?

| Parameter        | AZ3166                            | Laptop                           |
| ---------------- | --------------------------------- | -------------------------------- |
| Bandbreite       | 32Bit                             | 64Bit                            |
| Taktrate         | 100Mhz                            | 3.5Ghz                           |
| Arbeitsspeicher  | 256kBytes                         | 8 GBytes                         |
| Programmspeicher | 1 MByte                           |                                  |
| Energieaufnahme  | 0.x Watt                          | 100 Watt                         |
| Kosten           | $40$ Euro                           | $>500$ Euro                        |
| Einsatz          | Spezifisches eingebettetes System | Vielfältige Einsatzmöglichkeiten |

Zum Vergleich, eine Schreibmaschinenseite umfasst etwa 2KBytes.

Die Dokumentation der zugehörige _Application Programming Interface_ (API) finden Sie unter [Link](https://microsoft.github.io/azure-iot-developer-kit/docs/apis/led/).
Hier stehen Klassen bereit, die Einbettung der Sensoren, LEDs, des Displays kapseln und die Verwendung vereinfachen.
