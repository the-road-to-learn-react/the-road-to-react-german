## React DOM

Nachdem wir die Komponentendefinitionen und das Instanziieren kennengelernt haben, wenden wir das Wissen auf die App-Komponente an. Es ist nicht notwendig, dass du selbst Hand anlegst. Die App war von Anfang an Bestandteil unserer Anwendung. Den Code findest du in der Datei *src/index.js*:

{title="src/index.js",lang="javascript"}
~~~~~~~
import React from 'react';
import ReactDOM from 'react-dom';

import App from './App';

ReactDOM.render(
  <App />,
  document.getElementById('root')
);
~~~~~~~

Neben React wird eine weitere Bibliothek namens `react-dom` importiert. In dieser nutzt die Funktion `ReactDOM.render()` ein HTML-Element --- das mit der ID `root` --- und ersetzt es mit JSX. Der Prozess integriert React in HTML. `ReactDOM.render()` erwartet zwei Argumente: Das erste ist das zu rendernde JSX-Element. Meist wird eine Instanz der App verwendet. Das ist nicht zwingend. Das Übergeben von JSX ohne das "Instanziieren" einer Komponente ist völlig ausreichend. Zum Beispiel so:

{title="Code Playground",lang="javascript"}
~~~~~~~
ReactDOM.render(
  <h1>Hello React World</h1>,
  document.getElementById('root')
);
~~~~~~~

Das zweite Argument legt fest, wo die React-Anwendung den fertigen Code einfügt. Erwartet wird ein Element, mit `id='root'`, welches in der Datei *public/index.html* vorhanden ist. *public/index.html* ist eine normale HTML-Datei.

### Übungen:

* Öffnen die Datei *public/index.html* und finder heraus wo und wie die React-Anwendung in den HTML-Code eingebunden ist.
* Überlege, wie du eine React-Anwendung in eine externe HTML-Webanwendung integrierst.
* Lese mehr zum Thema [Rendern von Elementen in React](https://de.reactjs.org/docs/rendering-elements.html).
