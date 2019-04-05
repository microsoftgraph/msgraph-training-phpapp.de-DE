<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="a4770-101">In dieser Übung erstellen Sie eine neue Azure AD-Webanwendungs Registrierung mithilfe des Azure Active Directory Admin Center.</span><span class="sxs-lookup"><span data-stu-id="a4770-101">In this exercise, you will create a new Azure AD web application registration using the Azure Active Directory admin center.</span></span>

1. <span data-ttu-id="a4770-102">Öffnen Sie einen Browser, und navigieren Sie zum [Azure Active Directory Admin Center](https://aad.portal.azure.com).</span><span class="sxs-lookup"><span data-stu-id="a4770-102">Open a browser and navigate to the [Azure Active Directory admin center](https://aad.portal.azure.com).</span></span> <span data-ttu-id="a4770-103">Melden Sie sich über ein **persönliches Konto** (aka: Microsoft-Konto) oder ein Geschäfts- **oder Schulkonto**an.</span><span class="sxs-lookup"><span data-stu-id="a4770-103">Login using a **personal account** (aka: Microsoft Account) or **Work or School Account**.</span></span>

1. <span data-ttu-id="a4770-104">Wählen Sie **Azure Active Directory** in der linken Navigationsleiste aus, und wählen Sie dann **App-Registrierungen (Vorschau)** unter **Manage**aus.</span><span class="sxs-lookup"><span data-stu-id="a4770-104">Select **Azure Active Directory** in the left-hand navigation, then select **App registrations (Preview)** under **Manage**.</span></span>

    ![<span data-ttu-id="a4770-105">Screenshot der APP-Registrierungen</span><span class="sxs-lookup"><span data-stu-id="a4770-105">A screenshot of the App registrations</span></span> ](./images/aad-portal-app-registrations.png)

1. <span data-ttu-id="a4770-106">Wählen Sie **neue Registrierung**aus.</span><span class="sxs-lookup"><span data-stu-id="a4770-106">Select **New registration**.</span></span> <span data-ttu-id="a4770-107">Legen Sie auf der Seite **Anwendung registrieren** die Werte wie folgt fest.</span><span class="sxs-lookup"><span data-stu-id="a4770-107">On the **Register an application** page, set the values as follows.</span></span>

    - <span data-ttu-id="a4770-108">Legen \*\*\*\* Sie Name `PHP Graph Tutorial`auf fest.</span><span class="sxs-lookup"><span data-stu-id="a4770-108">Set **Name** to `PHP Graph Tutorial`.</span></span>
    - <span data-ttu-id="a4770-109">Legen Sie **unterstützte Kontotypen** auf **Konten in einem beliebigen Organisations Verzeichnis und persönlichen Microsoft-Konten**fest.</span><span class="sxs-lookup"><span data-stu-id="a4770-109">Set **Supported account types** to **Accounts in any organizational directory and personal Microsoft accounts**.</span></span>
    - <span data-ttu-id="a4770-110">Legen Sie unter umLeitungs- **URI**die erste Dropdown `Web` Liste auf fest, und `http://localhost:8000/callback`legen Sie den Wert auf fest.</span><span class="sxs-lookup"><span data-stu-id="a4770-110">Under **Redirect URI**, set the first drop-down to `Web` and set the value to `http://localhost:8000/callback`.</span></span>

    ![Screenshot der Seite "Registrieren einer Anwendung"](./images/aad-register-an-app.png)

1. <span data-ttu-id="a4770-112">Wählen Sie **registrieren**aus.</span><span class="sxs-lookup"><span data-stu-id="a4770-112">Choose **Register**.</span></span> <span data-ttu-id="a4770-113">Kopieren Sie auf der Seite mit dem **php Graph-Lernprogramm** den Wert der **Anwendungs-ID (Client)** , und speichern Sie ihn, dann benötigen Sie ihn im nächsten Schritt.</span><span class="sxs-lookup"><span data-stu-id="a4770-113">On the **PHP Graph Tutorial** page, copy the value of the **Application (client) ID** and save it, you will need it in the next step.</span></span>

    ![Screenshot der Anwendungs-ID der neuen App-Registrierung](./images/aad-application-id.png)

1. <span data-ttu-id="a4770-115">Wählen Sie unter **Manage**die Option **Certificates & Secrets** aus.</span><span class="sxs-lookup"><span data-stu-id="a4770-115">Select **Certificates & secrets** under **Manage**.</span></span> <span data-ttu-id="a4770-116">Klicken Sie auf die Schaltfläche **neuen geheimen Client Schlüssel** .</span><span class="sxs-lookup"><span data-stu-id="a4770-116">Select the **New client secret** button.</span></span> <span data-ttu-id="a4770-117">Geben Sie einen Wert in **Description** ein, und wählen Sie eine der Optionen für **Expires** und wählen Sie **Hinzufügen**aus.</span><span class="sxs-lookup"><span data-stu-id="a4770-117">Enter a value in **Description** and select one of the options for **Expires** and choose **Add**.</span></span>

    ![Screenshot des Dialogfelds zum Hinzufügen eines geheimen Clients](./images/aad-new-client-secret.png)

1. <span data-ttu-id="a4770-119">Kopieren Sie den Client geheimen Wert, bevor Sie diese Seite verlassen.</span><span class="sxs-lookup"><span data-stu-id="a4770-119">Copy the client secret value before you leave this page.</span></span> <span data-ttu-id="a4770-120">Sie benötigen Sie im nächsten Schritt.</span><span class="sxs-lookup"><span data-stu-id="a4770-120">You will need it in the next step.</span></span>

    > [!IMPORTANT]
    > <span data-ttu-id="a4770-121">Dieser geheime Client Schlüssel wird nie wieder angezeigt, stellen Sie daher sicher, dass Sie ihn jetzt kopieren.</span><span class="sxs-lookup"><span data-stu-id="a4770-121">This client secret is never shown again, so make sure you copy it now.</span></span>

    ![Screenshot des neu hinzugefügten geheimen Clients](./images/aad-copy-client-secret.png)