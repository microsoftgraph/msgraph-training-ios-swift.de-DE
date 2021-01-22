<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="f563a-101">In dieser Übung erstellen Sie eine neue native Azure AD-Anwendung mithilfe des Azure Active Directory Admin Centers.</span><span class="sxs-lookup"><span data-stu-id="f563a-101">In this exercise you will create a new Azure AD native application using the Azure Active Directory admin center.</span></span>

1. <span data-ttu-id="f563a-102">Öffnen Sie einen Browser, und navigieren Sie zum [Azure Active Directory Admin Center](https://aad.portal.azure.com). Melden Sie sich mit einem **persönlichen Konto** (auch: Microsoft-Konto) oder einem **Geschäfts- oder Schulkonto** an.</span><span class="sxs-lookup"><span data-stu-id="f563a-102">Open a browser and navigate to the [Azure Active Directory admin center](https://aad.portal.azure.com) and login using a **personal account** (aka: Microsoft Account) or **Work or School Account**.</span></span>

1. <span data-ttu-id="f563a-103">Wählen Sie in der linken Navigationsleiste **Azure Active Directory** aus, und wählen Sie dann **App-Registrierungen** unter **Verwalten** aus.</span><span class="sxs-lookup"><span data-stu-id="f563a-103">Select **Azure Active Directory** in the left-hand navigation, then select **App registrations** under **Manage**.</span></span>

    ![<span data-ttu-id="f563a-104">Screenshot der APP-Registrierungen</span><span class="sxs-lookup"><span data-stu-id="f563a-104">A screenshot of the App registrations</span></span> ](images/aad-portal-app-registrations.png)

1. <span data-ttu-id="f563a-105">Wählen Sie **Neue Registrierung** aus.</span><span class="sxs-lookup"><span data-stu-id="f563a-105">Select **New registration**.</span></span> <span data-ttu-id="f563a-106">Legen Sie auf der Seite **Anwendung registrieren** die Werte wie folgt fest.</span><span class="sxs-lookup"><span data-stu-id="f563a-106">On the **Register an application** page, set the values as follows.</span></span>

    - <span data-ttu-id="f563a-107">Legen Sie **Name** auf `iOS Swift Graph Tutorial` fest.</span><span class="sxs-lookup"><span data-stu-id="f563a-107">Set **Name** to `iOS Swift Graph Tutorial`.</span></span>
    - <span data-ttu-id="f563a-108">Legen Sie **Unterstützte Kontotypen** auf **Konten in allen Organisationsverzeichnissen und persönliche Microsoft-Konten** fest.</span><span class="sxs-lookup"><span data-stu-id="f563a-108">Set **Supported account types** to **Accounts in any organizational directory and personal Microsoft accounts**.</span></span>
    - <span data-ttu-id="f563a-109">Lassen Sie **URI umleiten** leer.</span><span class="sxs-lookup"><span data-stu-id="f563a-109">Leave **Redirect URI** empty.</span></span>

    ![Screenshot der Seite "Anwendung registrieren"](images/aad-register-an-app.png)

1. <span data-ttu-id="f563a-111">Wählen Sie **Registrieren** aus.</span><span class="sxs-lookup"><span data-stu-id="f563a-111">Select **Register**.</span></span> <span data-ttu-id="f563a-112">Kopieren Sie auf der **iOS Swift Graph-Lernprogrammseite** den Wert der Anwendungs-ID **(Client-ID),** und speichern Sie ihn. Sie benötigen ihn im nächsten Schritt.</span><span class="sxs-lookup"><span data-stu-id="f563a-112">On the **iOS Swift Graph Tutorial** page, copy the value of the **Application (client) ID** and save it, you will need it in the next step.</span></span>

    ![Screenshot der Anwendungs-ID der neuen App-Registrierung](images/aad-application-id.png)

1. <span data-ttu-id="f563a-114">Wählen Sie unter **Verwalten** die Option **Authentifizierung** aus.</span><span class="sxs-lookup"><span data-stu-id="f563a-114">Select **Authentication** under **Manage**.</span></span> <span data-ttu-id="f563a-115">Wählen **Sie "Plattform hinzufügen"** und dann **iOS/macOS aus.**</span><span class="sxs-lookup"><span data-stu-id="f563a-115">Select **Add a platform**, then **iOS / macOS**.</span></span>

1. <span data-ttu-id="f563a-116">Geben Sie die Bundle-ID Ihrer App ein, und wählen **Sie "Konfigurieren"** aus, und wählen Sie dann **"Fertig" aus.**</span><span class="sxs-lookup"><span data-stu-id="f563a-116">Enter your app's Bundle ID and select **Configure**, then select **Done**.</span></span>
