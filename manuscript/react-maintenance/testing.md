## React Anwendungen testen 

Das Testen des Quellcodes wird oft vernachlässigt. Dabei ist es wesentlich und zwingend erforderlich. Es ist unentbehrlich, die Qualität und Funktionalität des Codes automatisch zu überprüfen, bevor dieser im Echtsystem angewendet wird. Automatisierte Tests sind schneller durchführbar, fehlerfreier und kostengünstiger. Maschinen sind hier dem Menschen überlegen. Die [Testpyramide](https://martinfowler.com/articles/practical-test-pyramid.html) dient als Grundregel.

Die Testpyramide umfasst End-to-End-Tests (E2E), Integrationstests und Komponententests (Unit-Tests). Letztere werden für kleine, isolierte Codeblöcke verwendet, zum Beispiel eine einzelne Funktion oder Komponente. Man betrachtet hierbei einen Teilaspekt herausgelöst. Integrationstests helfen uns, herauszufinden, ob diese Einheiten zusammenarbeiten. Ein End-to-End-Test simuliert ein reales Szenario, beispielsweise den Anmeldevorgang einer Webanwendung. Unit-Tests laufen schnell ab, sind isolierte Einheiten und somit unkompliziert zu schreiben und zu warten. Auf End-to-End-Tests trifft das Gegenteil zu. Deshalb sind die Unit-Tests die Basis der Pyramide --- der breitere Teil, der suggeriert, dass diese mengenmäßig überwiegen. Nur der Vollständigkeit halber: Manche stellen die Pyramide auf den Kopf und je nach Anwendung ist das in Ordnung so. In der Regel ist die Pyramide erwiesenermaßen die passende Wahl.

![](images/testing-pyramid.png)

Scheiben wir zuerst Unit-Tests, die unsere Funktionen und Komponenten abdecken. Danach verwenden wir Integrationstests, um sicherzustellen, dass die einzelnen mit Unit-Tests unabhängig geprüften Einheiten, wie erwartet zusammenarbeiten. Am Ende benötigen wir End-to-End-Tests, um kritische Szenarien zu simulieren. In diesem Kapitel behandeln wir **Unit und Integrationstests** sowie eine komponentenspezifische Testtechnik namens **Schnappschusstest oder Snapshot Test**. **End-to-End-Tests** werden Teil der Übungen sein.

Da es viele Test-Bibliotheken gibt, ist es als Anfänger schwierig, die passende auszuwählen. Wir werden [Jest](https://jestjs.io/) und die [React Testing Library](https://testing-library.com/) (RTL) verwenden. Während Jest ein umfassendes Testframework mit Testrunner, Testsuiten, Testfällen und Assertions (Behauptungen) ist, wird die React Testing Library zum Rendern von React-Komponenten, Auslösen von Ereignissen wie Mausklicks und Auswählen von HTML-Elementen aus dem DOM verwendet. In den nächsten Abschnitten werden wir beide Tools Schritt für Schritt für Unit-Tests und Integrationstests untersuchen.

### Testsuiten, Testfälle und Assertions (Behauptungen)

Jest bietet dir alles, was du von einem Test-Framework erwartest. In JavaScript, und in vielen anderen Programmiersprachen, werden häufig Testsuiten und Testfälle verwendet. Während eine Testsuite mehrere inhaltlich zusammengehörende Testfälle vereint, besteht ein Testfall aus einem einzelnen Szenario. Sehen wir uns das mit Jest anhand der Datei *src/App.test.js* praktisch an:

{title="src/App.test.js",lang="javascript"}
~~~~~~~
describe('something truthy and falsy', () => {
  test('true to be true', () => {
    expect(true).toBe(true);
  });

  test('false to be false', () => {
    expect(false).toBe(false);
  });
});
~~~~~~~

Während der "describe"-Block die *Testsuite* ist, sind die "test"-Blöcke die *Testfälle*. Beachte, dass es möglich ist, Testfälle ohne Testsuite zu verwenden. Für unser Beispiel ist das nicht relevant, mir ist es aber wichtig, es zu erwähnen:

{title="src/App.test.js",lang="javascript"}
~~~~~~~
test('true to be true', () => {
  expect(true).toBe(true);
});

test('false to be false', () => {
  expect(false).toBe(false);
});
~~~~~~~

Warum nutzen wir Testsuiten und Testfälle? Es kommt häufig vor, dass beispielsweise eine Funktion oder eine Komponente mehrere Fälle aufweist --- jeder ist Teil eines Szenarios. Eine Testsuite ist in der Regel eine Sammlung der Testfälle, die zusammen eine Aktion simulieren. Zur Testausführung werden diese in einer Gruppe zusammengefasst aufgerufen. So wird die Aktion im Ganzen getestet. Daher ist es sinnvoll, Testsuiten und Testfälle in Kombination zu verwenden. Manchmal testet man eine aufeinander abgestimmte Abfolge, beispielsweise

- Testfall 1: Anmeldung
- Testfall 2: Anzeige einer Liste 
- Testfall 3: Löschung eines Eintrags
- Testfall 4: Abmeldung. 

Wir testen Teile der App-Komponente zusammenhängend in einer Testsuite:

{title="src/App.test.js",lang="javascript"}
~~~~~~~
describe('App component', () => {
  test('removes an item when clicking the Dismiss button', () => {

  });

  test('requests some initial stories from an API', () => {

  });
});
~~~~~~~

Hast du schon einmal mit Tests gearbeitet und kennst "it"-Blocks? Ein "it"-Block ist das gleiche wie ein "test"-Block. Warum gibt es beide Varianten? In der Vergangenheit wurde "it" genutzt. "test" ist neuer und gibt dem Ganzen einen passenderen Namen. Nenne deinen Block so, wie es dir am liebsten ist. 

{title="src/App.test.js",lang="javascript"}
~~~~~~~
describe('something truthy and falsy', () => {
# leanpub-start-insert
  it('true to be true', () => {
# leanpub-end-insert
    expect(true).toBe(true);
  });

# leanpub-start-insert
  it('false to be false', () => {
# leanpub-end-insert
    expect(false).toBe(false);
  });
});
~~~~~~~

Glücklicherweise beinhaltet die *Create React App* Jest, sodass alle wichtigen Werkzeuge installiert und konfiguriert sind. Führe die Tests mit dem Skript aus der Datei *package.json* direkt in der Befehlszeile aus. Verwendet dazu den Befehl `npm test` und du siehst die folgende Ausgabe:

{title="Command Line",lang="text"}
~~~~~~~
 PASS  src/App.test.js
  something truthy and falsy
    ✓ true to be true (3ms)
    ✓ false to be false (1ms)

Test Suites: 1 passed, 1 total
Tests:       2 passed, 2 total
Snapshots:   0 total
Time:        2.78s, estimated 4s
Ran all test suites related to changed files.

Watch Usage
 › Press a to run all tests.
 › Press f to run only failed tests.
 › Press q to quit watch mode.
 › Press p to filter by a filename regex pattern.
 › Press t to filter by a test name regex pattern.
 › Press Enter to trigger a test run.
~~~~~~~

Jest vergleicht alle Dateien mit dem Suffix *test.js* im Dateinamen, wenn der Befehl aufgerufen wird. Erfolgreiche Tests werden grün, fehlgeschlagene rot markiert. Das interaktive Test-Skript überwacht den Code und führt die Tests jedes Mal aus, wenn sich etwas im Code ändert. Es bietet dir zusätzliche Befehle wie das Drücken von "f", um nur fehlgeschlagene, und "a", um alle Tests erneut auszuführen. Probiere das mit einem absichtlich fehlerhaften Test praktisch aus:

{title="src/App.test.js",lang="javascript"}
~~~~~~~
describe('something truthy and falsy', () => {
  test('true to be true', () => {
    expect(true).toBe(true);
  });

  test('false to be false', () => {
# leanpub-start-insert
    expect(false).toBe(true);
# leanpub-end-insert
  });
});
~~~~~~~

Wenn du die Ausführung von npm test nicht beendet hast, sie somit bei dir weiterhin aktiv ist, dann werden deine Tests nach der Änderung im Quellcode erneut aufgerufen und du siehts über die Befehlszeilenausgabe einen roten Hinweis bezüglich des fehlgeschlagenen Tests:

{title="Command Line",lang="text"}
~~~~~~~
 FAIL  src/App.test.js
  something truthy and falsy
    ✓ true to be true (2ms)
    ✕ false to be false (4ms)

  ● something truthy and falsy › false to be false

    expect(received).toBe(expected) // Object.is equality

    Expected: true
    Received: false

       5 |
       6 |   test('false to be false', () => {
    >  7 |     expect(false).toBe(true);
         |                   ^
       8 |   });
       9 | });
      10 |

      at Object.<anonymous> (src/App.test.js:7:19)

