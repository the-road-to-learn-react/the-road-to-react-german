# React in der Praxis(Fortgeschrittene Anleitung)

Wir haben in den vorausgehenden Kapiteln die Grundlagen von React erarbeitet. Nebenbei haben wir in der Vergangenheit genutzte Funktionen und Techniken zur Wartung behandelt. Jetzt ist es Zeit, dass wir uns mit der Entwicklung realer Anwendungen befassen. Jeder der folgenden Abschnitte enthält eine Aufgabe. Versuche, diese ohne die *optionalen Hinweise* zu lösen. Vieles ist nicht trivial. Du wirst dich herausgefordert fühlen. So lernst du am meisten. Verwende die *optionalen Hinweise*, um deinen Lösungsweg mit meinem abzugleichen --- oder, wenn du Hilfe benötigst.

## Sortierung

**Aufgabe:** Das Arbeiten mit einer Liste von Elementen beinhalte in der Regel Funktionen, die einem Benutzer den Zugriff auf die Daten erleichtern. Bisher wird jeder Artikel aufgelistet. Erweitern wir die Benutzerfreundlichkeit, indem wir eine Sortierung ermöglichen. Je nachdem welche Eigenschaft dem Benutzer wichtig ist, wird die Liste nach Titel, Autor, Kommentaren oder Punkten sortiert. Fürs Erste reicht es, wenn du die Sortierung in eine Richtung implementierst. Im nächsten Kapitel ermöglichen wir es, diese umzukehren.

**Optionale Hinweise:**

