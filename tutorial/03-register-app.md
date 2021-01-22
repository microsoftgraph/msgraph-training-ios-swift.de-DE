<!-- markdownlint-disable MD002 MD041 -->

In dieser Übung erstellen Sie eine neue native Azure AD-Anwendung mithilfe des Azure Active Directory Admin Centers.

1. Öffnen Sie einen Browser, und navigieren Sie zum [Azure Active Directory Admin Center](https://aad.portal.azure.com). Melden Sie sich mit einem **persönlichen Konto** (auch: Microsoft-Konto) oder einem **Geschäfts- oder Schulkonto** an.

1. Wählen Sie in der linken Navigationsleiste **Azure Active Directory** aus, und wählen Sie dann **App-Registrierungen** unter **Verwalten** aus.

    ![Screenshot der APP-Registrierungen ](images/aad-portal-app-registrations.png)

1. Wählen Sie **Neue Registrierung** aus. Legen Sie auf der Seite **Anwendung registrieren** die Werte wie folgt fest.

    - Legen Sie **Name** auf `iOS Swift Graph Tutorial` fest.
    - Legen Sie **Unterstützte Kontotypen** auf **Konten in allen Organisationsverzeichnissen und persönliche Microsoft-Konten** fest.
    - Lassen Sie **URI umleiten** leer.

    ![Screenshot der Seite "Anwendung registrieren"](images/aad-register-an-app.png)

1. Wählen Sie **Registrieren** aus. Kopieren Sie auf der **iOS Swift Graph-Lernprogrammseite** den Wert der Anwendungs-ID **(Client-ID),** und speichern Sie ihn. Sie benötigen ihn im nächsten Schritt.

    ![Screenshot der Anwendungs-ID der neuen App-Registrierung](images/aad-application-id.png)

1. Wählen Sie unter **Verwalten** die Option **Authentifizierung** aus. Wählen **Sie "Plattform hinzufügen"** und dann **iOS/macOS aus.**

1. Geben Sie die Bundle-ID Ihrer App ein, und wählen **Sie "Konfigurieren"** aus, und wählen Sie dann **"Fertig" aus.**
