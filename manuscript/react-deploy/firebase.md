## In Firebase veröffentlichen/bereitstellen

Wir haben in React eine vollwertige Anwendung erstellt. Der letzte Schritt ist das Teilen dieser mit der Welt --- nachdem du das Programmieren erlernt hast und lauffähige Programme fertigstellst. Wir verwenden Firebase Hosting für die Bereitstellung.

Firebase unterstützt die Create-React-App und die meisten Bibliotheken und Frameworks wie Angular und Vue. Installiere zunächst die Firebase-CLI global:

{title="Command Line",lang="javascript"}
~~~~~~~
npm install -g firebase-tools
~~~~~~~

Die Verwendung der globalen Installation der Firebase-CLI hat den Vorteil, dass es möglich ist Anwendungen ohne Bedenken hinsichtlich der Projektabhängigkeit bereitzustellen. Denke bei jeder global Installation daran, diese aktuell zu halten. Aktualisiere so oft wie nötig mit demselben Befehl:

{title="Command Line",lang="javascript"}
~~~~~~~
npm install -g firebase-tools
~~~~~~~

Wenn du bisher kein Firebase-Konto hast, melde dich für ein [Firebase-Konto](https://console.firebase.google.com/) an und erstelle dort ein neues Projekt. Ordne anschließend die Firebase-CLI dem Firebase-Konto (Google-Konto) hinzu:

{title="Command Line",lang="javascript"}
~~~~~~~
firebase login
~~~~~~~

In der Befehlszeile wird eine URL angezeigt, die du in einem Browser öffnest, oder die Firebase-CLI öffnet sie für dich nachdem du Fragen hinsichtlich Datenschutz beantwortet hast. Wähle ein Google-Konto aus, um ein Firebase-Projekt zu erstellen, und erteile Google die erforderlichen Berechtigungen. Kehre zur Befehlszeile zurück, um eine erfolgreiche Anmeldung zu überprüfen.

Wechsele als Nächstes in den Projektordner und führe den folgenden Befehl aus, mit dem ein Firebase-Projekt für die Firebase-Hosting-Funktionen initialisiert wird:

{title="Command Line",lang="javascript"}
~~~~~~~
firebase init
~~~~~~~

Wähle als Nächstes die Option Hosting. Füge weitere Optionen hinzu, um neben Firebase Hosting ein anderes Tool zu verwenden:

{title="Command Line",lang="javascript"}
~~~~~~~
? Which Firebase CLI features do you want to set up for this folder? Press Space to select features, then Enter to confirm your choices.
 ? Database: Deploy Firebase Realtime Database Rules
 ? Firestore: Deploy rules and create indexes for Firestore
 ? Functions: Configure and deploy Cloud Functions
-> Hosting: Configure and deploy Firebase Hosting sites
 ? Storage: Deploy Cloud Storage security rules
~~~~~~~

Google verknüpft nach der Anmeldung alle Firebase-Projekte, die mit deinem Konto verknüpft sind. Wähle eines, beispielsweise das eben erstellte, von der Firebase-Plattform aus:

{title="Command Line",lang="javascript"}
~~~~~~~
First, let's associate this project directory with a Firebase project.
You can create multiple project aliases by running firebase use --add,
but for now we'll just set up a default project.

? Select a default Firebase project for this directory:
-> my-react-project-abc123 (my-react-project)
i  Using project my-react-project-abc123 (my-react-project)
~~~~~~~

Einige andere Konfigurationsschritte sind erforderlich. Anstatt den Standardordner *public/* zu verwenden, nutzen wir den Ordner *build/* aus der create-react-app. Wähle den passenden Namen für den Build-Ordner aus, wenn du deine Anwendung mit einem Tool wie Webpack selbst packst:

{title="Command Line",lang="javascript"}
~~~~~~~
? What do you want to use as your public directory? build
? Configure as a single-page app (rewrite all urls to /index.html)? Yes
? File public/index.html already exists. Overwrite? No
~~~~~~~

React erstellt einen *build/*-Ordner, nachdem wir `npm run build` aufgerufen haben. Dieser enthält den gesamten Inhalt der Ordner *public/* und *src/*. Da es sich um eine SPA (Einzelseitenanwendung) handelt, leiten wir den Benutzer in die Datei *index.html* um, damit der React-Router das clientseitige Routing verarbeitet.

Deine Firebase-Initialisierung ist abgeschlossen. In diesem Schritt wurden einige Konfigurationsdateien für Firebase Hosting im Ordner deines Projekts erstellt. Weitere Informationen hierzu findest du in der [Firebase-Dokumentation](https://firebase.google.com/docs/hosting/full-config) zum Konfigurieren von Weiterleitungen, einer 404-Seite oder von Headern. Stelle jetzt die React-Anwendung mit Firebase über die Befehlszeile bereit:

{title="Command Line",lang="javascript"}
~~~~~~~
firebase deploy
~~~~~~~

After a successful deployment, you should see a similar output with your project's identifier:

{title="Command Line",lang="javascript"}
~~~~~~~
Project Console: https://console.firebase.google.com/project/my-react-project-abc123/overview
Hosting URL: https://my-react-project-abc123.firebaseapp.com
~~~~~~~

Visit both pages to observe the results. The first link navigates to your Firebase project's dashboard, where you'll see a new panel for the Firebase Hosting. The second link navigates to your deployed React application.

If you see a blank page for your deployed React application, make sure the `public` key/value pair in the *firebase.json* is set to `build`, or whichever name you chose for this folder. Second, verify you've run the build script for your React app with `npm run build`. Finally, check out the [official troubleshoot area for deploying create-react-app applications to Firebase](https://create-react-app.dev/docs/deployment). Try another deployment with `firebase deploy`.

### Exercises

* Read more about [Firebase Hosting](https://firebase.google.com/docs/hosting/).
* [Connect your domain to your Firebase deployed application](https://firebase.google.com/docs/hosting/custom-domain).
* Optional: If you want to have a managed cloud server, check out [DigitalOcean](https://m.do.co/c/fb27c90322f3). It's more work, but it allows more control. [I host all my websites, web applications, and backend APIs there](https://www.robinwieruch.de/deploy-applications-digital-ocean/).