* Führe einen Sortierstatus in die App- oder Listenkomponente ein.
* Implementiere für jede Eigenschaft (`title`, `author`, `points`, `num_comments`) eine HTML-Schaltfläche, die den Sortierstatus festlegt.
* Verwende den Sortierstatus, um eine entsprechende Sortierfunktion auf die Liste anzuwenden.
* Verwende eine externe Bibliothek wie [Lodash](https://lodash.com/) um die Funktion  `sortBy` zu implementieren.

Wir behandeln die Liste wie eine Tabelle. Jede Zeile repräsentiert ein Element und jede Spalte die Eigenschaften. Die Überschriften der Spalten sind Orientierungshilfen:

{title="src/App.js",lang="javascript"}
~~~~~~~
# leanpub-start-insert
const List = ({ list, onRemoveItem }) => (
  <div>
    <div style={{ display: 'flex' }}>
      <span style={{ width: '40%' }}>Title</span>
      <span style={{ width: '30%' }}>Author</span>
      <span style={{ width: '10%' }}>Comments</span>
      <span style={{ width: '10%' }}>Points</span>
      <span style={{ width: '10%' }}>Actions</span>
    </div>

    {list.map(item => (
# leanpub-end-insert
      <Item
        key={item.objectID}
        item={item}
        onRemoveItem={onRemoveItem}
      />
# leanpub-start-insert
    ))}
  </div>
);
# leanpub-end-insert
~~~~~~~

Wir verwenden der Einfachheit halber Inline-CSS. Gib den Zeilen in der Item-Komponente ein Layout, um dieses mit den Kopfzeilen abzugleichen:

{title="src/App.js",lang="javascript"}
~~~~~~~
const Item = ({ item, onRemoveItem }) => (
# leanpub-start-insert
  <div style={{ display: 'flex' }}>
    <span style={{ width: '40%' }}>
# leanpub-end-insert
      <a href={item.url}>{item.title}</a>
    </span>
# leanpub-start-insert
    <span style={{ width: '30%' }}>{item.author}</span>
    <span style={{ width: '10%' }}>{item.num_comments}</span>
    <span style={{ width: '10%' }}>{item.points}</span>
    <span style={{ width: '10%' }}>
# leanpub-end-insert
      <button type="button" onClick={() => onRemoveItem(item)}>
        Dismiss
      </button>
    </span>
  </div>
);
~~~~~~~

In der laufenden Implementierung werden ich der Übersicht halber die Inline-CSS-Stilattribute entfernen. Behalte du diese aber gerne in deiner Anwendung bei.

Die List-Komponente verwaltet den Sortierstatus in meiner Version. Dieser wird mit dem Status `'NONE'` initialisiert, so werden die Listenelemente in der Reihenfolge angezeigt, in der sie die API sie ausgibt. Außerdem habe ich einen neuen Handler hinzugefügt, um den Sortierstatus mit einem spezifischen Schlüssel festzulegen.

{title="src/App.js",lang="javascript"}
~~~~~~~
# leanpub-start-insert
const List = ({ list, onRemoveItem }) => {
  const [sort, setSort] = React.useState('NONE');

  const handleSort = sortKey => {
    setSort(sortKey);
  };

  return (
# leanpub-end-insert
    ...
# leanpub-start-insert
  );
};
# leanpub-end-insert
~~~~~~~

Lege in der Kopfzeile der List-Komponente mithilfe der Schaltflächen den Sortierstatus für jede Eigenschaft fest. Ein Inline-Handler wird verwendet, um den spezifischen Schlüssel (`sortKey`) einzugeben. Wenn du auf die Schaltfläche für die Spalte "Title" klickst, wird `'TITLE'` zum neuen Sortierstatus.

{title="src/App.js",lang="javascript"}
~~~~~~~
const List = ({ list, onRemoveItem }) => {
  ...

  return (
    <div>
      <div>
        <span>
# leanpub-start-insert
          <button type="button" onClick={() => handleSort('TITLE')}>
            Title
          </button>
# leanpub-end-insert
        </span>
        <span>
# leanpub-start-insert
          <button type="button" onClick={() => handleSort('AUTHOR')}>
            Author
          </button>
# leanpub-end-insert
        </span>
        <span>
# leanpub-start-insert
          <button type="button" onClick={() => handleSort('COMMENT')}>
            Comments
          </button>
# leanpub-end-insert
        </span>
        <span>
# leanpub-start-insert
          <button type="button" onClick={() => handleSort('POINT')}>
            Points
          </button>
# leanpub-end-insert
        </span>
        <span>Actions</span>
      </div>

      {list.map(item => ... )}
    </div>
  );
};
~~~~~~~

Die Statusverwaltung für die neue Funktion ist implementiert, aber wir sehen keine Änderung, wenn wir auf eine Schaltfläche klicken. Dies ist so, weil der Sortiermechanismus nicht auf `list` angewendet wird.

Das Sortieren eines Arrays mit JavaScript ist nicht trivial, da jedes JavaScript-Grundelement (z. B. Zeichenfolge, Boolescher Wert, Zahl) Besonderheiten auf der Sortierung vorweist. Deshalb erfinden wir das Rad nicht neu, sondern verwenden eine vorhandene Lösung: [Lodash](https://lodash.com/). Installiere die externe Bibliothek über die Befehlszeile:

{title="Command Line",lang="text"}
~~~~~~~
npm install lodash
~~~~~~~

Second, at the top of your file, import the utility function for sorting:

{title="src/App.js",lang="javascript"}
~~~~~~~
import React from 'react';
import axios from 'axios';
# leanpub-start-insert
import { sortBy } from 'lodash';
# leanpub-end-insert

...
~~~~~~~

Erstelle ein JavaScript-Objekt mit allen möglichen Zuordnungen von `sortKey` zu einer Suchfunktion. Jeder Sortierschlüssel ist einer Funktion zugeordnet, welche `list` sortiert. Durch Sortieren nach `'NONE'` wird die unsortierte Liste zurückgegeben. Beim Sortieren nach `'POINT'` wird die Liste anhand der Eigenschaft `points` sortiert zurückgegeben.

{title="src/App.js",lang="javascript"}
~~~~~~~
# leanpub-start-insert
const SORTS = {
  NONE: list => list,
  TITLE: list => sortBy(list, 'title'),
  AUTHOR: list => sortBy(list, 'author'),
  COMMENT: list => sortBy(list, 'num_comments').reverse(),
  POINT: list => sortBy(list, 'points').reverse(),
};
# leanpub-end-insert

const List = ({ list, onRemoveItem }) => {
  ...
};
~~~~~~~

Mit dem Status `sort` (`sortKey`) und allen möglichen Sortiervariationen mit `SORTS` sortieren wir die Liste, bevor wir sie jeder Item-Komponente zuordnen:

{title="src/App.js",lang="javascript"}
~~~~~~~
const List = ({ list, onRemoveItem }) => {
  const [sort, setSort] = React.useState('NONE');

  const handleSort = sortKey => {
    setSort(sortKey);
  };

# leanpub-start-insert
  const sortFunction = SORTS[sort];
  const sortedList = sortFunction(list);
# leanpub-end-insert

  return (
    <div>
      ...

# leanpub-start-insert
      {sortedList.map(item => (
# leanpub-end-insert
        <Item
          key={item.objectID}
          item={item}
          onRemoveItem={onRemoveItem}
        />
      ))}
    </div>
  );
};
~~~~~~~

Fertig! Was haben wir genau umgesetzt? Zuerst erstellten wir die Sortierfunktion mithilfe eines `sortKey` (Status). Anschließend wenden wir die Funktion auf die Liste an, bevor wir sie zum Rendern jedem Item zuordnen. Der anfängliche Sortierstatus ist `'NONE'`, was bedeutet, dass nichts sortiert wird.

Deshalb haben wir als Nächstes Schaltflächen inklusive Implementierungsdetails erstellt, um unseren Benutzern die Interaktion zu ermöglichen. Dabei verwenden wir den Sortierstatus, um die Liste zu sortieren.

### Übungen:

* Begutachte den [Quellcode dieses Abschnittes](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/Sort).
  * Bestätige die [Änderungen gegenüber dem Stand der Anwendung am Ende des ersten Kapitels](https://github.com/the-road-to-learn-react/hacker-stories/compare/hs/react-modern-final...hs/Sort?expand=1).
* Lese mehr über [Lodash](https://lodash.com/).
* Warum verwenden wir bei der Sortierung von numerischen Eigenschaften wie `points` und `num_comments` die umgekehrte Reihenfolge?
* Zeige dem Benutzer, welche Sortierung aktiv ist. Implementiere dies auf eine unkomplizierte Art und Weise. Beispielsweise reichte es aus, der zuletzt angeklickten Schaltfläche eine andere Farbe zuzuweisen.
