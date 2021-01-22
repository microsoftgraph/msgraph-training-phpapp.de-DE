# <a name="how-to-run-the-completed-project"></a><span data-ttu-id="a25a3-101">Ausführen des abgeschlossenen Projekts</span><span class="sxs-lookup"><span data-stu-id="a25a3-101">How to run the completed project</span></span>

## <a name="prerequisites"></a><span data-ttu-id="a25a3-102">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="a25a3-102">Prerequisites</span></span>

<span data-ttu-id="a25a3-103">Zum Ausführen des abgeschlossenen Projekts in diesem Ordner benötigen Sie Folgendes:</span><span class="sxs-lookup"><span data-stu-id="a25a3-103">To run the completed project in this folder, you need the following:</span></span>

- <span data-ttu-id="a25a3-104">[PHP](http://php.net/downloads.php) auf Ihrem Entwicklungscomputer installiert.</span><span class="sxs-lookup"><span data-stu-id="a25a3-104">[PHP](http://php.net/downloads.php) installed on your development machine.</span></span> <span data-ttu-id="a25a3-105">Wenn Sie nicht über PHP verfügen, besuchen Sie den vorherigen Link für Downloadoptionen.</span><span class="sxs-lookup"><span data-stu-id="a25a3-105">If you do not have PHP, visit the previous link for download options.</span></span> <span data-ttu-id="a25a3-106">(**Hinweis:** Dieses Lernprogramm wurde mit PHP Version 7.4.4 geschrieben.</span><span class="sxs-lookup"><span data-stu-id="a25a3-106">(**Note:** This tutorial was written with PHP version 7.4.4.</span></span> <span data-ttu-id="a25a3-107">Die Schritte in diesem Handbuch funktionieren möglicherweise mit anderen Versionen, wurden jedoch noch nicht getestet.)</span><span class="sxs-lookup"><span data-stu-id="a25a3-107">The steps in this guide may work with other versions, but that has not been tested.)</span></span>
- <span data-ttu-id="a25a3-108">[Composer](https://getcomposer.org/) auf Ihrem Entwicklungscomputer installiert.</span><span class="sxs-lookup"><span data-stu-id="a25a3-108">[Composer](https://getcomposer.org/) installed on your development machine.</span></span>
- <span data-ttu-id="a25a3-109">[Laravel](https://laravel.com/) wurde auf Ihrem Entwicklungscomputer installiert.</span><span class="sxs-lookup"><span data-stu-id="a25a3-109">[Laravel](https://laravel.com/) installed on your development machine.</span></span>
- <span data-ttu-id="a25a3-110">Entweder ein persönliches Microsoft-Konto mit einem Postfach auf Outlook.com oder ein Microsoft-Arbeits- oder Schulkonto.</span><span class="sxs-lookup"><span data-stu-id="a25a3-110">Either a personal Microsoft account with a mailbox on Outlook.com, or a Microsoft work or school account.</span></span>

<span data-ttu-id="a25a3-111">Wenn Sie kein Microsoft-Konto haben, gibt es mehrere Optionen, um ein kostenloses Konto zu erhalten:</span><span class="sxs-lookup"><span data-stu-id="a25a3-111">If you don't have a Microsoft account, there are a couple of options to get a free account:</span></span>

- <span data-ttu-id="a25a3-112">Sie können [sich für ein neues persönliches Microsoft-Konto registrieren.](https://signup.live.com/signup?wa=wsignin1.0&rpsnv=12&ct=1454618383&rver=6.4.6456.0&wp=MBI_SSL_SHARED&wreply=https://mail.live.com/default.aspx&id=64855&cbcxt=mai&bk=1454618383&uiflavor=web&uaid=b213a65b4fdc484382b6622b3ecaa547&mkt=E-US&lc=1033&lic=1)</span><span class="sxs-lookup"><span data-stu-id="a25a3-112">You can [sign up for a new personal Microsoft account](https://signup.live.com/signup?wa=wsignin1.0&rpsnv=12&ct=1454618383&rver=6.4.6456.0&wp=MBI_SSL_SHARED&wreply=https://mail.live.com/default.aspx&id=64855&cbcxt=mai&bk=1454618383&uiflavor=web&uaid=b213a65b4fdc484382b6622b3ecaa547&mkt=E-US&lc=1033&lic=1).</span></span>
- <span data-ttu-id="a25a3-113">Sie können [sich für das Office 365-Entwicklerprogramm](https://developer.microsoft.com/office/dev-program) registrieren, um ein kostenloses Office 365-Abonnement zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="a25a3-113">You can [sign up for the Office 365 Developer Program](https://developer.microsoft.com/office/dev-program) to get a free Office 365 subscription.</span></span>

## <a name="register-a-web-application-with-the-azure-active-directory-admin-center"></a><span data-ttu-id="a25a3-114">Registrieren einer Webanwendung beim Azure Active Directory Admin Center</span><span class="sxs-lookup"><span data-stu-id="a25a3-114">Register a web application with the Azure Active Directory admin center</span></span>

1. <span data-ttu-id="a25a3-115">Öffnen Sie einen Browser, und navigieren Sie zum [Azure Active Directory Admin Center](https://aad.portal.azure.com).</span><span class="sxs-lookup"><span data-stu-id="a25a3-115">Open a browser and navigate to the [Azure Active Directory admin center](https://aad.portal.azure.com).</span></span> <span data-ttu-id="a25a3-116">Melden Sie sich mit einem **persönlichen Konto** (auch: Microsoft-Konto) oder einem **Geschäfts- oder Schulkonto** an.</span><span class="sxs-lookup"><span data-stu-id="a25a3-116">Login using a **personal account** (aka: Microsoft Account) or **Work or School Account**.</span></span>

1. <span data-ttu-id="a25a3-117">Wählen Sie in der linken Navigationsleiste **Azure Active Directory** aus, und wählen Sie dann **App-Registrierungen** unter **Verwalten** aus.</span><span class="sxs-lookup"><span data-stu-id="a25a3-117">Select **Azure Active Directory** in the left-hand navigation, then select **App registrations** under **Manage**.</span></span>

    ![<span data-ttu-id="a25a3-118">Screenshot der APP-Registrierungen</span><span class="sxs-lookup"><span data-stu-id="a25a3-118">A screenshot of the App registrations</span></span> ](/tutorial/images/aad-portal-app-registrations.png)

1. <span data-ttu-id="a25a3-119">Wählen Sie **Neue Registrierung** aus.</span><span class="sxs-lookup"><span data-stu-id="a25a3-119">Select **New registration**.</span></span> <span data-ttu-id="a25a3-120">Legen Sie auf der Seite **Anwendung registrieren** die Werte wie folgt fest.</span><span class="sxs-lookup"><span data-stu-id="a25a3-120">On the **Register an application** page, set the values as follows.</span></span>

    - <span data-ttu-id="a25a3-121">Legen Sie **Name** auf `PHP Graph Tutorial` fest.</span><span class="sxs-lookup"><span data-stu-id="a25a3-121">Set **Name** to `PHP Graph Tutorial`.</span></span>
    - <span data-ttu-id="a25a3-122">Legen Sie **Unterstützte Kontotypen** auf **Konten in allen Organisationsverzeichnissen und persönliche Microsoft-Konten** fest.</span><span class="sxs-lookup"><span data-stu-id="a25a3-122">Set **Supported account types** to **Accounts in any organizational directory and personal Microsoft accounts**.</span></span>
    - <span data-ttu-id="a25a3-123">Legen Sie unter **Umleitungs-URI** die erste Dropdownoption auf `Web` fest, und legen Sie den Wert auf `http://localhost:8000/callback` fest.</span><span class="sxs-lookup"><span data-stu-id="a25a3-123">Under **Redirect URI**, set the first drop-down to `Web` and set the value to `http://localhost:8000/callback`.</span></span>

    ![Screenshot der Seite "Anwendung registrieren"](/tutorial/images/aad-register-an-app.png)

1. <span data-ttu-id="a25a3-125">Wählen Sie **Registrieren** aus.</span><span class="sxs-lookup"><span data-stu-id="a25a3-125">Choose **Register**.</span></span> <span data-ttu-id="a25a3-126">Kopieren Sie auf der **Php Graph-Lernprogrammseite** den Wert der **Anwendungs-ID (Client-ID),** und speichern Sie ihn. Sie benötigen ihn im nächsten Schritt.</span><span class="sxs-lookup"><span data-stu-id="a25a3-126">On the **PHP Graph Tutorial** page, copy the value of the **Application (client) ID** and save it, you will need it in the next step.</span></span>

    ![Screenshot der Anwendungs-ID der neuen App-Registrierung](/tutorial/images/aad-application-id.png)

1. <span data-ttu-id="a25a3-128">Wählen Sie unter **Verwalten** die Option **Zertifikate und Geheime Clientschlüssel** aus.</span><span class="sxs-lookup"><span data-stu-id="a25a3-128">Select **Certificates & secrets** under **Manage**.</span></span> <span data-ttu-id="a25a3-129">Wählen Sie die Schaltfläche **Neuen geheimen Clientschlüssel** aus.</span><span class="sxs-lookup"><span data-stu-id="a25a3-129">Select the **New client secret** button.</span></span> <span data-ttu-id="a25a3-130">Geben Sie einen Wert unter **Beschreibung** ein, wählen Sie eine der Optionen für **Gilt bis** aus, und wählen Sie dann **Hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="a25a3-130">Enter a value in **Description** and select one of the options for **Expires** and choose **Add**.</span></span>

    ![Screenshot des Dialogfelds "Geheimen Clientschlüssel hinzufügen"](/tutorial/images/aad-new-client-secret.png)

1. <span data-ttu-id="a25a3-132">Kopieren Sie den Wert des geheimen Clientschlüssels, bevor Sie diese Seite verlassen.</span><span class="sxs-lookup"><span data-stu-id="a25a3-132">Copy the client secret value before you leave this page.</span></span> <span data-ttu-id="a25a3-133">Sie benötigen ihn im nächsten Schritt.</span><span class="sxs-lookup"><span data-stu-id="a25a3-133">You will need it in the next step.</span></span>

    > [!IMPORTANT]
    > <span data-ttu-id="a25a3-134">Dieser geheime Clientschlüssel wird nicht noch einmal angezeigt, stellen Sie daher sicher, dass Sie ihn jetzt kopieren.</span><span class="sxs-lookup"><span data-stu-id="a25a3-134">This client secret is never shown again, so make sure you copy it now.</span></span>

    ![Screenshot des neu hinzugefügten Clientschlüssels](/tutorial/images/aad-copy-client-secret.png)

## <a name="configure-the-sample"></a><span data-ttu-id="a25a3-136">Konfigurieren des Beispiels</span><span class="sxs-lookup"><span data-stu-id="a25a3-136">Configure the sample</span></span>

1. <span data-ttu-id="a25a3-137">Benennen Sie die `example.env` Datei um in `.env` .</span><span class="sxs-lookup"><span data-stu-id="a25a3-137">Rename the `example.env` file to `.env`.</span></span>
1. <span data-ttu-id="a25a3-138">Bearbeiten Sie `.env` die Datei, und nehmen Sie die folgenden Änderungen vor.</span><span class="sxs-lookup"><span data-stu-id="a25a3-138">Edit the `.env` file and make the following changes.</span></span>
    1. <span data-ttu-id="a25a3-139">Ersetzen `YOUR_APP_ID_HERE` Sie diese durch die **Anwendungs-ID,** die Sie aus dem App-Registrierungsportal erhalten haben.</span><span class="sxs-lookup"><span data-stu-id="a25a3-139">Replace `YOUR_APP_ID_HERE` with the **Application Id** you got from the App Registration Portal.</span></span>
    1. <span data-ttu-id="a25a3-140">Ersetzen `YOUR_APP_PASSWORD_HERE` Sie das Kennwort aus dem App-Registrierungsportal.</span><span class="sxs-lookup"><span data-stu-id="a25a3-140">Replace `YOUR_APP_PASSWORD_HERE` with the password you got from the App Registration Portal.</span></span>
1. <span data-ttu-id="a25a3-141">Navigieren Sie in der Befehlszeilenschnittstelle (CLI) zu diesem Verzeichnis, und führen Sie den folgenden Befehl aus, um die Installationsanforderungen zu erfüllen.</span><span class="sxs-lookup"><span data-stu-id="a25a3-141">In your command-line interface (CLI), navigate to this directory and run the following command to install requirements.</span></span>

    ```Shell
    composer install
    ```

1. <span data-ttu-id="a25a3-142">Führen Sie in der Befehlszeilenschnittstelle (CLI) den folgenden Befehl aus, um einen Anwendungsschlüssel zu generieren.</span><span class="sxs-lookup"><span data-stu-id="a25a3-142">In your command-line interface (CLI), run the following command to generate an application key.</span></span>

    ```Shell
    php artisan key:generate
    ```

## <a name="run-the-sample"></a><span data-ttu-id="a25a3-143">Ausführen des Beispiels</span><span class="sxs-lookup"><span data-stu-id="a25a3-143">Run the sample</span></span>

1. <span data-ttu-id="a25a3-144">Führen Sie den folgenden Befehl in Ihrer CLI aus, um die Anwendung zu starten.</span><span class="sxs-lookup"><span data-stu-id="a25a3-144">Run the following command in your CLI to start the application.</span></span>

    ```Shell
    php artisan serve
    ```

1. <span data-ttu-id="a25a3-145">Öffnen Sie einen Browser, und navigieren Sie zu `http://localhost:8000`.</span><span class="sxs-lookup"><span data-stu-id="a25a3-145">Open a browser and browse to `http://localhost:8000`.</span></span>
