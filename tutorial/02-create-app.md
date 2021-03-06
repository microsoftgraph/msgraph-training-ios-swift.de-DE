<!-- markdownlint-disable MD002 MD041 -->

Erstellen Sie zunächst ein neues Swift-Projekt.

1. Öffnen Sie Xcode. Wählen Sie **im Menü "Datei"** **"Neu"** und dann **"Projekt" aus.**
1. Wählen Sie die **App-Vorlage** und dann **"Weiter" aus.**

    ![Screenshot des Dialogfelds zur Auswahl der Xcode-Vorlage](images/xcode-select-template.png)

1. Legen Sie **den Produktnamen auf** `GraphTutorial` und die Sprache **auf** **Swift .**
1. Füllen Sie die verbleibenden Felder aus, und wählen Sie **"Weiter" aus.**
1. Wählen Sie einen Speicherort für das Projekt aus, und wählen Sie **"Erstellen" aus.**

## <a name="install-dependencies"></a>Installieren von Abhängigkeiten

Installieren Sie vor dem Wechsel einige zusätzliche Abhängigkeiten, die Sie später verwenden werden.

- [Microsoft Authentication Library (MSAL) für iOS](https://github.com/AzureAD/microsoft-authentication-library-for-objc) für die Authentifizierung bei Azure AD.
- [Microsoft Graph SDK für Objective C](https://github.com/microsoftgraph/msgraph-sdk-objc) für Aufrufe an Microsoft Graph.
- [Microsoft Graph Models SDK für Objective C](https://github.com/microsoftgraph/msgraph-sdk-objc-models) für stark typierte Objekte, die Microsoft #A0 wie Benutzer oder Ereignisse darstellen.

1. Beenden Sie Xcode.
1. Öffnen Sie Terminal, und ändern Sie das Verzeichnis in den Speicherort Ihres **GraphTutorial-Projekts.**
1. Führen Sie den folgenden Befehl aus, um eine Podfile zu erstellen.

    ```Shell
    pod init
    ```

1. Öffnen Sie die Podfile-Datei, und fügen Sie die folgenden Zeilen direkt hinter der Zeile `use_frameworks!` hinzu.

    ```Ruby
    pod 'MSAL', '~> 1.1.13'
    pod 'MSGraphClientSDK', ' ~> 1.0.0'
    pod 'MSGraphClientModels', '~> 1.3.0'
    ```

1. Speichern Sie die Podfile, und führen Sie dann den folgenden Befehl aus, um die Abhängigkeiten zu installieren.

    ```Shell
    pod install
    ```

1. Öffnen Sie nach Abschluss des Befehls den neu erstellten **GraphTutorial.xcworkspace** in Xcode.

## <a name="design-the-app"></a>Entwerfen der App

In diesem Abschnitt erstellen Sie die Ansichten für die App: eine Anmeldeseite, einen Registerkartenleistennavigator, eine Willkommensseite und eine Kalenderseite. Außerdem erstellen Sie eine Aktivitätsindikatorüberlagerung.

### <a name="create-sign-in-page"></a>Anmeldeseite erstellen

1. Erweitern Sie **den Ordner "GraphTutorial"** in Xcode, und wählen Sie **dann ViewController.swift aus.**
1. Ändern Sie **im Dateiinspektor** den **Namen** der Datei in `SignInViewController.swift` .

    ![Screenshot des Dateiinspektors](images/rename-file.png)

1. Öffnen **Sie "SignInViewController.swift",** und ersetzen Sie den Inhalt durch den folgenden Code.

    ```Swift
    import UIKit

    class SignInViewController: UIViewController {

        override func viewDidLoad() {
            super.viewDidLoad()
            // Do any additional setup after loading the view.
        }

        @IBAction func signIn() {
            self.performSegue(withIdentifier: "userSignedIn", sender: nil)
        }
    }
    ```

1. Öffnen **Sie Main.storyboard**. Erweitern **Sie die Ansichtscontroller-Szene,** und wählen Sie dann **"Ansichtscontroller" aus.**

    ![Screenshot von Xcode mit ausgewähltem Ansichtscontroller](images/storyboard-select-view-controller.png)

1. Wählen Sie **den Identitätsinspektor** aus, und ändern Sie dann das **Klassendropdown** in **SignInViewController**.

    ![Screenshot des Identitätsinspektors](images/change-class.png)

1. Wählen Sie die **Bibliothek** aus, und ziehen Sie dann eine **Schaltfläche** auf den **Anmeldeansichtscontroller.**

    ![Screenshot der Bibliothek in Xcode](images/add-button-to-view.png)

1. Wenn die Schaltfläche ausgewählt ist, wählen Sie den **Attributes Inspector** aus, und ändern Sie den Titel **der** Schaltfläche in `Sign In` .

    ![Screenshot des Titelfelds im Attributes Inspector in Xcode](images/set-button-title.png)

1. Wenn die Schaltfläche ausgewählt ist, wählen Sie die Schaltfläche **"Ausrichten"** am unteren Rand des Storyboards aus. Wählen Sie **sowohl "Horizontal im Container" als auch** **"Vertikal" in** Containereinschränkungen aus, lassen Sie deren Werte 0, und wählen Sie dann "2 Einschränkungen **hinzufügen" aus.**

    ![Screenshot der Einstellungen für Ausrichtungseinschränkungen in Xcode](images/add-alignment-constraints.png)

1. Wählen Sie **den Anmeldeansichtscontroller** und dann den **Connections Inspector aus.**
1. Ziehen **Sie unter "Empfangene** Aktionen" den nicht ausgefüllten Kreis neben **"SignIn" auf** die Schaltfläche. Wählen **Sie im Popupmenü** "Touch Up Inside" aus.

    ![Screenshot des Ziehens der Anmeldeaktion auf die Schaltfläche in Xcode](images/connect-sign-in-button.png)

### <a name="create-tab-bar"></a>Registerkartenleiste erstellen

1. Wählen Sie **die Bibliothek** aus, und ziehen Sie dann einen **Tableistencontroller** auf das Storyboard.
1. Wählen Sie **den Anmeldeansichtscontroller** und dann den **Connections Inspector aus.**
1. Ziehen **Sie unter "Ausgelöste Segues"** den nicht ausgefüllten Kreis neben **manuell** auf den **Tableistencontroller** im Storyboard. Wählen **Sie im Popupmenü** modal "Present" aus.

    ![Screenshot des Ziehens einer manuellen Segue auf den neuen Tableistencontroller in Xcode](images/add-segue.png)

1. Wählen Sie die gerade hinzugefügte Segue aus, und wählen Sie dann den **Attributes Inspector aus.** Legen Sie das **Feld "Bezeichner"** `userSignedIn` auf " und **"Presentation" auf** **"Vollbild" (Vollbild) festgelegt.**

    ![Screenshot des Felds "Identifier" im Attributes Inspector in Xcode](images/set-segue-identifier.png)

1. Wählen Sie **die Szene "Element 1"** und dann den Verbindungsinspektor **aus.**
1. Ziehen **Sie unter "Ausgelöste Segues"** den nicht ausgefüllten Kreis neben **manuell** auf den Sign In **View Controller** im Storyboard. Wählen **Sie im Popupmenü** "Modal präsentieren" aus.
1. Wählen Sie die gerade hinzugefügte Segue aus, und wählen Sie dann den **Attributes Inspector aus.** Legen Sie das **Feld "Bezeichner"** `userSignedOut` auf ", und legen Sie **"Presentation" auf** **"Full Screen" (Präsentation) auf "Vollbild" (Vollbild) festgelegt.**

### <a name="create-welcome-page"></a>Willkommensseite erstellen

1. Wählen Sie die **Datei Assets.xcassets** aus.
1. Wählen Sie **im Menü "Editor"** **"Neues Objekt hinzufügen"** und dann **"Bildsatz" aus.**
1. Wählen Sie die neue **Image-Ressource** aus, und legen Sie den Namen mithilfe des Attributprüfungsinspektors  **auf** `DefaultUserPhoto` .
1. Fügen Sie ein beliebiges Bild hinzu, das Sie als Standardbenutzerprofilfoto verwenden möchten.

    ![Screenshot der Objektansicht "Bildsatz" in Xcode](images/add-default-user-photo.png)

1. Erstellen Sie eine neue **Cocoa Touch Class-Datei** im **Ordner GraphTutorial** mit dem Namen `WelcomeViewController` . Wählen **Sie "UIViewController"** in der **Unterklasse des Felds** aus.
1. Öffnen **Sie WelcomeViewController.swift,** und ersetzen Sie den Inhalt durch den folgenden Code.

    ```Swift
    import UIKit

    class WelcomeViewController: UIViewController {

        @IBOutlet var userProfilePhoto: UIImageView!
        @IBOutlet var userDisplayName: UILabel!
        @IBOutlet var userEmail: UILabel!

        override func viewDidLoad() {
            super.viewDidLoad()

            // Do any additional setup after loading the view.

            // TEMPORARY
            self.userProfilePhoto.image = UIImage(imageLiteralResourceName: "DefaultUserPhoto")
            self.userDisplayName.text = "Default User"
            self.userEmail.text = "default@contoso.com"
        }

        @IBAction func signOut() {
            self.performSegue(withIdentifier: "userSignedOut", sender: nil)
        }
    }
    ```

1. Öffnen **Sie Main.storyboard**. Wählen Sie **die Szene "Element 1"** und dann den **Identitätsinspektor aus.** Ändern Sie **den Wert der** Klasse in **WelcomeViewController**.
1. Fügen Sie **mithilfe der Bibliothek** der Szene **"Element 1"** die folgenden Elemente hinzu.

    - Eine **Bildansicht**
    - Zwei **Bezeichnungen**
    - Eine **Schaltfläche**
1. Stellen Sie **mithilfe des Connections Inspector** die folgenden Verbindungen.

    - Verknüpfen Sie **den userDisplayName-Ausgang** mit der ersten Bezeichnung.
    - Verknüpfen Sie **die userEmail-Filiale** mit der zweiten Bezeichnung.
    - Verknüpfen Sie **die userProfilePhoto-Steckverbindung** mit der Bildansicht.
    - Verknüpfen Sie **die empfangene Aktion "SignOut"** mit dem Touch **Up Inside der Schaltfläche.**

1. Wählen Sie die Bildansicht und dann den **Größeninspektor aus.**
1. Legen Sie **die Breite** und **Höhe auf** 196 fest.
1. Verwenden Sie die **Schaltfläche "Ausrichten",** um die **Einschränkung "Horizontal in Container"** mit dem Wert 0 hinzuzufügen.
1. Verwenden Sie **die Schaltfläche "Neue Nebenbedingungen** hinzufügen" (neben der Schaltfläche "Ausrichten"), um die folgenden Einschränkungen hinzuzufügen: 

    - Oben ausrichten an: Sicherer Bereich, Wert: 0
    - Bottom Space to: User Display Name, value: Standard
    - Höhe, Wert: 196
    - Breite, Wert: 196

    ![Screenshot der neuen Einschränkungseinstellungen in Xcode](./images/add-new-constraints.png)

1. Wählen Sie die erste Bezeichnung aus, und verwenden Sie dann die Schaltfläche **"Ausrichten",** um die **Einschränkung "Horizontal in Container"** mit dem Wert 0 hinzuzufügen.
1. Verwenden Sie die **Schaltfläche "Neue Einschränkungen hinzufügen",** um die folgenden Einschränkungen hinzuzufügen:

    - Oberster Bereich für: Benutzerprofilfoto, Wert: Standard
    - Bottom Space to: User Email, value: Standard

1. Wählen Sie die zweite Bezeichnung und dann den **Attributes Inspector aus.**
1. Ändern Sie **die Farbe** in **Dunkelgrau,** und ändern Sie **die Schriftart** in **System 12.0**.
1. Verwenden Sie die **Schaltfläche "Ausrichten",** um die **Einschränkung "Horizontal in Container"** mit dem Wert 0 hinzuzufügen.
1. Verwenden Sie die **Schaltfläche "Neue Einschränkungen** hinzufügen", um die folgenden Einschränkungen hinzuzufügen:

    - Oberes Leerzeichen für: Anzeigename des Benutzers, Wert: Standard
    - Bottom Space to: Sign Out, value: 14

1. Wählen Sie die Schaltfläche und dann den **Attributes Inspector aus.**
1. Ändern Sie **den Titel** in `Sign Out` .
1. Verwenden Sie die **Schaltfläche "Ausrichten",** um die **Einschränkung "Horizontal in Container"** mit dem Wert 0 hinzuzufügen.
1. Verwenden Sie die **Schaltfläche "Neue Einschränkungen hinzufügen",** um die folgenden Einschränkungen hinzuzufügen:

    - Oberster Bereich für: Benutzer-E-Mail, Wert: 14

1. Wählen Sie das Element der Registerkartenleiste am unteren Rand der Szene aus, und wählen Sie dann den **Attributes Inspector aus.** Ändern Sie **den Titel** in `Me` .

Die Willkommensszene sollte wie dies aussehen, sobald Sie fertig sind.

![Screenshot des Layouts der Willkommensszene](images/welcome-scene-layout.png)

### <a name="create-calendar-page"></a>Kalenderseite erstellen

1. Erstellen Sie eine neue **Cocoa Touch Class-Datei** im **Ordner GraphTutorial** mit dem Namen `CalendarViewController` . Wählen **Sie "UIViewController"** in der **Unterklasse des Felds** aus.
1. Öffnen **Sie CalendarViewController.swift,** und ersetzen Sie den Inhalt durch den folgenden Code.

    ```Swift
    import UIKit

    class CalendarViewController: UIViewController {

        @IBOutlet var calendarJSON: UITextView!

        override func viewDidLoad() {
            super.viewDidLoad()

            // Do any additional setup after loading the view.

            // TEMPORARY
            calendarJSON.text = "Calendar"
            calendarJSON.sizeToFit()
        }
    }
    ```

1. Öffnen **Sie Main.storyboard**. Wählen Sie **die Szene "Element 2"** und dann den **Identitätsinspektor aus.** Ändern Sie **den Wert der** Klasse in **CalendarViewController**.
1. Fügen Sie **mithilfe der Bibliothek** eine **Textansicht zur** Szene **"Element 2" hinzu.**
1. Wählen Sie die gerade hinzugefügte Textansicht aus. Wählen Sie **im Menü "Editor"** **"Einbetten in"** und dann **"Bildlaufansicht" aus.**
1. Ändern Sie die Größe der Bildlauf- und Textansicht, um den gesamten Bildschirm zu übernehmen.
1. Verbinden Sie **mithilfe des Connections Inspectors** die **calendarJSON-Filiale** mit der Textansicht.
1. Wählen Sie das Element der Registerkartenleiste am unteren Rand der Szene aus, und wählen Sie dann den Attribute Inspector **aus.** Ändern Sie **den Titel** in `Calendar` .
1. Wählen Sie **im Menü "Editor"** **die** Option "Probleme mit dem automatischen Layout beheben" aus, und wählen Sie dann unter "Alle Ansichten" im **Kalenderansichtscontroller** "Fehlende Einschränkungen hinzufügen" **aus.**

Die Kalenderszene sollte in etwa so aussehen, sobald Sie fertig sind.

![Screenshot des Layouts der Kalenderszene](images/calendar-scene-layout.png)

### <a name="create-activity-indicator"></a>Aktivitätsindikator erstellen

1. Erstellen Sie eine neue **Cocoa Touch Class-Datei** im **Ordner GraphTutorial** mit dem Namen `SpinnerViewController` . Wählen **Sie "UIViewController"** in der **Unterklasse des Felds** aus.
1. Öffnen **Sie "SpinnerViewController.swift",** und ersetzen Sie den Inhalt durch den folgenden Code.

    :::code language="swift" source="../demo/GraphTutorial/GraphTutorial/SpinnerViewController.swift" id="SpinnerSnippet":::

## <a name="test-the-app"></a>Testen der App

Speichern Sie Ihre Änderungen, und starten Sie die App. Sie sollten mithilfe der Schaltflächen  "An-  und Abmelden" und der Registerkartenleiste zwischen den Bildschirmen wechseln können.

![Screenshots der Anwendung](images/app-screens.png)
