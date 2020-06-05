## React Anwendungen testen

Das Testen des Quellcodes wird oft vernachlässigt. Dabei ist es wesentlich und zwingend erforderlich. Es ist unentbehrlich, die Qualität und Funktionalität des Codes automatisch zu überprüfen, bevor dieser im Echtsystem angewendet wird. Automatisierte Tests sind schneller durchführbar, fehlerfreier und kostengünstiger. Maschinen sind hier dem Menschen überlegen. Die [Testpyramide](https://martinfowler.com/articles/practical-test-pyramid.html) dient als Grundregel.

Die Testpyramide umfasst End-to-End-Tests (E2E), Integrationstests und Komponententests (Unit-Tests). Letztere werden für kleine, isolierte Codeblöcke verwendet, zum Beispiel eine einzelne Funktion oder Komponente. Man betrachtet hierbei einen Teilaspekt herausgelöst. Integrationstests helfen uns, herauszufinden, ob diese Einheiten zusammenarbeiten. Ein End-to-End-Test simuliert ein reales Szenario, beispielsweise den Anmeldevorgang einer Webanwendung. Unit-Tests laufen schnell ab, sind isolierte Einheiten und somit unkompliziert zu schreiben und zu warten. Auf End-to-End-Tests trifft das Gegenteil zu.

![](images/testing-pyramid.png)

Scheiben wir zuerst Unit-Tests, die unsere Funktionen und Komponenten abdecken. Danach verwenden wir Integrationstests, um sicherzustellen, dass die einzelnen mit Unit-Tests unabhängig geprüften Einheiten, wie erwartet zusammenarbeiten. Am Ende benötigen wir End-to-End-Tests, um kritische Szenarien zu simulieren. In diesem Kapitel behandeln wir **Unit und Integrationstests** sowie eine komponentenspezifische Testtechnik namens **Schnappschusstest oder Snapshot Test**. **End-to-End-Tests** werden Teil der Übungen sein.

Da es viele Test-Bibliotheken gibt, ist es als Anfänger schwierig, die passende auszuwählen. Wir werden [Jest](https://jestjs.io/) und die [React Testing Library](https://testing-library.com/) (RTL) verwenden. Während Jest ein umfassendes Testframework mit Testrunner, Testsuiten, Testfällen und Assertions (Behauptungen) ist, wird die React Testing Library zum Rendern von React-Komponenten, Auslösen von Ereignissen wie Mausklicks und Auswählen von HTML-Elementen aus dem DOM zum Ausführen von Assertions (Behauptungen) verwendet. In den nächsten Abschnitten werden wir beide Tools Schritt für Schritt von der Einrichtung über Unit-Tests bis hin zu Integrationstests untersuchen.

### Testsuiten, Testfälle und Assertions (Behauptungen)

Jest bietet dir alles, was du von einem Test-Framework erwartest. In JavaScript, und in vielen anderen Programmiersprachen, werden häufig Testsuiten und Testfälle verwendet. Während eine Testsuite viele inhaltlich zusammengehörende Testfälle vereint, besteht ein Testfall aus einem Szenario. Sehen wir uns das mit Jest anhand der Datei *src/App.test.js* praktisch an:

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

Hast du schon einmal mit Tests gearbeitet und kennst "it"-Blocks? Ein "it"-Block ist das gleiche wie ein "test"-Block. In der Vergangenheit wurde "it" genutzt. "test" ist neuer und gibt dem Ganzen einen passenderen Namen. Nenne deinen Block so, wie es dir am liebsten ist. 

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

Jest vergleicht alle Dateien mit dem Suffix *test.js* im Dateinamen, wenn der Befehl aufgerufen wird. Erfolgreiche Tests werden grün, fehlgeschlagene rot markiert. Das interaktive Test-Skript überwacht den Code und führt die Tests jedes Mal aus, wenn sich etwas im Code ändert. Es bietet dir zusätzliche Befehle wie das Drücken von "f", um nur fehlgeschlagene, und "a", um alle Tests erneut auszuführen. Probiere das mit einem fehlerhaften Test praktisch aus:

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

Wenn die Ausführung von npm test bei dir weiterhin aktiv ist, dann werden deine Tests nach der Änderung im Quellcode erneut aufgerufen und du siehts über die Befehlszeilenausgabe einen roten Hinweis bezüglich des fehlgeschlagenen Tests:

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

Mach dich mit der Testausgabe vertraut und nutze sie. Diese enthält alle Informationen, um fehlgeschlagene Tests zu erkennen und so die Ursache zu beheben. Korrigiere den  problematischen Test und überprüfe in der Befehlszeile, ob die Ausgabe grün ist.

Ich habe bisher nichts zu Testaussagen geschrieben, obwohl diese ebenfalls wichtig sind. Du hast im Beispiel zwei Test-Assertions mit der Funktion `expect` verwendet. `expect` ist von Hause aus in den Jest enthalten. Eine Behauptung (Assertion) funktioniert wie folgt: Es wird erwartet, dass die linke Seite (`expect`) mit der rechten (`toBe`) übereinstimmt. `toBe` ist dabei eine von vielen Funktionen, die mit Jest verfügbar sind:

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

Nutze gleichzeitig zwei Befehlszeilen, wenn du mit Tests arbeitest: eine zum Überwachen deiner Tests (`npm start`) und eine zum Entwickeln der Anwendung (`npm start`). Wenn du eine Versionsverwaltung wie [Git](https://de.wikipedia.org/wiki/Git) nutzt, benötigst du unter Umständen eine weitere offene Befehlszeile, um deinen Code zu einem Repository hinzuzufügen.

### Übungen:

* Begutachte den [Quellcode dieses Abschnittes](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/react-testing-setup).
  * Bestätige die [Änderungen gegenüber dem Stand der Anwendung am Ende des ersten Kapitels](https://github.com/the-road-to-learn-react/hacker-stories/compare/hs/react-modern-final...hs/react-testing-setup?expand=1).
* Lese mehr über [Jest](https://jestjs.io/).

### Unit Testing: Functions

Als Erstes schreiben wir einem Komponententest. Ein Unit-Test testet normalerweise eine Komponente oder eine Funktion isoliert. Dies bedeutet, dass wir im Falle einer Funktion nur die Ein- und Ausgabe testen. Bei einer Komponente testen wir die Eigenschaften (Props) und die Callback-Handler.

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

Bevor wir den ersten Unit-Test schreiben, zeige ich dir, wie du eine einzelne JavaScript-Funktion testest. Der beste Kandidat für diesen Testanwendungsfall ist `storiesReducer` und eine ihrer Aktionen. Zunächst definieren wir einige Testdaten --- die wir später ebenfalls verwenden werden --- und die Testsuite:

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

Wenn du die Testfälle genauer untersuchst, erhältst du einen Testfall pro Reduzierungsaktion. Wir werden uns auf eine Aktion konzentrieren. Im Allgemeinen akzeptiert die Reduzierungsfunktion einen Zustand und eine Aktion und gibt einen neuen Zustand zurück. Jeder Test des Reduzierers folgt dem gleichen Muster:

{title="src/App.test.js",lang="javascript"}
~~~~~~~
...

describe('storiesReducer', () => {
  test('removes a story from all stories', () => {
# leanpub-start-insert
    const action = // TODO: some action
    const state = // TODO: some current state

    const newState = storiesReducer(state, action);

    const expectedState = // TODO: the expected state

    expect(newState).toBe(expectedState);
# leanpub-end-insert
  });
});
~~~~~~~

Für unseren speziellen Fall definieren wir eine Aktion `action`, einen Zustand `state` und ein erwartetes Ergebnis `newState`. Letzteres ist im Beispiel wie folgt: Die Liste enthält einen Eintrag weniger, da das Item, das mit er Aktion an den Reduzierer übergeben wird, entfernt wurde:

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

Der Test schlägt fehl. Das liegt daran, dass wir hier `toBe` anstelle von `toStrictEqual` verwenden. Die Funktion `toBe` führt einen [strengen Vergleich](https://developer.mozilla.org/de/docs/Web/JavaScript/Vergleiche_auf_Gleichheit_und_deren_Verwendung) wie `newState === expectedState` durch. Die Objektreferenz ist hier nicht dieselbe, sondern nur der Inhalt des Objekts. Daher vergleichen wir besser mit `toStrictEqual` anstelle von `toBe` den Inhalt:

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

Im Falle von JavaScript-Objekte ist es wichtig, zu unterscheiden, ob du einen strengen Vergleich oder nur einen Inhaltsvergleich testest. Meistens ist der Inhalt relevant, verwende daher `toStrictEqual`. Für Strings oder boolesche Werte nutzt du weiterhin `toBe`. Beachte, dass es eine `toEqual`-Funktion gibt, die [etwas anders funktioniert](https://twitter.com/rwieruch/status/1260866850080067584) als `toStrictEqual`. `toStrictEqual` überprüft zusätzlich, ob zwei Objekte denselben Typ haben.

Kommen wir zurück auf unser Beispiel: Der Reduzierertest schließt erfolgreich ab. Es wird eine JavaScript-Funktion mit einer Eingabe getestet und eine vordefinierte Ausgabe erwartet. Wie erwähnt, folgt eine Reduzierungsfunktion immer demselben Testmuster: Wenn ein Zustand und eine Aktion gegeben sind, erwarten wir ein Ergebnis Jede Aktion des Reduzierers ist ein weiterer Testfall in der Testsuite unseres Reduzierers. Das Testen aller verbleibender Aktionen überlasse ich dir als Übung.

### Übungen:

* Begutachte den [Quellcode dieses Abschnittes](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/react-testing-unit-function).
  * Bestätige die [Änderungen gegenüber dem Stand der Anwendung am Ende des letzten Kapitels](https://github.com/the-road-to-learn-react/hacker-stories/compare/hs/react-testing-setup...hs/react-testing-unit-function?expand=1).
* Lesen Sie mehr über Jests Funktionen wie `toBe` und `toStrictEqual`.

### Unit Testing: Komponenten

Nachdem wir eine Funktion in JavaScript mit Jest getestet haben, testen jetzt isoliert eine Komponente. Wir nutzen die React Testing Library (RTL). Diese ist in der *Create React App* integriert. Wenn du wie ich die *Create React App* nutzt, ist alles schon fertig installiert und konfiguriert. Falls du ein benutzerdefiniertes React-Setup verwendest, beispielsweise React mit Webpack, installierst du die Bibliothek bitte selbst.

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

Testen wir die Item-Komponente. Wichtig ist hier, dass alle Eigenschaften (Props) entsprechend der Eingabe gerendert werden. Mit anderen Worten: Ist die Ausgabe der Eigenschaften im HTML in Bezug zur Eingabe korrekt. Wir verwenden im Test die `render`-Funktion von RTL, um eine React-Komponente im HTML anzuzeigen. In diesem Fall rendern wir Item als Element und übergeben ihm als Eigenschaft (prop) ein `item`-Objekt --- eines unserer zuvor definierten Testobjekte:

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

Nach dem Rendern verwenden wir die `debug`-Funktion des `screen`-Objekt von RTL:

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

Sobald du den Tests mit `npm test` aufrufst, siehst du die Ausgabe der `debug`-Funktion. Alle HTML-Elemente der Komponente (und der untergeordneten) werden ausgegeben. Dies ist die Ausgabe:

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

Es ist hilfreich, die `debug`-Funktion von RTL zu verwenden, wenn du eine neue Komponente in einem Komponententest renderst. Dies gibt dir den perfekten Überblick über das, was gerendert wird. Hierauf aufbauend integrierst du die nächsten Tests. Erstelle weitere Behauptungen oder Assertions. Das `screen`-Objekt von RTL bietet dir eine Funktion namens `getByText`. Dies ist eine von vielen die dich bei der Testentwicklung unterstützen:

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

Für die beiden Behauptungen oder Assertions verwenden wir zwei neue Funktionen: `toBeInTheDocument` und `toHaveAttribute`. Um zu überprüfen, ob ein Element mit dem Text "Jordan Walke" und ob eines mit dem Text "React" im `href` Attributwert im Dokument vorhanden ist.

Die Funktion `getByText` findet das eine Element mit den sichtbaren Texten "Jordan Walke" und "React". Verwende Äquivalent `getAllByText`, wenn du nach mehr als einem suchst. RTL ist hier intuitiv aufgebaut. Für viele Funktionen ist diese Namenswahl für die Anwendung auf ein oder alle Elemente entsprechend implementiert.

Falls du RTL das erste Mal anwendest, ist diese Erklärung hilfreich: Der `getByText` gibt das Element mit einem Text zurück, den Benutzer selbst sehen. Entscheidend ist, wie Nutzer die Anwendung verwenden. `getByText` ist nicht die einzige Suchfunktion. Weitere häufig verwendete sind `getByRole` oder `getAllByRole`:

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

Der nächste Schritt ist, nicht nur zu behaupten, dass ein Text *im Dokument* vorhanden ist, sondern, dass Ereignisse wie erwartet funktionieren. Klicke beispielsweise auf das Schaltflächenelement der Item-Komponente, und prüfe, ob der Callback-Handler aufgerufen wird. Hierfür verwenden wir Jest. Wir erstellen eine Funktion, die wir als Rückruf-Handler für die Item-Komponente bereitstellen. Nachdem du ein Klickereignis mit RTL auf der Schaltfläche ausgelöst hast, prüfen wir, ob die Callback-Handler-Funktion aufgerufen wurde:

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

Mit Jest übergeben wir testspezifische Funktionen als Eigenschaft (prop) an die Item-Komponente. Diese heißen **spy**, **stub**, oder **mock**; Jedes wird für verschiedene Testszenarien verwendet. Das `jest.fn()` gibt uns ein *mock* für die eigentliche Funktion zurück, aus dem wir auslesen, wann und wie sie aufgerufen wird. So verwenden wir Jest-Assertions wie `toHaveBeenCalledTimes`, um die Anzahl der Aufrufe der Funktion zu bestätigen, oder `toHaveBeenCalledWith`, um die übergebenen Argumente zu überprüfen.


Immer wenn wir eine JavaScript-Funktion prüfen, verwenden wir Jest, um eine Funktion zu erstellen --- unabhängig davon, ob wir testen wie oft oder mit welchen Argumenten sie aufgerufen wurde. Nachdem diese implizit mit `fireEvent` von RTL ausgelöst wurde, behaupten wir, dass der bereitgestellte Callback-Handler --- welche die Mock-Funktion ist --- einmal aufgerufen hat.

Ever time we want to spy a JavaScript function, whether it has been called or whether it received certain arguments, we can use Jest's helper function to create a mocked function. Then, after invoking this function implicitly with RTL's `fireEvent` object's function, we can assert that the provided callback handler -- which is the mocked function -- has been called one time.



Bei den letzten Tests haben wir die Eingabe und Ausgabe der Item-Komponente über Rendering und Callback-Handler-Assertions getestet. Wir untersuchen keine realen Statusänderungen, da nach dem Klicken auf die Schaltfläche "Dismiss" kein tatsächliches Element aus dem DOM entfernt wird. Die Logik zum Entfernen aus der Liste ist in der App-Komponente, aber wir prüfen Item hier isoliert. Später beim Testen der App werden wir die eigentliche Implementierungslogik zum Entfernen eines Elements überprüfen.

With the last tests, we have tested the Item component's input and output via rendering assertions and callback handler assertions. However, we are not testing real state changes yet, because there is no actual item removed from the DOM after clicking the "Dismiss" button. The logic to remove the item from the list is in the App component, but we are only testing the Item component in isolation here. We will get to testing the actual implementation logic of removing an Item later when testing the App component.

Fahren wir mit der SearchForm-Komponente fort, unter der InputWithLabel angeordnet ist. Zunächst Rendern wir diese und alle ihre wichtigen Eigenschaften (props):


We will continue with the SearchForm component which uses the InputWithLabel component as child component. As before, we will start with rendering the component with providing all the essential props:

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

Wir verschaffen uns wieder mithilfe des Debuggens einen Überblick. Danach stellen wir die erste Behauptung oder Assertion für das Suchformular auf. Im Falle von Eingabefeldern ist die Funktion "getByDisplayValue" der perfekte Kandidat. Diese gibt das das Feld als Element zurückzugeben:

Again, we start with the debugging. After evaluating what we have rendered here, we can make our first assertion for the SearchForm. In the case of input fields, the `getByDisplayValue` search function is the perfect candidate to return the input field as element:

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

Since the input element gets rendered with a default value, we can make use of this default value (here "React") -- which is the displayed value -- in our test assertion. If the input element wouldn't have a default value, the application could maybe show a placeholder with the `placeholder` HTML attribute on the input field. Then we could use another function from RTL, called `getByPlaceholderText`, for searching an element with a placeholder text.

Because the debug information showed us lots of options to query our HTML, we could continue with one more test to assert the rendered label:

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

Mit der Funktion `getByLabelText` finden wir ein Element anhand einer Bezeichnung in einem Formular. Dies ist praktisch für Komponenten, die über mehrere Labels und HTML-Steuerelemente verfügen. Hast du bemerkt, wie wir hier einen [regulären Ausdruck](https://developer.mozilla.org/de/docs/Web/JavaScript/Guide/Regular_Expressions) nutzen? Wenn wir stattdessen eine Zeichenfolge verwendet hätten, müssten wir den Doppelpunkt für "Suche:" einfügen. Durch die Verwendung eines regulären Ausdrucks werden Strings abgeglichen, die die Zeichenfolge "Suchen" enthalten. Das macht das Auffinden von Elementen wesentlich komfortabler. Falls du bisher keine regulären Ausdrücke verwendest, sieh dir diese einmal an. Sie erleichtern nach einer Einarbeitungszeit in der Regel das programmieren enorm.

Auf jeden Fall testen wir die interaktiven Teile der SearchForm-Komponente. Da der Callback-Handler, der als Eigenschaften (props) an die SearchForm-Komponente übergeben wird, mit Jest durch eine Mock-Funktion ersetzt ist, prüfen wir, ob diese aufgerufen wurden:

The `getByLabelText` search function allows us to find an element by label in a form. This comes in handy for components which rendered multiple labels and HTML controls. However, you may have also seen how we used a [regular expression](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Regular_Expressions) here. If we would have used a string instead, we would need to include the colon for "Search:". However, by using a regular expression, we are matching strings that include the "Search" string, which makes it way more convenient to find elements. You may find yourself more often using regular expression rather than strings.

Anyway, perhaps it would be more interesting to test the interactive parts of the SearchForm component. Since our callback handlers, which are passed as props to the SearchForm component, are already mocked with Jest, we can assert whether these functions et called appropriately:

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

Ähnlich wie bei der Item-Komponente haben wir die Eingabe (Eigenschaften/props) und die Ausgabe (Callback-Handler) für die SearchForm getestet. Der Unterschied besteht darin, dass SearchForm eine untergeordnete Komponente namens InputWithLabel rendert. Wenn du die Debug-Ausgabe erneut überprüfst, wirst du feststellen, dass die React Testing Library sich nicht für diese untergeordnete Komponente interessiert. Dies liegt daran, dass sich ein Benutzer unserer Anwendung an der Stelle ebenfalls nicht für sie interessiert. Daher gibt die React Testing Library nur den HTML-Code für uns aus.

Wir haben gesehen, dass alle Callback-Handler-Tests für Item- und SearchForm-Komponenten nur prüfen, ob die Funktionen aufgerufen wurden. Hier findet kein React-Re-Rendering statt, da alle Komponenten isoliert ohne Statusverwaltung getestet werden, was für uns ausschließlich in der App geschieht. Daher setzt der eigentliche Test mit RTL weiter oben im Komponentenbaum an, wo Zustandsänderungen und Nebenwirkungen bewertet werden.

Similar to the Item component, we tested input (props) and output (callback handler) for the SearchForm component. The difference is that the SearchForm component renders a child component called InputWithLabel. If you check the debug output again, you will see that React Testing Library doesn't care much about this child component. It's because a user of our application wouldn't care about the component as well, so React Testing Library just outputs the HTML for us.

What we have also seen is that all the callback handler tests for Item and SearchForm component only test whether the functions have been called. There is no React re-rendering happening here, because all the components are tested in isolation without state management which solely happens in the App component for us. Therefore, the real testing with RTL starts further up the component tree where state changes and side-effects can be evaluated

### Übungen:


* Begutachte den [Quellcode dieses Abschnittes](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/react-testing-setup).
  * Bestätige die [Änderungen gegenüber dem Stand der Anwendung am Ende des ersten Kapitels](https://github.com/the-road-to-learn-react/hacker-stories/compare/hs/react-modern-final...hs/react-testing-setup?expand=1).
* Lese mehr über [Jest](https://jestjs.io/).

* Begutachte den [Quellcode dieses Abschnittes](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/react-testing-unit-component).
  * Bestätige die [Änderungen gegenüber dem Stand der Anwendung am Ende des ersten Kapitels](https://github.com/the-road-to-learn-react/hacker-stories/compare/hs/react-testing-unit-function...hs/react-testing-unit-component?expand=1).
* Lese mehr über die [React Testing Library](https://testing-library.com/docs/react-testing-library/intro).
  * Lese mehr über [Suchfunktionen](https://testing-library.com/docs/guide-which-query).
* Füge Tests für Ihre List- und InputWithLabel-Komponenten hinzu.

### Integration Testing: Component

Die React Testing Library basiert auf einer Kernphilosophie: Anstatt die Implementierungsdetails der React-Komponenten zu prüfen, testen wir, wie ein Benutzer mit unserer Anwendung interagieren würde und ob bei der Interaktion alles wie erwartet funktioniert. Dies ist effektiv für Integrationstests.

Um die App-Komponente zu testen, sind Daten erforderlich, da die App nach dem ersten Rendern eine Daten-Anforderung an die Remote-API sendet. Importieren wir Axios und erstellen eine Mock-Funktion, die wir in der App-Komponente für unsere Datenanforderung verwenden:

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

Second, implement the data you want to return from a mocked API request with a JavaScript Promise and use it for the axios mock. Afterward, we can render our component and assume that the correct data is mocked for our API request:

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

Now we use React Testing Library's `act` helper function to wait for our promise to resolve after the component rendered for the first time. With async/await, we can implement this like synchronous code. The great thing about the `debug` function from RTL is that it will output the App component's elements before and after the request for us:

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

Evaluating the output from debug, we can see that the loading indicator is rendered for the first debug function but absent for the second debug function. This is because the data fetching and component re-render is complete after we resolve the promise in our test with `act`. Let's assert the loading indicator for this case:

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

We will be using RTL's `queryByText` instead of `getByText` function this time, because we are testing for a returned element not being there anymore. If we would test this with `getByText`, we would get an error, because the element wouldn't be found. But with `queryByText` we just get `null` if no element is there.

Again, we are using a regular expression `/Loading/` instead of a string `'Loading'`. If we would want to use a string, we would have to be explicit by using `'Loading ...'` instead of `'Loading'`. With a regular expression, we are not forced to provide the whole content of the string but just matching a part of it.

Next, we can assert whether our fetched data gets rendered as expected:

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

The happy path for the data fetching is tested now. Similar to it, we can test the *unhappy path* in case of a failing API request. The only thing we need to change is that the promise needs to reject and that the error needs to be caught with a try/catch block:

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

You may notice that it becomes a bit fuzzy here when to use the `getBy` and when to use the `queryBy` search variants. As rule of thumb, use `getBy` for single elements and `getAllBy` for multiple elements. If you are checking for elements which aren't there, use `queryBy` (or `queryAllBy`). For the sake of readability and alignment, I used `queryBy` for all elements in this case though.

We know that the initial data fetching works for our App component now. Next, let's test the user interactions, which we have tested previously only in the child components by firing events without any state and side-effect. We will start with removing an item from the list after the data has been fetched successfully. Since the item with "Jordan Walke" is our first rendered item in the list, it gets removed if we click the first "Dismiss" button:

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

Next, we will test the search feature. First, we have to set up the mocking a bit differently than before, because we are dealing with the initial request (as before) but also with another request once a user searches for more stories by a specific search term:

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

Instead of mocking the request once with Jest (`mockImplementationOnce`), we are mocking multiple requests (`mockImplementation`) now. Depending on the incoming URL, the request either returns the initial list ("React"-related stories) or the new list ("JavaScript"-related stories). Just in case if we provide a wrong URL to the request, the test will throw an error for us to get some confirmation that we set up the test wrong. Now, like before, let's render the App component:

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

We are resolving the first promise for the initial render and expect the input field to render "React" and the two items in the list to render the creators of React and Redux. We also make sure that no JavaScript related stories are rendered yet. Next, we are going to change the input field's value by firing an event and assert whether the new value is rendered from the App component through all its child components in the actual input field:

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

Last, we can submit this search request by firing a submit event with the button. The new search term will be used from the App component's state and thus the new URL should search for JavaScript related stories which we have mocked before:

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

Now Brendan Eich as the creator of JavaScript should be rendered, while the other creators of React and Redux shouldn't be there anymore. The last test has shown you how to depict an entire test scenario in one test case. We can move through each step, like initial fetching, changing the value of the input field, submitting the form, and retrieving new data from the API, with our tools at hand.

After all, React Testing Library with Jest is the status quo library combination when it comes to testing React. Whereas RTL gives you all the React relevant testing tools, Jest comes with the general testing framework for test suites, test cases, assertions and mocking capabilities. A popular alternative to RTL is called [Enzyme](https://www.robinwieruch.de/react-testing-jest-enzyme) by Airbnb.

### Exercises:

* Confirm your [source code for the last section](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/react-testing-integration).
  * Confirm the [changes from the last section](https://github.com/the-road-to-learn-react/hacker-stories/compare/hs/react-testing-unit-component...hs/react-testing-integration?expand=1).
* Read more about [End-to-End-Tests tests in React](https://www.robinwieruch.de/react-testing-cypress).
* While you continue with the learning experience in the upcoming sections, keep your tests green and add new tests whenever you feel the need for it.

### Snapshot Testing

Facebook kam unabhängig von der Testpyramide auf die Idee, Schnappschuss-Tests für in React durchzuführen. Ziel war ein leichterer Weg zum Testen von Komponenten und ihrer Struktur. Grundsätzlich erstellt ein Snapshot-Test einen Snapshot der Ausgabe einer gerenderten Komponente. Dieser Snapshot wird verwendet, um ihn dem  Snapshot zu vergleichen, der erstellt wird, wenn du den Test erneut aufrufst. Wenn sich die Ausgabe der Komponente geändert hat, wird die Differenz beider Snapshots angezeigt und der Snapshot-Test schlägt fehl. Ergo: Der Snapshot-Test informiert, wenn sich die Ausgabe der Komponente ändert. Falls ein Snapshot-Test fehlschlägt, hast du zwei Möglichkeiten: Du akzeptierst das Ergebnis oder du lehnst es ab. Bei Letzterem korrigierst du die Implementierung der Komponente.

Durch die Verwendung von Snapshot-Tests hältst du deine Tests leichtgewichtig. Du testest, ohne die Implementierungsdetails der Komponente zu kennen. Sehen wir uns praktisch an, wie dies in React funktioniert. Als Nächstes führen wir einen Snapshot-Test für die SearchForm-Komponente durch:

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

After running your tests with `npm test`, you should see a new *src/__snapshots__* folder in your project. Browse into this folder and check out the file that has been generated there for you. Similar to RTL's `debug` function, you should see a snapshot of your rendered SearchForm component as HTML element structure in there. Now, go to your *src/App.js* file and change something in the HTML. For example, in the SearchForm component remove the bold text:

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

After your tests run again, you should see something similar to the following on the command line:

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

That's the typical case for a breaking snapshot test. Whenever you change a component's HTML structure and you didn't intend to change it, your snapshot test will tell you on the command line. Either you fix it and or you accept the change: If you want to fix it, you would go into your *src/App.js* file again and fix the SearchForm component. However, if you intended to perform this change, you can press "u" on the command line for your interactive tests and your new snapshot will be created. Try it yourself and see how the snapshot file in your *src/__snapshots__* folder applies the changes.

Jest stores snapshots in a folder so it can validate the difference against future snapshot tests. Users can share these snapshots across teams for version control (e.g. git). Running a snapshot test for the first time creates the snapshot file in your project's folder. When the test is run again, the snapshot is expected to match the version from the latest test run. This is how we make sure the DOM stays the same.

After all, snapshot tests are great for getting tests up and running quickly in React. However, it's not a great idea to snap snapshot tests on every component without any other tests. It's a good practice to use snapshot tests for components that don't update often, are not too complex, and make it easy to see what you are testing. Then snapshot tests are your most lightweight tool for testing.

### Übungen:

* Begutachte den [Quellcode dieses Abschnittes](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/react-snapshot).
  * Bestätige die [Änderungen gegenüber dem Stand der Anwendung am Ende des vorherigen Kapitels](https://github.com/the-road-to-learn-react/hacker-stories/compare/hs/react-testing-integration...hs/react-snapshot?expand=1).
* Ergänze für jede Komponente einen Snapshot-Test und überprüfe den Inhalt des Ordners *__snapshots__*.