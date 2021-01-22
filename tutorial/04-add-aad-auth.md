<!-- markdownlint-disable MD002 MD041 -->

In dieser Übung erweitern Sie die Anwendung aus der vorherigen Übung, um die Authentifizierung mit Azure AD zu unterstützen. Dies ist erforderlich, um das erforderliche OAuth-Zugriffstoken zum Aufrufen von Microsoft Graph abzurufen. Dazu integrieren Sie die [Microsoft Authentication Library (MSAL) für iOS](https://github.com/AzureAD/microsoft-authentication-library-for-objc) in die Anwendung.

1. Erstellen Sie eine neue **Eigenschaftslistendatei** im **GraphTutorial-Projekt** mit dem Namen **AuthSettings.plist**.
1. Fügen Sie der Datei im Stammverzeichnis die folgenden **Elemente** hinzu.

    | Key | Typ | Wert |
    |-----|------|-------|
    | `AppId` | Zeichenfolge | Die Anwendungs-ID aus dem Azure-Portal |
    | `GraphScopes` | Array | Drei Zeichenfolgenwerte: `User.Read` `MailboxSettings.Read` , und `Calendars.ReadWrite` |

    ![Screenshot der Datei "AuthSettings.plist" in Xcode](images/auth-settings.png)

> [!IMPORTANT]
> Wenn Sie Quellcodeverwaltung wie Git verwenden, wäre es jetzt ein guter Zeitpunkt, die **Datei "AuthSettings.plist"** aus der Quellcodeverwaltung auszuschließen, um versehentlich einen Leck für Ihre App-ID zu vermeiden.

## <a name="implement-sign-in"></a>Implementieren der Anmeldung

In diesem Abschnitt konfigurieren Sie das Projekt für MSAL, erstellen eine Authentifizierungs-Manager-Klasse und aktualisieren die App so, dass sie sich anmeldet und abmeldet.

### <a name="configure-project-for-msal"></a>Konfigurieren des Projekts für MSAL

1. Fügen Sie den Projektfunktionen eine neue Schlüsselbundgruppe hinzu.
    1. Wählen Sie **das GraphTutorial-Projekt** aus, und signieren **& Funktionen.**
    1. Wählen **Sie +Funktion** aus, und doppelklicken Sie dann **auf Schlüsselbundfreigabe.**
    1. Fügen Sie eine Schlüsselbundgruppe mit dem Wert `com.microsoft.adalcache` hinzu.

1. Steuerelement klicken **Sie auf "Info.plist",** und wählen **Sie "Öffnen als"** und dann **"Quellcode" aus.**
1. Fügen Sie Folgendes innerhalb des Elements `<dict>` hinzu.

    ```xml
    <key>CFBundleURLTypes</key>
    <array>
      <dict>
        <key>CFBundleURLSchemes</key>
        <array>
          <string>msauth.$(PRODUCT_BUNDLE_IDENTIFIER)</string>
        </array>
      </dict>
    </array>
    <key>LSApplicationQueriesSchemes</key>
    <array>
        <string>msauthv2</string>
        <string>msauthv3</string>
    </array>
    ```

1. Öffnen **Sie "AppDelegate.swift",** und fügen Sie die folgende Import-Anweisung am Anfang der Datei hinzu.

    ```Swift
    import MSAL
    ```

1. Fügen Sie die folgende Funktion zur `AppDelegate`-Klasse hinzu:

    :::code language="swift" source="../demo/GraphTutorial/GraphTutorial/AppDelegate.swift" id="HandleMsalResponseSnippet":::

### <a name="create-authentication-manager"></a>Erstellen eines Authentifizierungs-Managers

1. Erstellen Sie eine neue **Swift-Datei** im **GraphTutorial-Projekt** mit dem Namen **AuthenticationManager.swift**. Fügen Sie den folgenden Code in die Datei ein:

    :::code language="swift" source="../demo/GraphTutorial/GraphTutorial/AuthenticationManager.swift" id="AuthManagerSnippet":::

### <a name="add-sign-in-and-sign-out"></a>Hinzufügen von Anmeldung und Abmelden

1. Öffnen **Sie "SignInViewController.swift",** und ersetzen Sie den Inhalt durch den folgenden Code.

    :::code language="swift" source="../demo/GraphTutorial/GraphTutorial/SignInViewController.swift" id="SignInViewSnippet":::

1. Öffnen **Sie WelcomeViewController.swift,** und ersetzen Sie die vorhandene `signOut` Funktion durch Folgendes.

    :::code language="swift" source="../demo/GraphTutorial/GraphTutorial/WelcomeViewController.swift" id="SignOutSnippet":::

1. Speichern Sie Ihre Änderungen, und starten Sie die Anwendung im Simulator neu.

Wenn Sie sich bei der App anmelden, sollte ein Zugriffstoken im Ausgabefenster in Xcode angezeigt werden.

![Screenshot des Ausgabefensters in Xcode mit einem Zugriffstoken](images/access-token-output.png)

## <a name="get-user-details"></a>Benutzerdetails abrufen

In diesem Abschnitt erstellen Sie eine Hilfsklasse für alle Aufrufe von Microsoft Graph und aktualisieren die Klasse so, dass sie diese neue Klasse verwendet, um den angemeldeten `WelcomeViewController` Benutzer zu erhalten.

1. Erstellen Sie eine neue **Swift-Datei** im **GraphTutorial-Projekt** mit dem Namen **GraphManager.swift**. Fügen Sie den folgenden Code in die Datei ein:

    ```Swift
    import Foundation
    import MSGraphClientSDK
    import MSGraphClientModels

    class GraphManager {

        // Implement singleton pattern
        static let instance = GraphManager()

        private let client: MSHTTPClient?

        public var userTimeZone: String

        private init() {
            client = MSClientFactory.createHTTPClient(with: AuthenticationManager.instance)
            userTimeZone = "UTC"
        }

        public func getMe(completion: @escaping(MSGraphUser?, Error?) -> Void) {
            // GET /me
            let select = "$select=displayName,mail,mailboxSettings,userPrincipalName"
            let meRequest = NSMutableURLRequest(url: URL(string: "\(MSGraphBaseURL)/me?\(select)")!)
            let meDataTask = MSURLSessionDataTask(request: meRequest, client: self.client, completion: {
                (data: Data?, response: URLResponse?, graphError: Error?) in
                guard let meData = data, graphError == nil else {
                    completion(nil, graphError)
                    return
                }

                do {
                    // Deserialize response as a user
                    let user = try MSGraphUser(data: meData)
                    completion(user, nil)
                } catch {
                    completion(nil, error)
                }
            })

            // Execute the request
            meDataTask?.execute()
        }
    }
    ```

1. Öffnen **Sie WelcomeViewController.swift,** und fügen Sie die folgende `import` Anweisung am Anfang der Datei hinzu.

    ```Swift
    import MSGraphClientModels
    ```

1. Fügen Sie der `WelcomeViewController`-Klasse die folgende Eigenschaft hinzu.

    ```Swift
    private let spinner = SpinnerViewController()
    ```

1. Ersetzen Sie das vorhandene `viewDidLoad` durch den folgenden Code.

    :::code language="swift" source="../demo/GraphTutorial/GraphTutorial/WelcomeViewController.swift" id="ViewDidLoadSnippet":::

Wenn Sie Ihre Änderungen speichern und die App jetzt neu starten, wird die Benutzeroberfläche nach der Anmeldung mit dem Anzeigenamen und der E-Mail-Adresse des Benutzers aktualisiert.