Test Suites: 1 failed, 1 total
Tests:       1 failed, 1 passed, 2 total
Snapshots:   0 total
Time:        3.385s
Ran all test suites related to changed files.

Watch Usage: Press w to show more.
~~~~~~~

Mach dich mit der Testausgabe vertraut. Diese enthält alle Informationen, um fehlgeschlagene Tests zu erkennen und so die Ursache zu beheben. Korrigiere den  problematischen Test und überprüfe in der Befehlszeile, ob die Ausgabe grün ist.

Ich habe bisher nichts zu Behauptung (Assertion) geschrieben, obwohl diese ebenfalls wichtig sind. Du hast im Beispiel zwei Test-Assertions mit der Funktion `expect` verwendet. `expect` ist von Hause aus in Jest enthalten. Eine Behauptung funktioniert wie folgt: Es wird erwartet, dass die linke Seite (`expect`) mit der rechten (`toBe`) übereinstimmt. `toBe` ist dabei eine von vielen Funktionen, die mit Jest verfügbar sind:

{title="src/App.test.js",lang="javascript"}
~~~~~~~
describe('something truthy and falsy', () => {
  test('true to be true', () => {
    expect(true).toBeTruthy();
  });

  test('false to be false', () => {
    expect(false).toBeFalsy();
  });
});
~~~~~~~

