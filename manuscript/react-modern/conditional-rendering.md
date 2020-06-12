## Bedingte Darstellung in React

Das Arbeiten mit asynchronen Daten in React führt zu bedingten Zustände. Beispiel: Diese sind abrufbar oder `null` (es liegen keine vor). Da der Ausgangszustand in unserer Anwendung eine leere Liste ist und nicht `null`, betrifft der letzte Fall uns nicht. Wenn `null` möglich wäre, müssten wir dieses Problem in JSX lösen. Da es sich um `[]` handelt, filtern wir ein leeres Array in der Suchfunktion `filter()`. Übrig bleibt wie erwartet ein leeres Array. Dies führt dazu, dass in der Funktion `map()` der List-Komponente nichts angezeigt wird.

In einer realen Anwendung gibt es mehr als zwei bedingte Zustände bei asynchronen Daten. Zum Beispiel `schwebend oder pending`. In diesem Falle blendest du idealerweise einen Ladeindikator ein:

{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
  ...

  const [stories, setStories] = React.useState([]);
# leanpub-start-insert
  const [isLoading, setIsLoading] = React.useState(false);
# leanpub-end-insert

  React.useEffect(() => {
# leanpub-start-insert
    setIsLoading(true);
# leanpub-end-insert

    getAsyncStories().then(result => {
      setStories(result.data.stories);
# leanpub-start-insert
      setIsLoading(false);
# leanpub-end-insert
    });
  }, []);

  ...
};
~~~~~~~

Mithilfe des [ternären Operators ](https://developer.mozilla.org/de/docs/Web/JavaScript/Reference/Operators/Conditional_Operator) binden wir den Ladeindikator **bedingt** in JSX ein:

{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
  ...

  return (
    <div>
      ...

      <hr />

# leanpub-start-insert
      {isLoading ? (
        <p>Loading ...</p>
      ) : (
# leanpub-end-insert
        <List
          list={searchedStories}
          onRemoveItem={handleRemoveStory}
        />
# leanpub-start-insert
      )}
# leanpub-end-insert
    </div>
  );
};
~~~~~~~

Bei der Arbeit mit asynchronen Daten kommt es unter Umständen zu Datenübertragungsfehlern. In unserer simulierten Umgebung tritt dies nicht auf. In der Realität sind Fehler unvermeidbar. Insbesondere dann, wenn Daten von der API eines Drittanbieters abgerufen werden. Führe deshalb einen eigenen Status für die Fehlerbehandlung ein und fange Fehler im `catch()`-Block des Promis-Objektes ab:

{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
  ...

  const [stories, setStories] = React.useState([]);
  const [isLoading, setIsLoading] = React.useState(false);
# leanpub-start-insert
  const [isError, setIsError] = React.useState(false);
# leanpub-end-insert

  React.useEffect(() => {
    setIsLoading(true);

    getAsyncStories()
      .then(result => {
        setStories(result.data.stories);
        setIsLoading(false);
      })
# leanpub-start-insert
      .catch(() => setIsError(true));
# leanpub-end-insert
  }, []);

  ...
};
~~~~~~~

Gib den Benutzern ein Feedback, falls ein Fehler aufgetreten ist. Dieses Mal wird bedingt etwas oder nichts gerendert. Verwende anstelle eines ternären den logischen Operator `&&` als Kurzform:

{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
  ...

  return (
    <div>
      ...

      <hr />

# leanpub-start-insert
      {isError && <p>Something went wrong ...</p>}
# leanpub-end-insert

      {isLoading ? (
        <p>Loading ...</p>
      ) : (
        ...
      )}
    </div>
  );
};
~~~~~~~

In JavaScript wird `true && 'Hallo Welt'` immer als `'Hallo Welt'` ausgewertet. `false && 'Hallo Welt'` ist stets `false`. In React nutzen wir dieses Verhalten zu unserem Vorteil. Wenn die erste Bedingung erfüllt ist, wird der Ausdruck nach dem `&&`-Operator ausgegeben. Im Falle von `false` überspringt React den zweiten Teil.

Bedingtes Rendern ist nicht nur für asynchrone Daten sinnvoll. Das einfachste Beispiel ist eine boolesche Variable, die mit einer Schaltfläche umgeschaltet wird. Wenn diese mit `true` belegt ist, wird etwas gerendert. Im Falle von `false` bleibt die Anzeige leer.

Dieses Feature ist insbesondere deshalb leistungsfähig, weil es JSX bedingt rendert. Es handelt sich um ein weiteres Werkzeug, um die Benutzeroberfläche dynamisch zu gestalten. Dies ist für komplexere Steuerungsabläufe mit asynchronen Daten unerlässlich.

### Übungen:

* Begutachte den [Quellcode dieses Abschnittes](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/React-Conditional-Rendering).
  * Bestätige die [Änderungen gegenüber dem letzten Abschnitt](https://github.com/the-road-to-learn-react/hacker-stories/compare/hs/React-Asynchronous-Data...hs/React-Conditional-Rendering?expand=1).
* Lese mehr über die bedingte Darstellung in React([0](https://www.robinwieruch.de/conditional-rendering-react/), [1](https://de.reactjs.org/docs/conditional-rendering.html)).
