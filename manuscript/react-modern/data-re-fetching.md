## Nochmaliges Abrufen von Daten in React

Bisher ruft die App-Komponente beim ersten Aufruf eine initiale Liste über das hartcodierte Suchwort `react` ab. Danach ist es möglich, dass Benutzer clientseitig die Suchfunktion nutzen. Jetzt verschieben wir die Suche vom Client zum Server. Dabei verwenden wir `searchTerm` als dynamisches Suchwort.

Tausche zuerst `searchedStories` gegen `stories.data`, da wir ohnehin die Suchergebnisse von der API abfragen:

{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
  ...

  return (
    <div>
      ...

      {stories.isLoading ? (
        <p>Loading ...</p>
      ) : (
# leanpub-start-insert
        <List list={stories.data} onRemoveItem={handleRemoveStory} />
# leanpub-end-insert
      )}
    </div>
  );
};
~~~~~~~

Im zweiten Schritt stellst du sicher, dass anstelle des hartcodierten Suchwortes ein dynamisches Wort für die Anfrage verwendet wird: `searchTerm` aus dem Status der App-Komponente. Wenn `searchTerm` eine leere Zeichenfolge ist, passiert nichts:

{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
  ...

  React.useEffect(() => {
# leanpub-start-insert
    if (searchTerm === '') return;
# leanpub-end-insert

    dispatchStories({ type: 'STORIES_FETCH_INIT' });

# leanpub-start-insert
    fetch(`${API_ENDPOINT}${searchTerm}`)
# leanpub-end-insert
      .then(response => response.json())
      .then(result => {
        dispatchStories({
          type: 'STORIES_FETCH_SUCCESS',
          payload: result.hits,
        });
      })
      .catch(() =>
        dispatchStories({ type: 'STORIES_FETCH_FAILURE' })
      );
  }, []);

  ...
};
~~~~~~~

Die Suche berücksichtigt jetzt den Suchbegriff für den Status der Liste von Anfang an. Daher implementieren wir das erneute Abrufen von Daten, indem wir `searchTerm` in den bisher leeren Array im zweiten Argument der Funktion `useEffect` einfügen. Wenn sich `searchTerm` ändert, wird somit der Seiten-Effekt aufgerufen. Außerdem verallgemeinern wir den Umgang mit einem problematischen Suchwort: Wenn `searchTerm` nicht vorhanden ist (null, leere Zeichenfolge, undefiniert), passiert nichts:

{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
  ...

  React.useEffect(() => {
# leanpub-start-insert
    if (!searchTerm) return;
# leanpub-end-insert

    dispatchStories({ type: 'STORIES_FETCH_INIT' });

    fetch(`${API_ENDPOINT}${searchTerm}`)
      .then(response => response.json())
      .then(result => {
        dispatchStories({
          type: 'STORIES_FETCH_SUCCESS',
          payload: result.hits,
        });
      })
      .catch(() =>
        dispatchStories({ type: 'STORIES_FETCH_FAILURE' })
      );
# leanpub-start-insert
  }, [searchTerm]);
# leanpub-end-insert

  ...
};
~~~~~~~

Wir haben die Suche von einer clientseitigen in eine serverseitige geändert. Anstatt eine vordefinierte Liste auf dem Client zu filtern, wird mit `searchTerm` eine serverseitig gefilterte abgerufen. Das anfängliche Abrufen von Daten geschieht serverseitig, genauso wie das Aktualisieren, wenn sich das `searchTerm` ändert. Die Funktion ist jetzt vollständig serverseitig implementiert.

Das erneute Abrufen von Daten bei jeder Eingabe in das Eingabefeld ist nicht optimal. Die Implementierung belastet die API unnötig. Fehler treten vermehrt auf, wenn die Anfragen verdichtet eingehen. Wir werden dieses Problem bald korrigieren.

### Übungen:

* Begutachte den [Quellcode dieses Abschnittes](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/Data-Re-Fetching-in-React).
  * Reflektiere die [Änderungen gegenüber dem letzten Abschnitt](https://github.com/the-road-to-learn-react/hacker-stories/compare/hs/Data-Fetching-with-React...hs/Data-Re-Fetching-in-React?expand=1).
  