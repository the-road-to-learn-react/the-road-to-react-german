## Statusmanagement in React

Reacts Eigenschaften (Props) werden verwendet, um Informationen im Komponentenbaum weiterzugeben. Der **React Status (State)** dient dazu, eine Interaktion zu ermöglichen. Zum Beispiel verändern wir die Anzeige im Browser, indem wir mit der Anwendung interagieren.

Zu Beginn ist es wichtig, dass du die Funktion `useState` kennst. Wir übernehmen diese aus `React` zum Verwalten des Status. Die Funktion `useState` wird als Hook bezeichnet. Es gibt mehr als einen **React Hook** und du wirst in den nächsten Abschnitten mehr darüber erfahren. Konzentrieren wir uns vorerst auf **React's `useState` Hook**:

{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
  const stories = [ ... ];

# leanpub-start-insert
  const [searchTerm, setSearchTerm] = React.useState('');
# leanpub-end-insert

  ...
};
~~~~~~~

Der `useState`-Hook in React verwendet einen *Anfangszustand* als Argument und die Funktion gibt ein Array mit zwei Werten zurück. Wir verwenden als Anfangszustand eine leere Zeichenfolge. Der erste Wert (`searchTerm`) steht für den *aktuellen Zustand*; Der zweite Wert ist eine *Funktion zum Aktualisieren dieses Status* (`setSearchTerm`). Ich werde die Funktion *Statusaktualisierungsfunktion* nennen.

Lies bitte den Text [Destrukturierung von JavaScript-Arrays](https://developer.mozilla.org/de/docs/Web/JavaScript/Reference/Operators/Destrukturierende_Zuweisung)  wenn du mit der Syntax der beiden Werte aus dem zurückgegebenen Array nicht vertraut bist. Destrukturierung  wird verwendet, um einem Array gezielter auszulesen. Das nächste Beispiel verdeutlicht die Vorteile der  Array-Destrukturierung:

{title="Code Playground",lang="javascript"}
~~~~~~~
// basic array definition
const list = ['a', 'b'];

// no array destructuring
const itemOne = list[0];
const itemTwo = list[1];

// array destructuring
const [firstItem, secondItem] = list;
~~~~~~~

Der React `useState`-Hook ist eine Funktion, die ein Array zurückgibt. Vergleiche ihn mit dem folgenden JavaScript-Beispiel:

{title="Code Playground",lang="javascript"}
~~~~~~~
function getAlphabet() {
  return ['a', 'b'];
}

// no array destructuring
const itemOne = getAlphabet()[0];
const itemTwo = getAlphabet()[1];

// array destructuring
const [firstItem, secondItem] = getAlphabet();
~~~~~~~

Die Array-Destrukturierung ist nichts anderes als eine Kurzversion, mit der auf mehrere Elemente Nacheinander zugegriffen wird. Wenn Sie das Gleiche ohne die Array-Destrukturierung in React ausdrücken, wird es weniger lesbar:

{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
  const stories = [ ... ];

  // less readable version without array destructuring
  const searchTermState = React.useState('');
  const searchTerm = searchTermState[0];
  const setSearchTerm = searchTermState[1];

  ...
};
~~~~~~~

Das React-Team entschied sich für die Array-Destrukturierung aufgrund seiner präzisen Syntax und der Fähigkeit, destrukturierte Variablen zu benennen. Das folgende Codefragment ist ein weiteres Beispiel für die Array-Destrukturierung:

{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
  const stories = [ ... ];

# leanpub-start-insert
  const [searchTerm, setSearchTerm] = React.useState('');
# leanpub-end-insert

  ...
};
~~~~~~~

Nachdem wir den Status initialisiert haben und Zugriff auf den aktuellen Status und die Statusaktualisierungsfunktion haben, verwenden wir diese, um den aktuellen Status anzuzeigen und im Ereignis-Handler der App-Komponente zu aktualisieren:

{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
  const stories = [ ... ];

  const [searchTerm, setSearchTerm] = React.useState('');

  const handleChange = event => {
# leanpub-start-insert
    setSearchTerm(event.target.value);
# leanpub-end-insert
  };

  return (
    <div>
      <h1>My Hacker Stories</h1>

      <label htmlFor="search">Search: </label>
      <input id="search" type="text" onChange={handleChange} />

# leanpub-start-insert
      <p>
        Searching for <strong>{searchTerm}</strong>.
      </p>
# leanpub-end-insert

      <hr />

      <List list={stories} />
    </div>
  );
};
~~~~~~~

Wenn der Benutzer eine Eingabe im Eingabefeld tätigt, wird dessen Änderungsereignis vom Handler mit dem aktuellen Wert erfasst. Der Handlers legt dann mithilfe der Statusaktualisierungsfunktion den neuen Status fest. Daraufhin wird die Komponente nochmals gerendert, was bedeutet, dass die Komponentenfunktion erneut ausgeführt wird. Der neue Status tritt anstelle des aktuellen und wird im JSX der Komponente angezeigt.

### Übungen:

* Begutachte den [Quellcode dieses Abschnittes](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/React-State).
  * Bestätige die [Änderungen gegenüber dem letzten Abschnitt](https://github.com/the-road-to-learn-react/hacker-stories/compare/hs/React-Props...hs/React-State?expand=1).
* Lese mehr zur [Destrukturierung von JavaScript-Arrays](https://developer.mozilla.org/de/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment#Array_destructuring).
* Lese mehr zum Thema React's useState Hook ([0](https://www.robinwieruch.de/react-usestate-hook), [1](https://reactjs.org/docs/hooks-state.html)), da dieser deine React-Komponente interaktiv macht.
