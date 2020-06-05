## JSX (JavaScript XML)

Erinnerst du dich: Ich hatte erwähnt, dass die Ausgabe der App-Komponente der Syntax von HTML ähnelt. Diese "Abwandlung" heißt JSX und mischt JavaScript-Logik und HTML. In einem späteren Kapitel wirst du lernen, dass JSX ebenfalls CSS unterstützt. Wie JSX für die Anzeige der Variablen genutzt wird, zeigt dir das nachfolgende Beispiel:

{title="src/App.js",lang="javascript"}
~~~~~~~
import React from 'react';

const title = 'React';

function App() {
  return (
    <div>
# leanpub-start-insert
      <h1>Hello {title}</h1>
# leanpub-end-insert
    </div>
  );
}

export default App;
~~~~~~~

Rufe deine Anwendung mit `npm start` auf, falls diese nicht aktiv ist, und sieh dir die Anzeige im Browser an. Du siehst die gerendert Variable, die Ausgabe laute: "Hello React".

Richten wir die Aufmerksamkeit als Nächstes auf den HTML-Code, der in JSX fast gleich beschrieben wird. Ein Eingabefeld mit einem Label wird wie folgt definiert:

{title="src/App.js",lang="javascript"}
~~~~~~~
import React from 'react';

const title = 'React';

function App() {
  return (
    <div>
      <h1>Hello {title}</h1>

# leanpub-start-insert
      <label htmlFor="search">Search: </label>
      <input id="search" type="text" />
# leanpub-end-insert
    </div>
  );
}

export default App;
~~~~~~~

Du siehst drei HTML-Attribute: `htmlFor`, `id`, und `type`. `id`, `type` kennst du aus nativem HTML --- `htmlFor` ist gegebenenfalls neu für dich. `htmlFor` steht für das `for`-Attribut in HTML. Da `for` ein für JavaScript reserviertes Wort ist, verwenden React-Elemente stattdessen `htmlFor`. JSX ersetzt eine Handvoll interner HTML-Attribute, du findest alle [unterstützten HTML-Attribute](https://de.reactjs.org/docs/dom-elements.html#all-supported-html-attributes) in der React-Dokumentation, die [CamelCase](https://wiki.selfhtml.org/index.php?title=CamelCase&oldid=62021) als Namenskonvention verwendet. Je weiter du dich in React vorarbeitest, desto mehr JSX-spezifische Attribute wie `className` und `onClick` anstelle von `class` und `onclick` werden dir auf deinem Weg begegnen.

Wir werden das HTML-Eingabefeld später erneut ansehen und dabei Details zur Implementierung behandeln. Jetzt kehren wir zunächst zu JavaScript in JSX zurück. Wir haben im vorhergehenden Beispiel einen String in einer Konstante für die Ausgabe definiert, dasselbe erledigen wir im nächsten Schritt mit einem JavaScript-Objekt:

{title="src/App.js",lang="javascript"}
~~~~~~~
import React from 'react';

# leanpub-start-insert
const welcome = {
  greeting: 'Hey',
  title: 'React',
};
# leanpub-end-insert

function App() {
  return (
    <div>
      <h1>
# leanpub-start-insert
        {welcome.greeting} {welcome.title}
# leanpub-end-insert
      </h1>

      <label htmlFor="search">Search: </label>
      <input id="search" type="text" />
    </div>
  );
}

export default App;
~~~~~~~

Beachte: In JSX ist es ebenfalls möglich, geschweifte Klammern für JavaScript-Ausdrücke zu verwenden --- zum Beispiel für den Aufruf einer Funktion:

{title="src/App.js",lang="javascript"}
~~~~~~~
import React from 'react';

# leanpub-start-insert
function getTitle(title) {
  return title;
}
# leanpub-end-insert

function App() {
  return (
    <div>
# leanpub-start-insert
      <h1>Hello {getTitle('React')}</h1>
# leanpub-end-insert

      <label htmlFor="search">Search: </label>
      <input id="search" type="text" />
    </div>
  );
}

export default App;
~~~~~~~

JSX wurde ursprünglich für React entwickelt. Nach und nach haben andere die Vorzüge der Sprache erkannt. So wurde JSX später in verschiedenen modernen Bibliotheken und Frameworks integriert. JSX ist eines meiner Lieblings-Features, weil es ohne zusätzliche Syntax oder Template-Strings möglich ist, JavaScript in HTML zu verwenden. Einzig und allein geschweiften Klammern sind erforderlich.

### Übungen:

* Begutachte den [Quellcode dieses Abschnittes](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/React-JSX).
  * Bestätige die [Änderungen gegenüber dem letzten Abschnitt](https://github.com/the-road-to-learn-react/hacker-stories/compare/hs/Meet-the-React-Component...hs/React-JSX?expand=1).
* Lese mehr zum Thema [JSX](https://de.reactjs.org/docs/introducing-jsx.html).
* Definiere eigene Variablen und zeige diese mithilfe von JSX im Frontend deiner Anwendung an.
* Versuche, die Daten eines Arrays mithilfe von JSX im Frontend anzuzeigen. Sorge dich nicht, falls es nicht sofort klappt. Im nächsten Kapitel werden wir diese Fragestellung gemeinsam angehen.
