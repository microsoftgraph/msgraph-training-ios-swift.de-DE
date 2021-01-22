<!-- markdownlint-disable MD002 MD041 -->

In dieser Übung integrieren Sie Microsoft Graph in die Anwendung. Für diese Anwendung verwenden Sie das [Microsoft Graph SDK für Objective C,](https://github.com/microsoftgraph/msgraph-sdk-objc) um Aufrufe an Microsoft Graph zu senden.

## <a name="get-calendar-events-from-outlook"></a>Abrufen von Kalenderereignissen von Outlook

In diesem Abschnitt erweitern Sie die Klasse, um eine Funktion hinzuzufügen, um die Ereignisse des Benutzers für die aktuelle Woche zu erhalten, und aktualisieren, um diese `GraphManager` `CalendarViewController` neuen Funktionen zu verwenden.

1. Öffnen **Sie GraphManager.swift,** und fügen Sie der Klasse die folgende Methode `GraphManager` hinzu.

    ```Swift
    public func getCalendarView(viewStart: String,
                                viewEnd: String,
                                completion: @escaping(Data?, Error?) -> Void) {
        // GET /me/calendarview
        // Set start and end of the view
        let start = "startDateTime=\(viewStart)"
        let end = "endDateTime=\(viewEnd)"
        // Only return these fields in results
        let select = "$select=subject,organizer,start,end"
        // Sort results by when they were created, newest first
        let orderBy = "$orderby=start/dateTime"
        // Request at most 25 results
        let top = "$top=25"

        let eventsRequest = NSMutableURLRequest(url: URL(string: "\(MSGraphBaseURL)/me/calendarview?\(start)&\(end)&\(select)&\(orderBy)&\(top)")!)

        // Add the Prefer: outlook.timezone header to get start and end times
        // in user's time zone
        eventsRequest.addValue("outlook.timezone=\"\(self.userTimeZone)\"", forHTTPHeaderField: "Prefer")

        let eventsDataTask = MSURLSessionDataTask(request: eventsRequest, client: self.client, completion: {
            (data: Data?, response: URLResponse?, graphError: Error?) in
            guard let eventsData = data, graphError == nil else {
                completion(nil, graphError)
                return
            }

            // TEMPORARY
            completion(eventsData, nil)
        })

        // Execute the request
        eventsDataTask?.execute()
    }
    ```

    > [!NOTE]
    > Überlegen Sie, was der Code `getCalendarView` gerade macht.
    >
    > - Die URL, die aufgerufen wird, lautet `/v1.0/me/calendarview`.
    >   - Die `startDateTime` Parameter `endDateTime` und Abfrageparameter definieren den Anfang und das Ende der Kalenderansicht.
    >   - Der Abfrageparameter beschränkt die für die einzelnen Ereignisse zurückgegebenen Felder auf die Felder, die `select` tatsächlich von der Ansicht verwendet werden.
    >   - Der `orderby` Abfrageparameter sortiert die Ergebnisse nach Startzeit.
    >   - Der `top` Abfrageparameter fordert 25 Ergebnisse pro Seite an.
    >   - Der Header bewirkt, dass Microsoft Graph die Start- und Endzeiten der einzelnen Ereignisse in der Zeitzone des `Prefer: outlook.timezone` Benutzers zurück gibt.

1. Erstellen Sie eine neue **Swift-Datei** im **GraphTutorial-Projekt** mit dem Namen **GraphToIana.swift**. Fügen Sie den folgenden Code in die Datei ein:

    :::code language="swift" source="../demo/GraphTutorial/GraphTutorial/GraphToIana.swift" id="GraphToIanaSnippet":::

    Dies führt eine einfache Suche durch, um einen IANA-Zeitzonenbezeichner basierend auf dem von Microsoft Graph zurückgegebenen Zeitzonennamen zu finden.

1. Öffnen **Sie CalendarViewController.swift,** und ersetzen Sie den gesamten Inhalt durch den folgenden Code.

    ```Swift
    import UIKit
    import MSGraphClientModels

    class CalendarViewController: UIViewController {

        @IBOutlet var calendarJSON: UITextView!

        private let spinner = SpinnerViewController()

        override func viewDidLoad() {
            super.viewDidLoad()

            // Do any additional setup after loading the view.
            self.spinner.start(container: self)

            // Calculate the start and end of the current week
            let timeZone = GraphToIana.getIanaIdentifier(graphIdentifer: GraphManager.instance.userTimeZone)
            let now = Date()
            var calendar = Calendar(identifier: .gregorian)
            calendar.timeZone = TimeZone(identifier: timeZone)!

            let startOfWeek = calendar.dateComponents([.calendar, .yearForWeekOfYear, .weekOfYear], from: now).date!
            let endOfWeek = calendar.date(byAdding: .day, value: 7, to: startOfWeek)!

            // Convert start and end to ISO 8601 strings
            let isoFormatter = ISO8601DateFormatter()
            let viewStart = isoFormatter.string(from: startOfWeek)
            let viewEnd = isoFormatter.string(from: endOfWeek)

            GraphManager.instance.getCalendarView(viewStart: viewStart, viewEnd: viewEnd) {
                (data: Data?, error: Error?) in
                DispatchQueue.main.async {
                    self.spinner.stop()

                    // TEMPORARY
                    guard let eventsData = data, error == nil else {
                        self.calendarJSON.text = error.debugDescription
                        return
                    }

                    let jsonString = String(data: eventsData, encoding: .utf8)
                    self.calendarJSON.text = jsonString
                    self.calendarJSON.sizeToFit()
                }
            }
        }
    }
    ```

Sie können jetzt die App ausführen, sich anmelden und im Menü auf das **Navigationselement** "Kalender" tippen. Es sollte ein JSON-Dump der Ereignisse in der App angezeigt werden.

## <a name="display-the-results"></a>Anzeigen der Ergebnisse

Jetzt können Sie das JSON-Dump durch eine benutzerfreundliche Anzeige der Ergebnisse ersetzen. In diesem Abschnitt ändern Sie die Funktion so, dass stark typierte Objekte und die Ereignisse mithilfe einer Tabellenansicht `getCalendarView` `CalendarViewController` gerendert werden.

1. Öffnen **Sie GraphManager.swift**. Ersetzen Sie die vorhandene `getCalendarView`-Funktion durch Folgendes.

    :::code language="swift" source="../demo/GraphTutorial/GraphTutorial/GraphManager.swift" id="GetEventsSnippet" highlight="3,28-49":::

1. Erstellen Sie eine neue **Cocoa Touch Class-Datei** im **GraphTutorial-Projekt** mit dem Namen `CalendarTableViewController.swift` . Wählen **Sie "UITableViewController"** in der **Unterklasse des Felds** aus.

1. Öffnen **Sie CalendarTableViewController.swift,** und ersetzen Sie den Inhalt durch Folgendes.

    :::code language="swift" source="../demo/GraphTutorial/GraphTutorial/CalendarTableViewController.swift" id="CalendarTableViewControllerSnippet":::

1. Erstellen Sie eine neue **Cocoa Touch Class-Datei** im **GraphTutorial-Projekt** mit dem Namen `CalendarTableViewCell.swift` . Wählen **Sie "UITableViewCell"** in der **Unterklasse des Felds** aus.

1. Öffnen **Sie CalendarTableViewCell.swift,** und fügen Sie der Klasse die folgenden Eigenschaften `CalendarTableViewCell` hinzu.

    :::code language="swift" source="../demo/GraphTutorial/GraphTutorial/CalendarTableViewCell.swift" id="PropertiesSnippet":::

1. Öffnen **Sie Main.storyboard,** und suchen Sie **die Kalender-Szene.** Löschen Sie die Bildlaufansicht aus der Stammansicht.
1. Fügen Sie **in der Bibliothek** oben in der **Ansicht** eine Navigationsleiste hinzu.
1. Doppelklicken Sie in **der** Navigationsleiste auf den Titel, und aktualisieren Sie ihn in `Calendar` .
1. Fügen Sie **in der Bibliothek** rechts neben der Navigationsleiste ein Balkenschaltflächeelement hinzu. 
1. Wählen Sie die Schaltfläche für die neue Leiste und dann den **Attributprüfungsinspektor aus.** Bild **in** Plus **ändern.**
1. Fügen Sie **der Ansicht unter der Navigationsleiste** eine **Containeransicht** aus der Bibliothek hinzu. Ändern Sie die Größe der Containeransicht, um den verbleibenden Platz in der Ansicht zu übernehmen.
1. Legen Sie Einschränkungen für die Navigationsleiste und die Containeransicht wie folgt ein.

    - **Navigationsleiste**
        - Einschränkung hinzufügen: Höhe, Wert: 44
        - Einschränkung hinzufügen: Führendes Leerzeichen zum sicheren Bereich, Wert: 0
        - Einschränkung hinzufügen: Nachgestellter Speicherplatz zum sicheren Bereich, Wert: 0
        - Einschränkung hinzufügen: Oberster Platz zum sicheren Bereich, Wert: 0
    - **Containeransicht**
        - Einschränkung hinzufügen: Führendes Leerzeichen zum sicheren Bereich, Wert: 0
        - Einschränkung hinzufügen: Nachgestellter Speicherplatz zum sicheren Bereich, Wert: 0
        - Einschränkung hinzufügen: Oberster Platz auf der Navigationsleiste unten, Wert: 0
        - Einschränkung hinzufügen: Unterer Platz zum sicheren Bereich, Wert: 0

1. Suchen Sie den zweiten Ansichtscontroller, der dem Storyboard hinzugefügt wurde, wenn Sie die Containeransicht hinzugefügt haben. Es wird durch eine **Einbettungs-Segue** mit der Kalenderszene verbunden. Wählen Sie diesen Controller aus, und verwenden Sie den **Identitätsinspektor,** um **die Klasse in** **CalendarTableViewController zu ändern.**
1. Löschen Sie **die Ansicht** aus dem **Kalender tabellenansichtscontroller**.
1. Fügen Sie dem  **Tabellenansichtscontroller des** Kalenders eine Tabellenansicht aus der **Bibliothek hinzu.**
1. Wählen Sie die Tabellenansicht und dann den **Attributes Inspector aus.** Set **Prototype Cells** to **1**.
1. Ziehen Sie den unteren Rand der Prototypzelle, um Ihnen einen größeren Bereich für die Arbeit zu bieten.
1. Verwenden Sie die **Bibliothek,** um **der** Prototypzelle drei Beschriftungen hinzuzufügen.
1. Wählen Sie die Prototypzelle und dann den **Identitätsinspektor aus.** Ändern **Sie die Klasse** in **CalendarTableViewCell**.
1. Wählen Sie den **Attributes Inspector aus,** und legen **Sie den Bezeichner auf** . `EventCell`
1. Wenn Die **EventCell ausgewählt** ist, wählen Sie den **Connections Inspector** und verbinden , und mit den Beschriftungen, die Sie der Zelle `durationLabel` im `organizerLabel` `subjectLabel` Storyboard hinzugefügt haben.
1. Legen Sie die Eigenschaften und Einschränkungen für die drei Beschriftungen wie folgt dar.

    - **Betreffbezeichnung**
        - Einschränkung hinzufügen: Führendes Leerzeichen zum führenden Rand der Inhaltsansicht, Wert: 0
        - Einschränkung hinzufügen: Nachgestellter Speicherplatz zum nachgestellten Rand der Inhaltsansicht, Wert: 0
        - Einschränkung hinzufügen: Oberster Platz am oberen Rand der Inhaltsansicht, Wert: 0
    - **Bezeichnung des Organisators**
        - Schriftart: System 12.0
        - Einschränkung hinzufügen: Höhe, Wert: 15
        - Einschränkung hinzufügen: Führendes Leerzeichen zum führenden Rand der Inhaltsansicht, Wert: 0
        - Einschränkung hinzufügen: Nachgestellter Speicherplatz zum nachgestellten Rand der Inhaltsansicht, Wert: 0
        - Einschränkung hinzufügen: Oberster Platz im unteren Bereich der Betreffbezeichnung, Wert: Standard
    - **Bezeichnung "Dauer"**
        - Schriftart: System 12.0
        - Farbe: Dunkelgrau
        - Einschränkung hinzufügen: Höhe, Wert: 15
        - Einschränkung hinzufügen: Führendes Leerzeichen zum führenden Rand der Inhaltsansicht, Wert: 0
        - Einschränkung hinzufügen: Nachgestellter Speicherplatz zum nachgestellten Rand der Inhaltsansicht, Wert: 0
        - Einschränkung hinzufügen: Oberster Platz am unteren Rand der Bezeichnung "Organizer", Wert: Standard
        - Einschränkung hinzufügen: Unterer Bereich zum unteren Rand der Inhaltsansicht, Wert: 0

1. Select the **EventCell,** then select the **Size Inspector**. Aktivieren **Sie "Automatisch** für **Zeilenhöhe".**

    ![Screenshot der Ansichtscontroller für Kalender- und Kalendertabelle](images/calendar-table-storyboard.png)

1. Öffnen **Sie CalendarViewController.swift,** und ersetzen Sie den Inhalt durch den folgenden Code.

    :::code language="swift" source="../demo/GraphTutorial/GraphTutorial/CalendarViewController.swift" id="CalendarViewSnippet":::

1. Führen Sie die App aus, melden Sie sich an, und tippen Sie auf die **Registerkarte "Kalender".** Die Liste der Ereignisse sollte angezeigt werden.

    ![Ein Screenshot der Tabelle mit Ereignissen](images/calendar-list.png)
