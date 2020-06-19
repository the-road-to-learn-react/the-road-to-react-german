## Callback-Handler in JSX

Als Nächstes konzentrieren wir uns auf das Eingabefeld und das Label. Wir erstellen eine eigenständige Komponente für die Suche. Im Komponentenbaum wird die Suchkomponente zu einem Blatt auf der gleichen Ebene, quasi ein Geschwisterkind von `List`. Außerdem verschieben wir den Handler und den Status in die neue `Search`-Komponente, um die Funktionalität aufrechtzuerhalten.

{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
  const stories = [ ... ];

  return (
    <div>
      <h1>My Hacker Stories</h1>

# leanpub-start-insert
      <Search />
# leanpub-end-insert

      <hr />

      <List list={stories} />
    </div>
  );
};

# leanpub-start-insert
const Search = () => {
  const [searchTerm, setSearchTerm] = React.useState('');

  const handleChange = event => {
    setSearchTerm(event.target.value);
  };

  return (
    <div>
      <label htmlFor="search">Search: </label>
      <input id="search" type="text" onChange={handleChange} />

      <p>
        Searching for <strong>{searchTerm}</strong>.
      </p>
    </div>
  );
};
# leanpub-end-insert
~~~~~~~

Jetzt verfügen wir über eine für sich abgeschlossene Suchkomponente, die ihren Status selbst verwaltet. Sie zeigt das `searchTerm` als Text an, teilt diese Informationen aber nicht mit übergeordneten oder gleichrangigen Komponenten. Da die Suchkomponente einzig und allein den Suchbegriff anzeigt, ist sie für die anderen unnütz. Es sei denn, es gäbe doch eine Möglichkeit Informationen bei ihr abzufragen.

![](images/callback-handler.png)

Auf den ersten Blick gibt es keine Möglichkeit, Informationen an übergeordnete oder gleichrangige Komponenten zu übergeben. Eigenschaften (Props) werden ausschließlich nach unten weitergegeben. Deshalb schauen wir ein zweites Mal und führen den **Callback-Handler** als Funktion ein: Dies geschieht wie folgt: Eine Rückruffunktion wird eingeführt (A), diese wird an einer anderen Stelle verwendet (B) und gibt das Ergebnis an den Ort zurück, an dem sie eingeführt wurde (C). Das ist undramatischer, als der Name vermuten lässt, oder? Sieh es dir an:

{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
  const stories = [ ... ];

# leanpub-start-insert
  // A
  const handleSearch = event => {
    // C
    console.log(event.target.value);
  };
# leanpub-end-insert

  return (
    <div>
      <h1>My Hacker Stories</h1>

# leanpub-start-insert
      <Search onSearch={handleSearch} />
# leanpub-end-insert

      <hr />

      <List list={stories} />
    </div>
  );
};

# leanpub-start-insert
const Search = props => {
# leanpub-end-insert
  const [searchTerm, setSearchTerm] = React.useState('');

  const handleChange = event => {
    setSearchTerm(event.target.value);

# leanpub-start-insert
    // B
    props.onSearch(event);
# leanpub-end-insert
  };

  return ( ... );
};
~~~~~~~

Schauen wir uns das Konzept des Callback-Handlers Schritt für Schritt an: Wir übergeben eine Funktion von einer Komponente an eine andere --- hier konkret von App an Search. Wir nennen diese in Search `onSearch`. Die eigentliche Implementierung ist in `handleSearch` der App-Komponente. So tauschen wir Informationen im Komponentenbaum aus. Eine Funktion wird zu einem Callback-Handler, der über Eigenschaften (Props) an andere weitergegeben wird. Auf diese Weise kommunizieren die Komponenten innerhalb der Komponentenhierarchie untereinander.

### Übungen:

* Begutachte den [Quellcode dieses Abschnittes](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/Callback-Handler-in-JSX).
  * Reflektiere die [Änderungen gegenüber dem letzten Abschnitt](https://github.com/the-road-to-learn-react/hacker-stories/compare/hs/React-State...hs/Callback-Handler-in-JSX?expand=1).
* Wiederhole und durchdenke Eventhandler und Callback-Handlern.
