## Komponentendefinition in React (Fortgeschrittene Anleitung)

Die folgenden Überarbeitungen sind optionale Empfehlungen von mir. Erstelle deine Anwendung ohne diese erweiterten Muster, wenn du dies bevorzugst. Lasse dich nicht entmutigen, wenn das Kapitel dir zunächst zu kompliziert erscheint.

Alle Komponenten in der Datei *src/App.js* sind Funktionskomponenten. JavaScript bietet dir mehrere Möglichkeiten, Funktionen zu deklarieren. Bisher haben wir die Funktionsanweisung genutzt, obwohl Pfeilfunktionen präziser und knapper sind:

{title="Code Playground",lang="javascript"}
~~~~~~~
// function declaration
function () { ... }

// arrow function declaration
const () => { ... }
~~~~~~~

Entferne die Klammern in einem Pfeilfunktionsausdruck, wenn dieser nur ein Argument enthält --- für mehrere sind sie dagegen erforderlich:

{title="Code Playground",lang="javascript"}
~~~~~~~
// allowed
const item => { ... }

// allowed
const (item) => { ... }

// not allowed
const item, index => { ... }

// allowed
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

Wenn eine Pfeilfunktion *nichts* ausführt, sondern nur *etwas* zurückgibt --- mit anderen Worten, wenn sie keine Aufgabe erledigt und nur Informationen wiedergibt ---, ist der Blockkörper **(geschweifte Klammern)** nicht notwendig --- entferne diesen. In einem **knappen oder prägnanten Körper** ist die **Rückgabeanweisung implizit**, sodass du *return* ebenfalls nicht verwendest:

{title="Code Playground",lang="javascript"}
~~~~~~~
// with block body
count => {
  // perform any task in between

  return count + 1;
}

// with concise body
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

Unser JSX ist jetzt knapper und prägnanter, da die Funktionsanweisung, die geschweiften Klammern und die return-Anweisung weggelassen werden. Denke daran, dass dies ein optionaler Schritt ist. Es ist möglich, normale Funktionen anstelle von Pfeilfunktionen zu verwenden und Körper mit geschweiften Klammern um implizite Rückgaben zu blockieren. Manchmal sind Blockkörper erforderlich, weil mehr Logik zwischen Funktionssignatur und return-Anweisung notwendig ist:

{title="Code Playground",lang="javascript"}
~~~~~~~
const App = () => {
  // perform any task in between

  return (
    <div>
      ...
    </div>
  );
};
~~~~~~~

Stelle sicher, dass du dieses Refactoring-Konzept verstehst, da wir oft zwischen Pfeilfunktionskomponenten mit und ohne Blockkörper wechseln. Welche Variante wir verwenden, hängt von den Anforderungen der Komponente ab.

### Übungen:

* Begutachte den [Quellcode dieses Abschnittes](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/React-Component-Definition).
  * Bestätige die [Änderungen gegenüber dem letzten Abschnitt](https://github.com/the-road-to-learn-react/hacker-stories/compare/hs/Meet-another-React-Component...hs/React-Component-Definition?expand=1).
* Lese mehr zum Thema [JavaScript arrow functions](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions).
* Vertiefe Pfeilfunktionen mit Blockkörper inklusive Return und knappem Körper ohne Returnanweisung.
