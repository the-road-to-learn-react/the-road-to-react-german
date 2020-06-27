## Komponentendefinition in React (fortgeschrittene Anleitung)

Die Überarbeitungen in diesem Kapitel sind optionale Empfehlungen von mir. Erstelle deine Anwendung ohne die erweiterten Muster, wenn du dies bevorzugst. Lasse dich nicht entmutigen, wenn das Kapitel dir zunächst zu kompliziert erscheint. Beim Durcharbeiten legst du den Samen. Manchmal reicht es aus, eine Nacht darüber zu schlafen und alles ist klar. 

Alle Komponenten in der Datei *src/App.js* sind Funktionskomponenten. JavaScript bietet dir mehrere Möglichkeiten, Funktionen zu deklarieren. Bisher haben wir Funktionsanweisungen genutzt, obwohl Pfeilfunktionen präziser und knapper sind. Das folgende Beispiel macht dies nicht vollständig klar. Dafür zeigt es dir die unterschiedliche Syntax:

{title="Code Playground",lang="javascript"}
~~~~~~~
// Funktionsdeklaration
function () { ... }

// Pfeilfunktionsdeklaration
const () => { ... }
~~~~~~~

Entferne die Klammern in einem Pfeilfunktionsausdruck, wenn dieser nur ein Argument enthält --- für mehrere sind sie erforderlich:

{title="Code Playground",lang="javascript"}
~~~~~~~
// erlaubt
const item => { ... }

// erlaubt
const (item) => { ... }

// nicht erlaubt
const item, index => { ... }

// erlaubt
const (item, index) => { ... }
~~~~~~~

Das Definieren der Funktionskomponenten mithilfe von Pfeilfunktionen macht diese übersichtlicher:

{title="src/App.js",lang="javascript"}
~~~~~~~
# leanpub-start-insert
const App = () => {
# leanpub-end-insert
  return (
    <div>
      ...
    </div>
  );
# leanpub-start-insert
};
# leanpub-end-insert

# leanpub-start-insert
const List = () => {
# leanpub-end-insert
  return list.map(function(item) {
    return (
      <div key={item.objectID}>
        ...
      </div>
    );
  });
# leanpub-start-insert
};
# leanpub-end-insert
~~~~~~~

Dies gilt ebenfalls für andere Funktionen, wie die, die wir in der Map-Methode unseres JavaScript-Arrays verwendet haben:

{title="src/App.js",lang="javascript"}
~~~~~~~
const List = () => {
# leanpub-start-insert
  return list.map(item => {
# leanpub-end-insert
    return (
      <div key={item.objectID}>
        <span>
          <a href={item.url}>{item.title}</a>
        </span>
        <span>{item.author}</span>
        <span>{item.num_comments}</span>
        <span>{item.points}</span>
      </div>
    );
  });
};
~~~~~~~

Wenn eine Pfeilfunktion *nichts* ausführt, sondern nur *etwas* zurückgibt --- mit anderen Worten, wenn sie keine Aufgabe erledigt stattdessen Informationen wiedergibt ---, ist der Blockkörper **(geschweifte Klammern)** nicht notwendig. Entferne diesen in dem Falle. Und: In einem **knappen oder prägnanten Körper** ist die **Rückgabeanweisung implizit**, sodass du *return* ebenfalls außen vor lässt:

{title="Code Playground",lang="javascript"}
~~~~~~~
// Blockkörper
count => {
  // perform any task in between

  return count + 1;
}

// Präziser und knapper Körper
count =>
  count + 1;
~~~~~~~

Gleiches ist auf die App- und List-Komponente anwendbar, da beide nur JSX zurückgeben und keine Logikaufgabe bearbeiten. Dies gilt ebenso für die Pfeilfunktion, die in der Map-Funktion verwendet wird:

{title="src/App.js",lang="javascript"}
~~~~~~~
# leanpub-start-insert
const App = () => (
# leanpub-end-insert
  <div>
    ...
  </div>
# leanpub-start-insert
);
# leanpub-end-insert

# leanpub-start-insert
const List = () =>
  list.map(item => (
# leanpub-end-insert
    <div key={item.objectID}>
      <span>
        <a href={item.url}>{item.title}</a>
      </span>
      <span>{item.author}</span>
      <span>{item.num_comments}</span>
      <span>{item.points}</span>
    </div>
# leanpub-start-insert
  ));
# leanpub-end-insert
~~~~~~~

Unser JSX ist jetzt knapper und prägnanter, da die Funktionsanweisung, die geschweiften Klammern und die return-Anweisung weggelassen werden. Denke daran, dass dies ein optionaler Schritt ist. Es natürlich in React möglich, normale Funktionen anstelle von Pfeilfunktionen zu verwenden und Körper mit geschweiften Klammern um implizite Rückgaben zu blockieren. Manchmal sind Blockkörper erforderlich, weil mehr Logik zwischen Funktionssignatur und return-Anweisung notwendig ist:

{title="Code Playground",lang="javascript"}
~~~~~~~
const App = () => {
  // Führe eine beliebige Aufgabe aus

  return (
    <div>
      ...
    </div>
  );
};
~~~~~~~

Obwohl die Pfeilfunktionen in React nicht zwingend sind: Stelle sicher, dass du dieses Refactoring-Konzept verstehst, da wir im Buch oft zwischen Funktionsdeklarationen und Pfeilfunktionsdeklarationen mit und ohne Blockkörper wechseln. Welche Variante wir verwenden, hängt von den Anforderungen der Komponente ab.

### Übungen:

* Begutachte den [Quellcode dieses Abschnittes](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/React-Component-Definition).
  * Reflektiere die [Änderungen gegenüber dem Kapitel "Mach dich mit einer anderen React-Komponente bekannt"](https://github.com/the-road-to-learn-react/hacker-stories/compare/hs/Meet-another-React-Component...hs/React-Component-Definition?expand=1).
* Lese mehr zu [Pfeilfunktionen in JavaScript](https://developer.mozilla.org/de/docs/Web/JavaScript/Reference/Functions/Arrow_functions).
* Vertiefe Pfeilfunktionen mit Blockkörpern inklusive Return-Anweisung und mit knappem präzisem Körper ohne Return-Anweisung.
