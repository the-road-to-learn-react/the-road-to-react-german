## Speichere die letzten Suchaktionen

**Aufgabe:** Merke dir die letzten fünf Suchbegriffe, und zeige Schaltflächen an, mit deren Hilfe du schnell zwischen den Suchvorgängen wechselst. Wenn du auf eine der Schaltfläche klickst, werden die Suchergebnisse für den gespeicherten Begriff erneut abgerufen.

**Optionale Hinweise:**

* Verwende für dieses Feature keinen neuen Status. Nutze stattdessen den Status `url` und die Statusaktualisierungsfunktion `setUrl`, um Suchergebnise von der API abzurufen. Passe diese für die Verwendung mehrerer `urls` an und lege mit `setUrls` mehrere `urls` fest. Verwende die letzte URL von `urls` zum Abrufen der Daten, und die letzten fünf URLs von `urls` zum Anzeigen der Schaltflächen.

![](images/last-searches.png)

Zuerst werden wir an allen Stellen den Status `url` in `urls` und die Statusaktualisierungsfunktionen `setUrl` in `setUrls` umwandeln. Anstatt den Status mit einer `url` als Zeichenfolge zu initialisieren, konvertierst du ihn zu einem Array mit der anfänglichen `url` als einzigem Eintrag:

{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
  ...

# leanpub-start-insert
  const [urls, setUrls] = React.useState([
# leanpub-end-insert
    `${API_ENDPOINT}${searchTerm}`,
# leanpub-start-insert
  ]);
# leanpub-end-insert

  ...
};
~~~~~~~

Verwende anstelle des aktuellen `url`-Status zum Abrufen von Daten den letzten `url`-Eintrag aus dem `url`-Array. Wenn der Liste der `urls` eine andere `url` hinzugefügt wird, wird diese zum Abrufen der Daten verwendet:

