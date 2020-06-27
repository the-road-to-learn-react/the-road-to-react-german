## Bibliotheken von Drittanbietern in React

Wir nutzen die [Fetch-API](https://developer.mozilla.org/de/docs/Web/API/Fetch_API), um die Hacker News-API abzufragen. Diese wird leider nicht von allen Browsern zuverlässig unterstützt. Insbesondere bei alten Versionen ist die [Browserkompatibilität](https://developer.mozilla.org/de/docs/Web/API/Fetch_API#Browserkompatibilit%C3%A4t) lückenhaft. Spätestens sobald du deine Anwendung in einer [Headless-Browser-Umgebung](https://en.wikipedia.org/wiki/Headless_browser) testest, werden Probleme mit der Fetch-API auftreten. Es gibt verschiedene Möglichkeiten, das Abrufen in älteren Browsern mithilfe von ([Polyfills](https://de.wikipedia.org/wiki/Polyfill)) und die Ausführung in Tests (somorphic fetch) zum Laufen zu bringen. Die genauere Erklärung der Konzepte würde den Rahmen dieses Buchs sprengen.

Eine andere Alternative besteht darin, die Fetch-API durch eine stabile Drittanbieter Bibliothek zu ersetzten. Da das Integrieren von externen Modulen Thema dieses Abschnittes ist, schlagen wir im folgenden zwei Fliegen mit einer Klappe. Wir ersetzen die Fetch-API mit dem HTTP-Client [axios](https://github.com/axios/axios), welcher asynchrone Anforderungen unterstützt. In diesem Abschnitt lernst du, wie du eine Bibliothek --- in unserem Fall die Fetch-API, eine native API des Browsers --- durch ein Node.js-Module ersetzt. Installiere zunächst axios mithilfe der Befehlszeile:

{title="Command Line",lang="text"}
~~~~~~~
npm install axios
~~~~~~~

Importieren danach Axios in deine Anwendung:

{title="src/App.js",lang="javascript"}
~~~~~~~
import React from 'react';
# leanpub-start-insert
import axios from 'axios';
# leanpub-end-insert

...
~~~~~~~

Jetzt ist es dir möglich, *axios* anstelle der *Fetch-API* zu verwenden. Die Anwendung ist ähnlich wie zuvor. Axios nimmt die URL als Argument entgegen und gibt ein Promise-Objekt zurück. Es ist nicht mehr erforderlich, die zurückgegebene Antwort in JSON umzuwandeln, da axios das Ergebnis in ein JavaScript-Objekt einschließt. Passen wir den Beispielcode im nächsten Schritt an die Verwendung von axios an:

{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
  ...

  const handleFetchStories = React.useCallback(() => {
    dispatchStories({ type: 'STORIES_FETCH_INIT' });

# leanpub-start-insert
    axios
      .get(url)
# leanpub-end-insert
      .then(result => {
        dispatchStories({
          type: 'STORIES_FETCH_SUCCESS',
# leanpub-start-insert
          payload: result.data.hits,
# leanpub-end-insert
        });
      })
      .catch(() =>
        dispatchStories({ type: 'STORIES_FETCH_FAILURE' })
      );
  }, [url]);

  ...
};
~~~~~~~

In diesem Code führen wir mit `axios.get()` eine [HTTP-GET-Anfrage](https://developer.mozilla.org/de/docs/Web/HTTP/Methods/GET) aus. Es handelt sich um dieselbe HTTP-Methode, die wir mit der `Fetch-API` verwendeten. `axios` ist intuitiv aufgebaut: Für eine [HTTP-POST-Anfrage](https://developer.mozilla.org/de/docs/Web/HTTP/Methods/POST) verwendest du `axios.post()`.

Sieh dir die von `axios` zurückgegeben Daten in der Konsole deines Browsers an, indem du die nachfolgende Zeile temporär im Code ergänzt:

{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
  ...

    axios
      .get(url)
      .then(result => {
# leanpub-start-insert
          console.log(result.data.hits);
# leanpub-end-insert
        dispatchStories({
          type: 'STORIES_FETCH_SUCCESS',

  ...
};
~~~~~~~

Wie du sieht: `axios` ist eine leistungsstarke Bibliothek zum Zugriff auf Remote-APIs. Ich empfehle dir diese anstelle der nativen Abruf-API, wenn deine Anforderungen komplex sind, es dir wichtig ist, älteren Browser zu unterstützen und/oder du ein Framework zum Testen verwendest.

### Übungen:

* Begutachte den [Quellcode dieses Abschnittes](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/Third-Party-Libraries-in-React).
  * Reflektiere die [Änderungen gegenüber dem letzten Abschnitt](https://github.com/the-road-to-learn-react/hacker-stories/compare/hs/Explicit-Data-Fetching-with-React...hs/Third-Party-Libraries-in-React?expand=1).
* Informiere dich über [beliebte Bibliotheken in React](https://www.robinwieruch.de/react-libraries).
* Lese [warum Frameworks unerlässlich sind](https://www.robinwieruch.de/why-frameworks-matter).
* Informiere dich über [Axios](https://github.com/axios/axios).