Tipp: Nutze gleichzeitig zwei Befehlszeilen, wenn du mit Tests arbeitest: eine zum Überwachen deiner Tests (`npm test`) und eine zum Entwickeln der Anwendung (`npm start`). Wenn du eine Versionsverwaltung wie [Git](https://de.wikipedia.org/wiki/Git) nutzt, benötigst du unter Umständen eine weitere offene Befehlszeile, um deinen Code zu einem Repository hinzuzufügen.

### Übungen:

* Begutachte den [Quellcode dieses Abschnittes](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/react-testing-setup).
  * Reflektiere die [Änderungen gegenüber dem Stand der Anwendung am Ende des ersten Kapitels](https://github.com/the-road-to-learn-react/hacker-stories/compare/hs/react-modern-final...hs/react-testing-setup?expand=1).
* Lese mehr über [Jest](https://jestjs.io/).

### Unit Testing: Funktionen

Als Erstes schreiben wir einen Unit-Test. Dieser testet normalerweise eine Komponente oder eine Funktion isoliert. Dies bedeutet, dass wir im Falle einer Funktion nur die Ein- und die Ausgabe testen. Bei einer Komponente testen wir die Eigenschaften (Props) und die Callback-Handler.

Bevor wir etwas in der Datei *src/App.js* testen ist es erforderlich, dass wir Komponenten und Funktionen exportieren:

{title="src/App.js",lang="javascript"}
~~~~~~~
...

export default App;

# leanpub-start-insert
export { storiesReducer, SearchForm, InputWithLabel, List, Item };
# leanpub-end-insert
~~~~~~~

Ich liste dir hier im Abschnitt nicht die Tests für sämtliche Szenarien auf. Vielmehr zeige ich die Implementierung beispielhaft und überlasse es dir, diese zu vervollständigen. Importieren wir als Erstes alles Erforderliche in die Datei *src/App.test.js*. Wie du siehst, ist React ebenfalls notwendig!

{title="src/App.test.js",lang="javascript"}
~~~~~~~
import React from 'react';

import App, {
  storiesReducer,
  Item,
  List,
  SearchForm,
  InputWithLabel,
} from './App';
~~~~~~~

Bevor wir den ersten Unit-Test schreiben, zeige ich dir, wie du eine einzelne JavaScript-Funktion testest. Der beste Kandidat für diesen Testanwendungsfall ist `storiesReducer`. Zunächst definieren wir einige Testdaten und die Testsuite:

{title="src/App.test.js",lang="javascript"}
~~~~~~~
import React from 'react';

...

# leanpub-start-insert
const storyOne = {
  title: 'React',
  url: 'https://reactjs.org/',
  author: 'Jordan Walke',
  num_comments: 3,
  points: 4,
  objectID: 0,
};

const storyTwo = {
  title: 'Redux',
  url: 'https://redux.js.org/',
  author: 'Dan Abramov, Andrew Clark',
  num_comments: 2,
  points: 5,
  objectID: 1,
};

const stories = [storyOne, storyTwo];

describe('storiesReducer', () => {
  test('removes a story from all stories', () => {

  });
});
# leanpub-end-insert
~~~~~~~

Wenn du dir ansiehst, was zu testen ist, kommst du auf mindestens einen Testfall pro Reduzierungsaktion. Wir werden uns auf einen Test konzentrieren. Im Allgemeinen akzeptiert die Reduzierungsfunktion einen Zustand und eine Aktion und gibt einen neuen Zustand zurück. Jeder Test des Reduzierers folgt dem gleichen Muster:

{title="src/App.test.js",lang="javascript"}
~~~~~~~
...

describe('storiesReducer', () => {
  test('removes a story from all stories', () => {
# leanpub-start-insert
    const action = // TODO: eine Aktion
    const state = // TODO: ein Status

    const newState = storiesReducer(state, action);

    const expectedState = // TODO: der erwartete Status

    expect(newState).toBe(expectedState);
# leanpub-end-insert
  });
});
~~~~~~~

Für unseren speziellen Fall definieren wir eine Aktion `action`, einen Zustand `state` und ein erwartetes Ergebnis `newState`. Letzteres ist im Beispiel wie folgt: Die Liste enthält einen Eintrag weniger, da das Item entfernt wird, welches mit er Aktion an den Reduzierer übergeben wurde:

{title="src/App.test.js",lang="javascript"}
~~~~~~~
describe('storiesReducer', () => {
  test('removes a story from all stories', () => {
# leanpub-start-insert
    const action = { type: 'REMOVE_STORY', payload: storyOne };
    const state = { data: stories, isLoading: false, isError: false };
# leanpub-end-insert

    const newState = storiesReducer(state, action);

# leanpub-start-insert
    const expectedState = {
      data: [storyTwo],
      isLoading: false,
      isError: false,
    };
# leanpub-end-insert

    expect(newState).toBe(expectedState);
  });
});
~~~~~~~

Auf den ersten Blick sieht es so aus, als ob der Test erfolgreich sein wird. Aber: Er schlägt fehl. Das liegt daran, dass wir hier `toBe` anstelle von `toStrictEqual` verwenden. Die Funktion `toBe` führt einen [strengen Vergleich](https://developer.mozilla.org/de/docs/Web/JavaScript/Vergleiche_auf_Gleichheit_und_deren_Verwendung) wie `newState === expectedState` durch. Die Objektreferenz ist nicht dieselbe. Nur der Inhalt des Objekts ist gleich. Daher vergleichen wir besser mit `toStrictEqual` anstelle von `toBe`:

{title="src/App.test.js",lang="javascript"}
~~~~~~~
describe('storiesReducer', () => {
  test('removes a story from all stories', () => {
    const action = { type: 'REMOVE_STORY', payload: storyOne };
    const state = { data: stories, isLoading: false, isError: false };

    const newState = storiesReducer(state, action);

    const expectedState = {
      data: [storyTwo],
      isLoading: false,
      isError: false,
    };

# leanpub-start-insert
    expect(newState).toStrictEqual(expectedState);
# leanpub-end-insert
  });
});
~~~~~~~

Im Falle von JavaScript-Objekte ist es wichtig, zu unterscheiden, ob du einen strengen Vergleich oder nur einen Inhaltsvergleich beabsichtigst. Meistens ist der Inhalt relevant, verwende daher `toStrictEqual`. Für Strings oder boolesche Werte nutzt du weiterhin `toBe`. Beachte, dass es eine `toEqual`-Funktion gibt, die [etwas anders funktioniert](https://twitter.com/rwieruch/status/1260866850080067584) als `toStrictEqual`. `toStrictEqual` überprüft zusätzlich, ob zwei Objekte denselben Typ haben.

Kommen wir zurück auf unser Beispiel: Der Reduzierertest schließt jetzt erfolgreich ab. Es wird eine JavaScript-Funktion mithilfe getestet. Wir geben Werte in diese ein und erwarten eine vordefinierte Ausgabe. Wie erwähnt, folgt eine Reduzierungsfunktion immer demselben Testmuster: Wenn ein Zustand und eine Aktion gegeben sind, erwarten wir ein Ergebnis. Jede Aktion ist ein weiterer Testfall in der Testsuite unseres Reduzierers. Das Testen der verbleibenden überlasse ich dir als Übung. Du weißt jetzt alles, was dazu notwendig ist.

### Übungen:

* Begutachte den [Quellcode dieses Abschnittes](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/react-testing-unit-function).
  * Reflektiere die [Änderungen gegenüber dem Stand der Anwendung am Ende des letzten Kapitels](https://github.com/the-road-to-learn-react/hacker-stories/compare/hs/react-testing-setup...hs/react-testing-unit-function?expand=1).
* Lese mehr über Jests Funktionen wie `toBe` und `toStrictEqual`.

### Unit Testing: Komponenten

Nachdem wir eine Funktion in JavaScript mit Jest getestet haben, testen wir jetzt isoliert eine Komponente. Wir nutzen die React Testing Library (RTL). Diese ist in der *Create React App* integriert. Wenn du die *Create React App* nutzt, ist alles schon fertig installiert und konfiguriert. Falls du ein benutzerdefiniertes React-Setup verwendest, beispielsweise React mit Webpack, installierst du die Bibliothek bitte selbst.

Wir nutzen die folgenden Funktionen der React Testing Library für den Komponententest. Ich werde diese Schritt für Schritt erklären. Als Erstes importieren wir diese:

{title="src/App.test.js",lang="javascript"}
~~~~~~~
import React from 'react';
# leanpub-start-insert
import {
  render,
  screen,
  fireEvent,
  act,
} from '@testing-library/react';
# leanpub-start-insert

...
~~~~~~~

Testen wir die Item-Komponente. Wichtig ist, dass alle Eigenschaften (Props) entsprechend der Eingabe gerendert werden. Mit anderen Worten: Ist die Ausgabe im HTML korrekt. Wir verwenden im Test die `render`-Funktion, um eine React-Komponente im HTML anzuzeigen. In diesem Fall rendern wir Item als Element und übergeben ihm als Eigenschaft (Prop) ein `item`-Objekt:

{title="src/App.test.js",lang="javascript"}
~~~~~~~
# leanpub-start-insert
describe('Item', () => {
  test('renders all properties', () => {
    render(<Item item={storyOne} />);
  });
});
# leanpub-end-insert
~~~~~~~

Nach dem Rendern verwenden wir die `debug`-Funktion des `screen`-Objekt:

{title="src/App.test.js",lang="javascript"}
~~~~~~~
describe('Item', () => {
  test('renders all properties', () => {
    render(<Item item={storyOne} />);

# leanpub-start-insert
    screen.debug();
# leanpub-end-insert
  });
});
~~~~~~~

Sobald du den Tests mit `npm test` aufrufst, siehst du die Ausgabe der `debug`-Funktion. Alle HTML-Elemente der Komponente (und der untergeordneten) werden ausgegeben. Hinweis: Aktualisiere die [Versionen von RTL](https://github.com/the-road-to-learn-react/hacker-stories/compare/hs/react-testing-unit-function...hs/react-testing-unit-component?expand=1) in der Datei `package.json` und führe `npm install` erneut aus, falls du Probleme hast. Dies ist die konkrete Ausgabe:

{title="src/App.test.js",lang="javascript"}
~~~~~~~
<body>
  <div>
    <div>
      <span>
        <a
          href="https://reactjs.org/"
        >
          React
        </a>
      </span>
      <span>
        Jordan Walke
      </span>
      <span>
        3
      </span>
      <span>
        4
      </span>
      <span>
        <button
          type="button"
        >
          Dismiss
        </button>
      </span>
    </div>
  </div>
</body>
~~~~~~~

Es ist hilfreich, die `debug`-Funktion zu verwenden, wenn du eine neue Komponente in einem Komponententest renderst. Dies gibt dir den perfekten Überblick über das, was gerendert wird. Hierauf aufbauend integrierst du die nächsten Tests. Erstelle weitere Behauptungen oder Assertions. Das `screen`-Objekt bietet dir eine Funktion namens `getByText`. Dies ist eine von vielen, die dich bei der Testentwicklung unterstützen:

{title="src/App.test.js",lang="javascript"}
~~~~~~~
describe('Item', () => {
  test('renders all properties', () => {
    render(<Item item={storyOne} />);

# leanpub-start-insert
    expect(screen.getByText('Jordan Walke')).toBeInTheDocument();
    expect(screen.getByText('React')).toHaveAttribute(
      'href',
      'https://reactjs.org/'
    );
# leanpub-end-insert
  });
});
~~~~~~~

Für die beiden Behauptungen (Assertions) verwenden wir zwei neue Funktionen: `toBeInTheDocument` und `toHaveAttribute`. Um zu überprüfen, ob ein Element mit dem Text "Jordan Walke" und ob eines mit dem Text "React" inklusive `href` Attribut im Dokument vorhanden ist.

Die Funktion `getByText` findet die Elemente mit den sichtbaren Texten "Jordan Walke" und "React". Verwende Äquivalent `getAllByText`, wenn du nach mehr als einem suchst. RTL ist hier intuitiv aufgebaut. Für viele Funktionen ist diese Namenswahl für die Anwendung auf ein oder alle Elemente entsprechend implementiert.

`getByText` gibt das Element mit einem Text zurück, den Benutzer beim Aufruf der Anwendung sehen. Andererseits ist es aussagekräftig, wie Nutzer die App verwenden. `getByText` ist nicht die einzige Suchfunktion. Weitere häufig verwendete sind `getByRole` oder `getAllByRole`:

{title="src/App.test.js",lang="javascript"}
~~~~~~~
describe('Item', () => {
  test('renders all properties', () => {
    ...
  });

# leanpub-start-insert
  test('renders a clickable dismiss button', () => {
    render(<Item item={storyOne} />);

    expect(screen.getByRole('button')).toBeInTheDocument();
  });
# leanpub-end-insert
});
~~~~~~~

Die Funktion `getByRole` wird zum Abrufen von Elementen anhand von [Aria-Label-Attributen](https://developer.mozilla.org/de/docs/Web/Accessibility/ARIA/ARIA_Techniques/Using_the_aria-label_attribute) verwendet. Es gibt [implizite Rollen für HTML-Elemente](https://developer.mozilla.org/de/docs/Web/Accessibility/ARIA/Roles) --- ähnliche Schaltflächen für ein Schaltflächenelement. So wählst du Elemente nicht nur nach sichtbarem Text aus, sondern nach ihrer Barrierefreiheit. Eine erwähnenswerte Funktion von `getRoleBy` ist, dass [Rollen vorgeschlagen werden](https://twitter.com/rwieruch/status/1260912349978013696). `getByText` und `getByRole` sind die am häufigsten verwendeten Suchfunktionen von RTL.

Der nächste Schritt ist, nicht nur zu behaupten, dass ein Text *im Dokument* vorhanden ist, sondern, dass Ereignisse wie erwartet funktionieren. Klicke beispielsweise auf das Schaltflächenelement der Item-Komponente, und prüfe, ob der Callback-Handler `handleRemoveItem` aufgerufen wird. Hierfür verwenden wir Jest. Wir erstellen die Funktion `handleRemoveItem` mit `const handleRemoveItem = jest.fn();`. Nachdem du ein Klickereignis mit RTL auf der Schaltfläche ausgelöst hast, prüfen wir, ob `handleRemoveItem` aufgerufen wurde. `fireEvent.click(screen.getByRole('button'));` ist eine unkomplizierte Variante die wir einsetzen, wenn wir nur eine Schaltfläche auf der Site haben da `screen.getByRole('button')` andernfalls mehr als ein Element zurückgibt:

{title="src/App.test.js",lang="javascript"}
~~~~~~~
describe('Item', () => {
  test('renders all properties', () => {
    ...
  });

  test('renders a clickable dismiss button', () => {
    ...
  });

# leanpub-start-insert
  test('clicking the dismiss button calls the callback handler', () => {
    const handleRemoveItem = jest.fn();

    render(<Item item={storyOne} onRemoveItem={handleRemoveItem} />);

    fireEvent.click(screen.getByRole('button'));

    expect(handleRemoveItem).toHaveBeenCalledTimes(1);
  });
# leanpub-end-insert
});
~~~~~~~

Mit Jest übergeben wir testspezifische Funktionen als Eigenschaft (Props) an die Item-Komponente. Diese heißen **spy**, **stub**, oder **mock**; Jedes wird für verschiedene Testszenarien verwendet. `jest.fn()` gibt uns ein *mock* für die eigentliche Funktion zurück, aus dem wir auslesen, wann und wie sie aufgerufen wird. So verwenden wir Jest-Assertions wie `toHaveBeenCalledTimes`, um die Anzahl der Aufrufe der Funktion zu bestätigen, oder `toHaveBeenCalledWith`, um die übergebenen Argumente zu überprüfen.

Immer wenn wir eine JavaScript-Funktion prüfen, verwenden wir Jest, um eine Funktion zu erstellen --- unabhängig davon, ob wir testen wie oft oder mit welchen Argumenten sie aufgerufen wurde. Nachdem diese implizit mit `fireEvent` von RTL ausgelöst wurde, behaupten wir, dass der bereitgestellte Callback-Handler --- welche die Mock-Funktion ist --- einmal aufgerufen wurde.

Bei den letzten Tests haben wir die Eingabe und Ausgabe der Item-Komponente über Rendering und Assertions getestet. Wir untersuchen keine realen Statusänderungen, da nach dem Klicken auf die Schaltfläche "Dismiss" kein tatsächliches Element aus dem DOM entfernt wird. Die Logik zum Entfernen aus der Liste ist in der App-Komponente und wir prüfen Item hier isoliert. Später beim Testen der App werden wir die eigentliche Implementierungslogik zum Entfernen eines Elements überprüfen.

Fahren wir mit der SearchForm-Komponente fort, unter der InputWithLabel angeordnet ist. Zunächst Rendern wir diese mithilfe ihrer Eigenschaften (Props):

{title="src/App.test.js",lang="javascript"}
~~~~~~~
# leanpub-start-insert
describe('SearchForm', () => {
  const searchFormProps = {
    searchTerm: 'React',
    onSearchInput: jest.fn(),
    onSearchSubmit: jest.fn(),
  };

  test('renders the input field with its value', () => {
    render(<SearchForm {...searchFormProps} />);

    screen.debug();
  });
});
# leanpub-end-insert
~~~~~~~

Wir verschaffen uns wieder mithilfe des Debuggens einen Überblick. Danach stellen wir die erste Behauptung (Assertion) für das Suchformular auf. Im Falle von Eingabefeldern ist die Funktion "getByDisplayValue" der perfekte Kandidat. Diese gibt das Feld als Element zurückzugeben. Mit folgendem Test stellen wir sicher, dass das Suchformular beim ersten Aufruf der Anwendung gerendert wird und das Eingabefeld den Text "React" enthält:

{title="src/App.test.js",lang="javascript"}
~~~~~~~
describe('SearchForm', () => {
  const searchFormProps = { ... };

  test('renders the input field with its value', () => {
    render(<SearchForm {...searchFormProps} />);

# leanpub-start-insert
    expect(screen.getByDisplayValue('React')).toBeInTheDocument();
# leanpub-end-insert
  });
});
~~~~~~~

Da das Eingabefeld mit einem Standardwert gerendert wird, verwenden wir diesen in unserer Testbehauptung. Wäre dies nicht der Fall, zeigte das Feld unter Umständen einen Platzhalter mit dem HTML-Attribut `placeholder` an. Dann könnten wir eine andere Funktion von RTL namens `getByPlaceholderText` verwenden, um ein Element mit einem Platzhaltertext zu suchen.

Da die Debug-Informationen uns viele Optionen zum Abfragen unseres HTML-Codes bietet, fahren wir mit einem weiteren Test fort, um das gerenderte Label zu testen:

{title="src/App.test.js",lang="javascript"}
~~~~~~~
describe('SearchForm', () => {
  const searchFormProps = { ... };

  test('renders the input field with its value', () => {
    ...
  });

# leanpub-start-insert
  test('renders the correct label', () => {
    render(<SearchForm {...searchFormProps} />);

    expect(screen.getByLabelText(/Search/)).toBeInTheDocument();
  });
# leanpub-end-insert
});
~~~~~~~

Mit der Funktion `getByLabelText` finden wir ein Element anhand einer Bezeichnung in einem Formular. Dies ist praktisch für Komponenten, die über mehrere Labels und HTML-Steuerelemente verfügen. Hast du bemerkt, wie wir hier den [regulären Ausdruck](https://developer.mozilla.org/de/docs/Web/JavaScript/Guide/Regular_Expressions) `/Search/` nutzen? Wenn wir stattdessen eine normale Zeichenfolge verwendet hätten, müssten wir den Doppelpunkt für "Suche:" einfügen. Durch die Verwendung dieses regulären Ausdrucks werden Strings abgeglichen, die die Zeichenfolge "Search" enthalten. Das macht das Auffinden von Elementen wesentlich komfortabler. Falls du bisher keine regulären Ausdrücke verwendest, sieh dir diese einmal an. Sie erleichtern nach einer Einarbeitungszeit in der Regel das Programmieren enorm.

Auf jeden Fall testen wir die interaktiven Teile von SearchForm. Da der Callback-Handler, der als Eigenschaften (Props) an die SearchForm-Komponente übergeben wird, mit Jest durch eine Mock-Funktion ersetzt ist, prüfen wir, ob diese aufgerufen wird --- `toHaveBeenCalledTimes` kennst du schon:

{title="src/App.test.js",lang="javascript"}
~~~~~~~
describe('SearchForm', () => {
  const searchFormProps = {
    searchTerm: 'React',
    onSearchInput: jest.fn(),
    onSearchSubmit: jest.fn(),
  };

  ...

# leanpub-start-insert
  test('calls onSearchInput on input field change', () => {
    render(<SearchForm {...searchFormProps} />);

    fireEvent.change(screen.getByDisplayValue('React'), {
      target: { value: 'Redux' },
    });

    expect(searchFormProps.onSearchInput).toHaveBeenCalledTimes(1);
  });

  test('calls onSearchSubmit on button submit click', () => {
    render(<SearchForm {...searchFormProps} />);

    fireEvent.submit(screen.getByRole('button'));

    expect(searchFormProps.onSearchSubmit).toHaveBeenCalledTimes(1);
  });
# leanpub-end-insert
});
~~~~~~~

Ähnlich wie bei der Item-Komponente haben wir die Eingabe (Props) und die Ausgabe (Callback-Handler) für SearchForm getestet. Der Unterschied besteht darin, dass SearchForm eine untergeordnete Komponente namens InputWithLabel rendert. Wenn du die Debug-Ausgabe erneut überprüfst, wirst du feststellen, dass die React Testing Library sich nicht für diese untergeordnete Komponente interessiert. Dies liegt daran, dass sich ein Benutzer an der Stelle ebenfalls nicht für sie interessiert. Daher gibt die React Testing Library nur den HTML-Code für uns aus.

Wir haben gesehen, dass alle Callback-Handler-Tests für Item- und SearchForm nur prüfen, ob die Funktionen aufgerufen wurden. Hier findet kein React-Re-Rendering statt, da alle Komponenten isoliert ohne Statusverwaltung getestet werden. Daher setzt der eigentliche Test mit RTL weiter oben im Komponentenbaum an, wo Zustandsänderungen und Seiten-Effekte bewertet werden.

### Übungen:

* Begutachte den [Quellcode dieses Abschnittes](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/react-testing-unit-component).
  * Reflektiere die [Änderungen gegenüber dem Stand der Anwendung am Ende des ersten Kapitels](https://github.com/the-road-to-learn-react/hacker-stories/compare/hs/react-testing-unit-function...hs/react-testing-unit-component?expand=1).
* Lese mehr über die [React Testing Library](https://testing-library.com/docs/react-testing-library/intro).
  * Lese mehr über [Suchfunktionen](https://testing-library.com/docs/guide-which-query).
* Füge Tests für Ihre List- und InputWithLabel-Komponenten hinzu.

### Integration Testing: Component

Die React Testing Library basiert auf einer Kernphilosophie: Anstatt die Implementierungsdetails der React-Komponenten zu prüfen, testen wir, wie ein Benutzer mit unserer Anwendung interagieren würde und ob bei der Interaktion alles wie erwartet funktioniert. Dies ist effektiv und gilt insbesondere für Integrationstests.

Um die App-Komponente zu testen, sind Daten erforderlich, da die App nach dem ersten Rendern eine Anforderung an die Remote-API sendet. Deshalb importieren wir Axios und erstellen eine Mock-Funktion, die wir in der App-Komponente für unsere Datenanforderung verwenden:

{title="src/App.test.js",lang="javascript"}
~~~~~~~
...

# leanpub-start-insert
import axios from 'axios';
# leanpub-end-insert

...

# leanpub-start-insert
jest.mock('axios');
# leanpub-end-insert

...
~~~~~~~

Implementiere dann die Daten, die du von einer gemocked API-Anforderung zurückgibst, mit einem Promis-Objekt und verwende diese für das Axios-Mock. Danach rendern wir unsere Komponente und gehen davon aus, dass die richtigen Daten für die API-Anfrage gemocked werden. Probiere es aus! Du wirst auf einen Fehler stoßen. Diesen beheben wir im nächsten Schritt::

{title="src/App.test.js",lang="javascript"}
~~~~~~~
# leanpub-start-insert
describe('App', () => {
  test('succeeds fetching data', () => {
    const promise = Promise.resolve({
      data: {
        hits: stories,
      },
    });

    axios.get.mockImplementationOnce(() => promise);

    render(<App />);

    screen.debug();
  });
});
# leanpub-end-insert
~~~~~~~

Jetzt verwenden wir die "act"-Hilfefunktion, um darauf zu warten, dass unser Versprechen (Promise) nach dem ersten Rendern der Komponente aufgelöst wird. Mit await implementieren wir dies. Das Tolle an der Debug-Funktion von RTL ist, dass sie die Elemente der App-Komponente vor und nach der Anfrage für uns ausgibt. Sie es dir praktisch an, nachfolgend findest du den Code dazu:

{title="src/App.test.js",lang="javascript"}
~~~~~~~
describe('App', () => {
# leanpub-start-insert
  test('succeeds fetching data', async () => {
# leanpub-end-insert
    const promise = Promise.resolve({
      data: {
        hits: stories,
      },
    });

    axios.get.mockImplementationOnce(() => promise);

    render(<App />);

    screen.debug();

# leanpub-start-insert
    await act(() => promise);

    screen.debug();
# leanpub-end-insert
  });
});
~~~~~~~

Wenn wir die Ausgabe des Debugs auswerten, sehen wir, dass der Ladeindikator `Loading ...` für die erste Debug-Funktion gerendert wird, für die zweite aber nicht. Dies liegt daran, dass das Abrufen der Daten und das erneute Rendern der Komponente abgeschlossen ist, nachdem wir das Versprechen in unserem Test mit `act` gelöst haben. Testen wir den Ladeindikator für den zweiten Fall:

{title="src/App.test.js",lang="javascript"}
~~~~~~~
describe('App', () => {
  test('succeeds fetching data', async () => {
    const promise = Promise.resolve({
      data: {
        hits: stories,
      },
    });

    axios.get.mockImplementationOnce(() => promise);

    render(<App />);

# leanpub-start-insert
    expect(screen.queryByText(/Loading/)).toBeInTheDocument();
# leanpub-end-insert

    await act(() => promise);

# leanpub-start-insert
    expect(screen.queryByText(/Loading/)).toBeNull();
# leanpub-end-insert
  });
});
~~~~~~~

Wir werden dieses Mal `queryByText` anstelle von `getByText` verwenden, da wir testen, ob ein zurückgegebenes Element nicht mehr vorhanden ist. Wenn wir `getByText` verwendeten, sähen wir eine Fehlermeldung, da nichts gefunden wird. Aber mit `queryByText` erhalten wir nur einen Fehler, wenn kein Element vorhanden ist.

Wieder verwenden wir einen regulären Ausdruck `/Loading/` anstelle eines Strings `'Loading'`. Wenn wir eine Zeichenfolge verwendeten, wäre es erforderlich, dass wir explizit `'Loading ...'` anstelle von `'Loading'` einsetzen. So ist es unkomplizierter.

Als Nächstes prüfen wir, ob die abgerufenen Daten wie erwartet gerendert werden:

{title="src/App.test.js",lang="javascript"}
~~~~~~~
describe('App', () => {
  test('succeeds fetching data', async () => {
    const promise = Promise.resolve({
      data: {
        hits: stories,
      },
    });

    axios.get.mockImplementationOnce(() => promise);

    render(<App />);

    expect(screen.queryByText(/Loading/)).toBeInTheDocument();

    await act(() => promise);

    expect(screen.queryByText(/Loading/)).toBeNull();

# leanpub-start-insert
    expect(screen.getByText('React')).toBeInTheDocument();
    expect(screen.getByText('Redux')).toBeInTheDocument();
    expect(screen.getAllByText('Dismiss').length).toBe(2);
# leanpub-end-insert
  });
});
~~~~~~~

So wie wir es gerne hätten, haben wir es jetzt getestet. Auf ähnliche Art und Weise testen wir den Fehlerfall --- eine fehlgeschlagene API-Anfrage. Das einzige, was wir ändern, ist, dass die Promise abgelehnt wird und dass der Fehler mit einem Try/Catch-Block abgefangen wird:

{title="src/App.test.js",lang="javascript"}
~~~~~~~
describe('App', () => {
  test('succeeds fetching data', async () => {
    ...
  });

# leanpub-start-insert
  test('fails fetching data', async () => {
    const promise = Promise.reject();

    axios.get.mockImplementationOnce(() => promise);

    render(<App />);

    expect(screen.getByText(/Loading/)).toBeInTheDocument();

    try {
      await act(() => promise);
    } catch (error) {
      expect(screen.queryByText(/Loading/)).toBeNull();
      expect(screen.queryByText(/went wrong/)).toBeInTheDocument();
    }
  });
# leanpub-end-insert
});
~~~~~~~

Stellst du dir die Frage, wann du `getBy` und wann `queryBy` verwendest? Als Faustregel: Verwende `getBy` für einzelne und `getAllBy` für mehrere Elemente. Wenn du nach Elementen suchen, die nicht unter Umständen nicht vorhanden sind, nutze `queryBy` (oder `queryAllBy`). Der Einfachheit halber habe ich in diesem Beispiel `queryBy` für alle Elemente verwendet.

Wir wissen, dass das anfängliche Abrufen von Daten jetzt für unsere App-Komponente funktioniert. Als Nächstes testen wir die Benutzerinteraktionen, die wir zuvor nur in den untergeordneten Komponenten getestet haben, indem wir Ereignisse ohne Status und Seiten-Effekte ausgelöst haben. Wir entfernen ein Element aus der Liste, nachdem die Daten erfolgreich abgerufen wurden. Da "Jordan Walke" unser erster gerenderter Artikel in der Liste ist, wird er entfernt, wenn wir auf die erste "Dismiss"-Schaltfläche klicken:

{title="src/App.test.js",lang="javascript"}
~~~~~~~
describe('App', () => {

  ...

# leanpub-start-insert
  test('removes a story', async () => {
    const promise = Promise.resolve({
      data: {
        hits: stories,
      },
    });

    axios.get.mockImplementationOnce(() => promise);

    render(<App />);

    await act(() => promise);

    expect(screen.getAllByText('Dismiss').length).toBe(2);
    expect(screen.getByText('Jordan Walke')).toBeInTheDocument();

    fireEvent.click(screen.getAllByText('Dismiss')[0]);

    expect(screen.getAllByText('Dismiss').length).toBe(1);
    expect(screen.queryByText('Jordan Walke')).toBeNull();
  });
# leanpub-end-insert
});
~~~~~~~

Als Nächstes werden wir die Suchfunktion testen. Wir richten das Mock etwas anders als zuvor ein, weil wir nicht nur die anfängliche Suchanfrage beachten. Wir decken zusätzlich potentielle zukünftige Suchen ab:

{title="src/App.test.js",lang="javascript"}
~~~~~~~
describe('App', () => {

  ...

# leanpub-start-insert
  test('searches for specific stories', async () => {
    const reactPromise = Promise.resolve({
      data: {
        hits: stories,
      },
    });

    const anotherStory = {
      title: 'JavaScript',
      url: 'https://en.wikipedia.org/wiki/JavaScript',
      author: 'Brendan Eich',
      num_comments: 15,
      points: 10,
      objectID: 3,
    };

    const javascriptPromise = Promise.resolve({
      data: {
        hits: [anotherStory],
      },
    });

    axios.get.mockImplementation(url => {
      if (url.includes('React')) {
        return reactPromise;
      }

      if (url.includes('JavaScript')) {
        return javascriptPromise;
      }

      throw Error();
    });
  });
# leanpub-end-insert
});
~~~~~~~

Anstatt die Anfrage einmal mit Jest (`mockImplementationOnce`) zu mocken, mocken wir jetzt mehrere Anfragen (` mockImplementation`). Abhängig von der eingehenden URL gibt die Anforderung entweder die ursprüngliche Liste ("React" -bezogene Storys) oder die neue Liste ("JavaScript"-bezogene Storys) zurück. Für den Fall, dass wir eine falsche URL für die Anfrage angeben, wird beim Test ein Fehler ausgegeben, um eine Bestätigung zu erhalten, dass wir den Test nicht korrekt eingerichtet haben. Lasse uns wie zuvor die App-Komponente rendern:

{title="src/App.test.js",lang="javascript"}
~~~~~~~
describe('App', () => {

  ...

  test('searches for specific stories', async () => {
    const reactPromise = Promise.resolve({ ... });

    const anotherStory = { ... };

    const javascriptPromise = Promise.resolve({ ... });

    axios.get.mockImplementation((url) => {
      ...
    });

# leanpub-start-insert
    // Initial Render

    render(<App />);

    // First Data Fetching

    await act(() => reactPromise);

    expect(screen.queryByDisplayValue('React')).toBeInTheDocument();
    expect(screen.queryByDisplayValue('JavaScript')).toBeNull();

    expect(screen.queryByText('Jordan Walke')).toBeInTheDocument();
    expect(
      screen.queryByText('Dan Abramov, Andrew Clark')
    ).toBeInTheDocument();
    expect(screen.queryByText('Brendan Eich')).toBeNull();
# leanpub-end-insert
  });
});
~~~~~~~

Wir lösen das erste Versprechen für das anfängliche Rendern und erwarten, dass das Eingabefeld "React" und die beiden Elemente in der Liste die Ersteller von React und Redux rendert. Wir stellen außerdem sicher, dass keine JavaScript-bezogene Storys gerendert werden. Als Nächstes ändern wir den Wert des Eingabefelds, indem wir ein Ereignis auslösen und festlegen, ob der neue Wert der App über alle untergeordneten Komponenten im tatsächlichen Eingabefeld gerendert wird:

{title="src/App.test.js",lang="javascript"}
~~~~~~~
describe('App', () => {

  ...

  test('searches for specific stories', async () => {

    ...

    expect(screen.queryByText('Jordan Walke')).toBeInTheDocument();
    expect(
      screen.queryByText('Dan Abramov, Andrew Clark')
    ).toBeInTheDocument();
    expect(screen.queryByText('Brendan Eich')).toBeNull();

# leanpub-start-insert
    // User Interaction -> Search

    fireEvent.change(screen.queryByDisplayValue('React'), {
      target: {
        value: 'JavaScript',
      },
    });

    expect(screen.queryByDisplayValue('React')).toBeNull();
    expect(
      screen.queryByDisplayValue('JavaScript')
    ).toBeInTheDocument();
# leanpub-end-insert
  });
});
~~~~~~~

Zuletzt senden wir diese Suchanforderung, indem wir die Schaltfläche klicken. Der neue Suchbegriff wird aus dem Status der App-Komponente verwendet. Daher wird nun nach JavaScript-bezogenen Geschichten gesucht, die wir zuvor gemocked haben:

{title="src/App.test.js",lang="javascript"}
~~~~~~~
describe('App', () => {

  ...

  test('searches for specific stories', async () => {

    ...

    expect(screen.queryByDisplayValue('React')).toBeNull();
    expect(
      screen.queryByDisplayValue('JavaScript')
    ).toBeInTheDocument();

# leanpub-start-insert
    fireEvent.submit(screen.queryByText('Submit'));

    // Second Data Fetching

    await act(() => javascriptPromise);

    expect(screen.queryByText('Jordan Walke')).toBeNull();
    expect(
      screen.queryByText('Dan Abramov, Andrew Clark')
    ).toBeNull();
    expect(screen.queryByText('Brendan Eich')).toBeInTheDocument();
# leanpub-end-insert
  });
});
~~~~~~~

Jetzt wird Brendan Eich als Schöpfer von JavaScript gerendert, während die anderen nicht mehr angezeigt werden. Der letzte Test hat gezeigt, wie du ein Testszenario in einem Testfall darstellst. Mit unseren Tools durchlaufen wir jeden Schritt --- das anfängliche Abrufen, das Ändern des Werts des Eingabefelds, das Senden des Formulars und das Abrufen neuer Daten von der API.

Die React Testing Library und Jest sind in Kombination der Status Quo, beim Testen von React-Anwendungen. Während RTL dir alle React-relevanten Testtools bietet, wird Jest mit dem allgemeinen Testframework für Testsuiten, Testfälle, Assertions und VMock-Funktionen geliefert. Der Vollständigkeit halber: Eine beliebte Alternative zu RTL ist [Enzyme] (https://www.robinwieruch.de/react-testing-jest-enzyme) von Airbnb.

### Exercises:

* Begutachte den [Quellcode dieses Abschnittes](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/react-testing-integration).
  * Reflektiere die [Änderungen gegenüber dem Stand der Anwendung am Ende des vorherigen Kapitels](https://github.com/the-road-to-learn-react/hacker-stories/compare/hs/react-testing-unit-component...hs/react-testing-integration?expand=1).
* Lese mehr zu [E2E-Tests in React](https://www.robinwieruch.de/react-testing-cypress).
* Halten deine Tests grün und füge neue hinzu während du weiter im Buch arbeitetst.

### Snapshot Testing

Facebook kam unabhängig von der Testpyramide auf die Idee, Schnappschuss-Tests in React durchzuführen. Ziel war ein leichterer Weg zum Testen von Komponenten und ihrer Struktur. Grundsätzlich erstellt ein Snapshot-Test eine Momentaufnahme der Ausgabe einer gerenderten Komponente. Diese wird verwendet, um sie mit Momentaufnahmen zu vergleichen, die erstellt werden, wenn du den Test erneut aufrufst. Wenn sich die Ausgabe der Komponente geändert hat, wird die Differenz beider Snapshots angezeigt und der Snapshot-Test schlägt fehl. Ergo: Der Snapshot-Test informiert, wenn sich die Ausgabe der Komponente ändert. Falls ein Snapshot-Test fehlschlägt, hast du zwei Möglichkeiten: Du akzeptierst das Ergebnis oder du lehnst es ab. Bei bestätigst du die Abweichung. Bei Letzterem korrigierst du die Implementierung der Komponente.

Durch die Verwendung von Snapshot-Tests hältst du deine Tests leichtgewichtig. Du testest, ohne die Implementierungsdetails der Komponente zu kennen. Sehen wir uns  das praktisch an. Als Nächstes führen wir einen Snapshot-Test für SearchForm durch:

{title="src/App.test.js",lang="javascript"}
~~~~~~~
describe('SearchForm', () => {

  ...

# leanpub-start-insert
  test('renders snapshot', () => {
    const { container } = render(<SearchForm {...searchFormProps} />);
    expect(container.firstChild).toMatchSnapshot();
  });
# leanpub-end-insert
});
~~~~~~~

Nachdem du den Tests mit `npm test` aufgerufen hast, ist in deinem Projekt ein neuer Ordner *src/__snapshots__* vorhanden. Navigiere in diesen und überprüfen die Datei, die gespeichert wurde. Ähnlich wie bei der `debug`-Funktion von RTL ist es eine Momentaufnahme von SearchForm in HTML. Navigiere jetzt zu deiner *src/App.js*-Datei und änder etwas. Entferne beispielsweise in SearchForm den fett gedruckten Text:

{title="src/App.js",lang="javascript"}
~~~~~~~
const SearchForm = ({
  searchTerm,
  onSearchInput,
  onSearchSubmit,
}) => (
  <form onSubmit={onSearchSubmit}>
    <InputWithLabel
      id="search"
      value={searchTerm}
      isFocused
      onInputChange={onSearchInput}
    >
# leanpub-start-insert
      Search:
# leanpub-end-insert
    </InputWithLabel>

    <button type="submit" disabled={!searchTerm}>
      Submit
    </button>
  </form>
);
~~~~~~~

Nachdem du den Tests erneut aufgerufen hast siehst du in der Befehlszeile unter anderem Folgendes:

{title="Command Line",lang="text"}
~~~~~~~
- Snapshot
+ Received

    <label
      for="search"
    >
-     <strong>
-       Search:
-     </strong>
+     Search:
    </label>
~~~~~~~

Dies ist der typische Fall für einen Schnappschuss-Test. Wann immer sich die HTML-Struktur einer Komponente ändert, wirst du in Ihrem Snapshot-Test in der Befehlszeile darüber informiert. Entweder du akzeptierst die Änderung: Korrigiere in diesem Fall die Datei *src/App.js*. Wenn du den Korrekturhinweis nicht annimmst, drücke „u“ und ein neuer korrigierter Snapshot wird erstellt. Probiere es selbst aus und sieh, wie die Snapshot-Datei im Ordner *src/__snapshots__* die Änderungen anwendet.

Jest speichert Snapshots in einem Ordner, damit der Unterschied zu zukünftigen Snapshot-Tests überprüft wird. Benutzer geben diese Snapshots für die Versionskontrolle (z. B. Git) teamübergreifend frei. Wenn du zum ersten Mal einen Snapshot-Test aufrufst, wird die Snapshot-Datei im Ordner deines Projekts erstellt. Wenn der Test erneut aufgerufen wird, wird erwartet, dass der Snapshot mit der Version aus dem letzten Testlauf übereinstimmt. So stellen wir sicher, dass das DOM gleich bleibt --- beziehungsweise Änderungen überprüft werden.

Snapshot-Tests eignen sich, um Tests in React schnell zum Laufen zu bringen. Du solltest aber nicht für jede Komponente Snapshot-Tests durchzuführen. Allgemein wird empfohlen, Snapshot-Tests für Elemente zu verwenden, die nicht häufig aktualisiert werden und nicht zu komplex sind. In diesem Falle sind Snapshot-Tests das Werkzeug der Wahl.

### Übungen:

* Begutachte den [Quellcode dieses Abschnittes](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/react-snapshot).
  * Reflektiere die [Änderungen gegenüber dem Stand der Anwendung am Ende des vorherigen Kapitels](https://github.com/the-road-to-learn-react/hacker-stories/compare/hs/react-testing-integration...hs/react-snapshot?expand=1).
* Ergänze für jede Komponente einen Snapshot-Test und überprüfe den Inhalt des Ordners *__snapshots__*.