{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {

  ...

  const handleFetchStories = React.useCallback(async () => {
    dispatchStories({ type: 'STORIES_FETCH_INIT' });

    try {
# leanpub-start-insert
      const lastUrl = urls[urls.length - 1];
      const result = await axios.get(lastUrl);
# leanpub-end-insert

      dispatchStories({
        type: 'STORIES_FETCH_SUCCESS',
        payload: result.data.hits,
      });
    } catch {
      dispatchStories({ type: 'STORIES_FETCH_FAILURE' });
    }
# leanpub-start-insert
  }, [urls]);
# leanpub-end-insert

  ...
};
~~~~~~~

Anstatt die `url` als Status mit der Statusaktualisierungsfunktion zu speichern, verknüpfe die neue `url` mit den vorherigen `urls` in einem Array. Dies ist der neue Status:

{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
  ...

  const handleSearchSubmit = event => {
# leanpub-start-insert
    const url = `${API_ENDPOINT}${searchTerm}`;
    setUrls(urls.concat(url));
# leanpub-end-insert

    event.preventDefault();
  };

  ...
};
~~~~~~~

Bei jeder Suche wird eine andere URL in unserem Status `urls` gespeichert. Rendere als Nächstes eine Schaltfläche für jede der letzten fünf URLs. Wir werden hierfür einen neuen universellen Handler hinzufügen. Jede übergibt eine `url` mit einem spezifischeren Inline-Handler:

{title="src/App.js",lang="javascript"}
~~~~~~~
# leanpub-start-insert
const getLastSearches = urls => urls.slice(-5);
# leanpub-end-insert

...

const App = () => {
  ...

# leanpub-start-insert
  const handleLastSearch = url => {
    // do something
  };
# leanpub-end-insert

# leanpub-start-insert
  const lastSearches = getLastSearches(urls);
# leanpub-end-insert

  return (
    <div>
      <h1>My Hacker Stories</h1>

      <SearchForm ... />

# leanpub-start-insert
      {lastSearches.map(url => (
        <button
          key={url}
          type="button"
          onClick={() => handleLastSearch(url)}
        >
          {url}
        </button>
      ))}
# leanpub-end-insert

      ...
    </div>
  );
};
~~~~~~~

Anstatt die gesamte URL der letzten Suche in der Schaltfläche als Schaltflächentext anzuzeigen, wird als Nächstes nur der Suchbegriff angezeigt, indem der Endpunkt der API durch eine leere Zeichenfolge ersetzt wird:

{title="src/App.js",lang="javascript"}
~~~~~~~
# leanpub-start-insert
const extractSearchTerm = url => url.replace(API_ENDPOINT, '');
# leanpub-end-insert

# leanpub-start-insert
const getLastSearches = urls =>
  urls.slice(-5).map(url => extractSearchTerm(url));
# leanpub-end-insert

...

const App = () => {
  ...

  const lastSearches = getLastSearches(urls);

  return (
    <div>
      ...

      {lastSearches.map(searchTerm => (
        <button
# leanpub-start-insert
          key={searchTerm}
# leanpub-end-insert
          type="button"
# leanpub-start-insert
          onClick={() => handleLastSearch(searchTerm)}
# leanpub-end-insert
        >
# leanpub-start-insert
          {searchTerm}
# leanpub-end-insert
        </button>
      ))}

      ...
    </div>
  );
};
~~~~~~~

Die Funktion `getLastSearches` gibt jetzt Suchbegriffe anstelle von URLs zurück. Das eigentliche `searchTerm` wird statt der `url` an den Inline-Handler übergeben. Durch Zuordnen über die Liste der `urls` in `getLastSearches` extrahieren wir den Suchbegriff für jede `url` innerhalb der Zuordnungsmethode des Arrays. Um es prägnanter und knapper zu gestalten, integrieren wir alles wie folgt:

{title="src/App.js",lang="javascript"}
~~~~~~~
const getLastSearches = urls =>
# leanpub-start-insert
  urls.slice(-5).map(extractSearchTerm);
# leanpub-end-insert
~~~~~~~

Jetzt stellen wir Funktionen für den neuen Handler bereit, der von jeder Schaltfläche verwendet wird --- das Klicken auf eine löst eine weitere Suche aus. Da wir den Status `urls` zum Abrufen von Daten verwenden und wissen, dass die letzte URL immer hierzu verwendet wird, füge der Liste der `urls` eine neue `url` hinzu. So löst du eine weitere Suchanforderung aus:

{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
  ...

# leanpub-start-insert
  const handleLastSearch = searchTerm => {
    const url = `${API_ENDPOINT}${searchTerm}`;
    setUrls(urls.concat(url));
  };
# leanpub-end-insert

  ...
};
~~~~~~~

Wenn du die Implementierungslogik dieses neuen Handlers mit `handleSearchSubmit` vergleichst, siehst du einige allgemeine Funktionen. Extrahiere die Funktionalität in einen neuen Handler und eine neue extrahierte Dienstprogrammfunktion:

{title="src/App.js",lang="javascript"}
~~~~~~~
# leanpub-start-insert
const getUrl = searchTerm => `${API_ENDPOINT}${searchTerm}`;
# leanpub-end-insert

...

const App = () => {
  ...

  const handleSearchSubmit = event => {
# leanpub-start-insert
    handleSearch(searchTerm);
# leanpub-end-insert

    event.preventDefault();
  };

  const handleLastSearch = searchTerm => {
# leanpub-start-insert
    handleSearch(searchTerm);
# leanpub-end-insert
  };

# leanpub-start-insert
  const handleSearch = searchTerm => {
    const url = getUrl(searchTerm);
    setUrls(urls.concat(url));
  };
# leanpub-end-insert

  ...
};
~~~~~~~

Die neue Dienstprogrammfunktion ist an anderen Stellen in der App-Komponente verwendbar. Wenn du eine solche Funktion extrahierst, überprüfe immer, ob sie von Dritten verwendet wird.

{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
  ...

  // important: still wraps the returned value in []
# leanpub-start-insert
  const [urls, setUrls] = React.useState([getUrl(searchTerm)]);
# leanpub-end-insert

  ...
};
~~~~~~~

Die Funktionalität ist zwar fertig umgesetzt; sie ist aber leider fehlerhaft. Beispielsweise wenn derselbe Suchbegriff mehrmals genutzt wird, da `searchTerm` für jedes Schaltflächenelement als `key`-Attribut verwendet wird. Verkette den Schlüssel mit dem `index` des zugeordneten Arrays, so ist er spezifischer und der zuvor beschriebene Fehler ist behoben.

{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
  ...

  return (
    <div>
      ...

# leanpub-start-insert
      {lastSearches.map((searchTerm, index) => (
# leanpub-end-insert
        <button
# leanpub-start-insert
          key={searchTerm + index}
# leanpub-end-insert
          type="button"
          onClick={() => handleLastSearch(searchTerm)}
        >
          {searchTerm}
        </button>
      ))}

      ...
    </div>
  );
};
~~~~~~~

Dies ist keine perfekte Lösung, da `index` kein stabiler Schlüssel ist. Insbesondere das Hinzufügen von Elementen ist nicht sicher. Wir verbessern die Anwendung aber nicht weiter, denn für unser Szenario reicht die bisherige Implementierung. Die Funktion macht was man von ihr erwartet. Mir ist es wichtiger, dir UX-Verbesserungen anhand der folgenden Aufgaben vorzuschlagen.

**Weitere Aufgaben:**

* (1) Zeige für die aktuelle Suche keine Schaltfläche an, erstelle diese nur für die fünf vorhergehenden Suchaktionen. Tipp: Passe die Funktion `getLastSearches` an.
* (2) Zeige eine Suchanfrage nicht doppelt an. Erstelle beispielsweise keine verschiedenen Schaltflächen, wenn du zweimal nach "React" suchst. Tipp: Ändere die Funktion `getLastSearches`.
* (3) Lege den Eingabefeldwert der SearchForm-Komponente mit dem letzten Suchbegriff fest, wenn du auf eine der Schaltflächen klickst.

Die fünf Schaltflächen werden in der Funktion `getLastSearches` erstellt. Dort nehmen wir das Array `urls` und geben die neuesten fünf Einträge zurück. Jetzt ändern wir diese Funktion so, dass die letzten sechs --- anstelle von fünf --- zurückgeben werden, wobei wir den aktuellsten Eintrag auslassen. Danach zeigt die Anwendung nur die fünf *vorherigen* Suchvorgänge als Schaltflächen an.

{title="src/App.js",lang="javascript"}
~~~~~~~
const getLastSearches = urls =>
  urls
# leanpub-start-insert
    .slice(-6)
    .slice(0, -1)
# leanpub-end-insert
    .map(extractSearchTerm);
~~~~~~~

Wenn dieselbe Suche zweimal oder mehrmals hintereinander aufgerufen wird, werden doppelte Schaltflächen angezeigt. Das ist nicht das erwartete Verhalten. Es ist akzeptabel, gleiche Suchen zusammenzufassen. Gruppiere dieselben Suchvorgänge, bevor du das Array in die fünf vorherigen unterteilen:

{title="src/App.js",lang="javascript"}
~~~~~~~
const getLastSearches = urls =>
  urls
# leanpub-start-insert
    .reduce((result, url, index) => {
      const searchTerm = extractSearchTerm(url);

      if (index === 0) {
        return result.concat(searchTerm);
      }

      const previousSearchTerm = result[result.length - 1];

      if (searchTerm === previousSearchTerm) {
        return result;
      } else {
        return result.concat(searchTerm);
      }
    }, [])
# leanpub-end-insert
    .slice(-6)
    .slice(0, -1);
~~~~~~~

Die Reduktionsfunktion wird mit einem leeren Array `result` initialisiert. Die erste Iteration konzentriert sich auf das `searchTerm`, das wir aus der `url` berechnet haben. Jedes `searchTerm` wird mit dem vorhergehenden verglichen. Wenn sich der vorherige Suchbegriff vom aktuellen unterscheidet, verknüpfe `searchTerm` mit dem Ergebnis. Wenn die Begriffe identisch sind, gib das Ergebnis zurück ohne sonst etwas zu veranlassen.

Zuletzt wird das Eingabefeld des Suchformulars mit dem neuen `searchTerm` verknüpft, wenn auf eine der Suchschaltflächen geklickt wird. Wir verwenden hierzu die Statusaktualisierungsfunktion.

{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
  ...

  const handleLastSearch = searchTerm => {
# leanpub-start-insert
    setSearchTerm(searchTerm);
# leanpub-end-insert

    handleSearch(searchTerm);
  };

  ...
};
~~~~~~~

Überarbeite zuletzt den in diesem Kapitel neu erstellten Code als eigenständige Komponente, damit die App übersichtlich bleibt:

{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
  ...

  const lastSearches = getLastSearches(urls);

  return (
    <div>
      ...

# leanpub-start-insert
      <LastSearches
        lastSearches={lastSearches}
        onLastSearch={handleLastSearch}
      />
# leanpub-end-insert

      ...
    </div>
  );
};

# leanpub-start-insert
const LastSearches = ({ lastSearches, onLastSearch }) => (
  <>
    {lastSearches.map((searchTerm, index) => (
      <button
        key={searchTerm + index}
        type="button"
        onClick={() => onLastSearch(searchTerm)}
      >
        {searchTerm}
      </button>
    ))}
  </>
);
# leanpub-end-insert
~~~~~~~

Die Umsetzung dieser Funktion war komplex. Es waren viele grundlegende React- und JavaScript-Kenntnisse erforderlich, um alles nachzuvollziehen, was ich hier gezeigt habe. Sorge dich nicht, wenn du nicht alles sofort verstanden hast. Gegebenenfalls hast du sogar einen anderen Weg gefunden, um die Aufgabe zu lösen, und dieser ist intuitiver als der, den ich hier gezeigt habe.

### Übungen:

* Begutachte den [Quellcode dieses Abschnittes](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/Remember-Last-Searches).
  * Reflektiere die [Änderungen gegenüber dem letzten Kapitel](https://github.com/the-road-to-learn-react/hacker-stories/compare/hs/Reverse-Sort...hs/Remember-Last-Searches?expand=1).
