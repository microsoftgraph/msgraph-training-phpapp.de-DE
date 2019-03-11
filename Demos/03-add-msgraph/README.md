# <a name="how-to-run-the-completed-project"></a><span data-ttu-id="4477e-101">Ausführen des abgeschlossenen Projekts</span><span class="sxs-lookup"><span data-stu-id="4477e-101">How to run the completed project</span></span>

## <a name="prerequisites"></a><span data-ttu-id="4477e-102">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="4477e-102">Prerequisites</span></span>

<span data-ttu-id="4477e-103">Zum Ausführen des abgeschlossenen Projekts in diesem Ordner benötigen Sie Folgendes:</span><span class="sxs-lookup"><span data-stu-id="4477e-103">To run the completed project in this folder, you need the following:</span></span>

- <span data-ttu-id="4477e-104">[Php](http://php.net/downloads.php) auf dem Entwicklungscomputer installiert.</span><span class="sxs-lookup"><span data-stu-id="4477e-104">[PHP](http://php.net/downloads.php) installed on your development machine.</span></span> <span data-ttu-id="4477e-105">Wenn Sie nicht über PHP verfügen, besuchen Sie den vorherigen Link für Downloadoptionen.</span><span class="sxs-lookup"><span data-stu-id="4477e-105">If you do not have PHP, visit the previous link for download options.</span></span> <span data-ttu-id="4477e-106">(**Hinweis:** dieses Tutorial wurde mit PHP Version 7,2 geschrieben.</span><span class="sxs-lookup"><span data-stu-id="4477e-106">(**Note:** This tutorial was written with PHP version 7.2.</span></span> <span data-ttu-id="4477e-107">Die Schritte in diesem Handbuch funktionieren möglicherweise mit anderen Versionen, die jedoch nicht getestet wurden.)</span><span class="sxs-lookup"><span data-stu-id="4477e-107">The steps in this guide may work with other versions, but that has not been tested.)</span></span>
- <span data-ttu-id="4477e-108">[Komponist](https://getcomposer.org/) , der auf dem Entwicklungscomputer installiert ist.</span><span class="sxs-lookup"><span data-stu-id="4477e-108">[Composer](https://getcomposer.org/) installed on your development machine.</span></span>
- <span data-ttu-id="4477e-109">[Laravel](https://laravel.com/) wird auf dem Entwicklungscomputer installiert.</span><span class="sxs-lookup"><span data-stu-id="4477e-109">[Laravel](https://laravel.com/) installed on your development machine.</span></span>
- <span data-ttu-id="4477e-110">Entweder ein persönliches Microsoft-Konto mit einem Postfach auf Outlook.com oder ein Microsoft-Geschäfts-oder Schulkonto.</span><span class="sxs-lookup"><span data-stu-id="4477e-110">Either a personal Microsoft account with a mailbox on Outlook.com, or a Microsoft work or school account.</span></span>

<span data-ttu-id="4477e-111">Wenn Sie nicht über ein Microsoft-Konto verfügen, gibt es eine Reihe von Optionen, um ein kostenloses Konto zu erhalten:</span><span class="sxs-lookup"><span data-stu-id="4477e-111">If you don't have a Microsoft account, there are a couple of options to get a free account:</span></span>

- <span data-ttu-id="4477e-112">Sie können [sich für ein neues persönliches Microsoft-Konto registrieren](https://signup.live.com/signup?wa=wsignin1.0&rpsnv=12&ct=1454618383&rver=6.4.6456.0&wp=MBI_SSL_SHARED&wreply=https://mail.live.com/default.aspx&id=64855&cbcxt=mai&bk=1454618383&uiflavor=web&uaid=b213a65b4fdc484382b6622b3ecaa547&mkt=E-US&lc=1033&lic=1).</span><span class="sxs-lookup"><span data-stu-id="4477e-112">You can [sign up for a new personal Microsoft account](https://signup.live.com/signup?wa=wsignin1.0&rpsnv=12&ct=1454618383&rver=6.4.6456.0&wp=MBI_SSL_SHARED&wreply=https://mail.live.com/default.aspx&id=64855&cbcxt=mai&bk=1454618383&uiflavor=web&uaid=b213a65b4fdc484382b6622b3ecaa547&mkt=E-US&lc=1033&lic=1).</span></span>
- <span data-ttu-id="4477e-113">Sie können [sich für das office 365-Entwicklerprogramm anmelden](https://developer.microsoft.com/office/dev-program) , um ein kostenloses Office 365-Abonnement zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="4477e-113">You can [sign up for the Office 365 Developer Program](https://developer.microsoft.com/office/dev-program) to get a free Office 365 subscription.</span></span>

## <a name="register-a-web-application-with-the-application-registration-portal"></a><span data-ttu-id="4477e-114">Registrieren einer Webanwendung mit dem Anwendungs Registrierungs Portal</span><span class="sxs-lookup"><span data-stu-id="4477e-114">Register a web application with the Application Registration Portal</span></span>

1. <span data-ttu-id="4477e-115">Öffnen Sie einen Browser, und navigieren Sie zum [Anwendungs Registrierungs Portal](https://apps.dev.microsoft.com).</span><span class="sxs-lookup"><span data-stu-id="4477e-115">Open a browser and navigate to the [Application Registration Portal](https://apps.dev.microsoft.com).</span></span> <span data-ttu-id="4477e-116">Melden Sie sich über ein **persönliches Konto** (aka: Microsoft-Konto) oder ein Geschäfts- **oder Schulkonto**an.</span><span class="sxs-lookup"><span data-stu-id="4477e-116">Login using a **personal account** (aka: Microsoft Account) or **Work or School Account**.</span></span>

1. <span data-ttu-id="4477e-117">Wählen Sie oben auf der Seite **eine APP hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="4477e-117">Select **Add an app** at the top of the page.</span></span>

    > <span data-ttu-id="4477e-118">**Hinweis:** Wenn auf der Seite mehr als eine Schaltfläche **app hinzufügen** angezeigt wird, wählen Sie diejenige aus, die der Liste **konvergierter apps** entspricht.</span><span class="sxs-lookup"><span data-stu-id="4477e-118">**Note:** If you see more than one **Add an app** button on the page, select the one that corresponds to the **Converged apps** list.</span></span>

1. <span data-ttu-id="4477e-119">Legen Sie auf der Seite **Anwendung registrieren** den **Anwendungsnamen** auf **php Graph Tutorial** fest, und wählen Sie **Erstellen**aus.</span><span class="sxs-lookup"><span data-stu-id="4477e-119">On the **Register your application** page, set the **Application Name** to **PHP Graph Tutorial** and select **Create**.</span></span>

    ![Screenshot des Erstellens einer neuen app in der APP-Registrierungs Portal-Website](/tutorial/images/arp-create-app-01.png)

1. <span data-ttu-id="4477e-121">Kopieren Sie auf der Seite mit der Registrierung für das **php Graph-Lernprogramm** im Abschnitt **Eigenschaften** die **Anwendungs-ID** , so wie Sie Sie später benötigen.</span><span class="sxs-lookup"><span data-stu-id="4477e-121">On the **PHP Graph Tutorial Registration** page, under the **Properties** section, copy the **Application Id** as you will need it later.</span></span>

    ![Screenshot der neu erstellten Anwendungs-ID](/tutorial/images/arp-create-app-02.png)

1. <span data-ttu-id="4477e-123">Scrollen Sie nach unten zum Abschnitt **Anwendungs Geheimnisse** .</span><span class="sxs-lookup"><span data-stu-id="4477e-123">Scroll down to the **Application Secrets** section.</span></span>

    1. <span data-ttu-id="4477e-124">Wählen Sie **Neues Kennwort generieren**aus.</span><span class="sxs-lookup"><span data-stu-id="4477e-124">Select **Generate New Password**.</span></span>
    1. <span data-ttu-id="4477e-125">Kopieren Sie im Dialogfeld **Neues Kennwort generiert** den Inhalt des Felds, so wie Sie es später benötigen.</span><span class="sxs-lookup"><span data-stu-id="4477e-125">In the **New password generated** dialog, copy the contents of the box as you will need it later.</span></span>

        > <span data-ttu-id="4477e-126">**Wichtig:** Dieses Kennwort wird nie wieder angezeigt, stellen Sie daher sicher, dass Sie es jetzt kopieren.</span><span class="sxs-lookup"><span data-stu-id="4477e-126">**Important:** This password is never shown again, so make sure you copy it now.</span></span>

    ![Screenshot des Kennworts der neu erstellten Anwendung](/tutorial/images/arp-create-app-03.png)

1. <span data-ttu-id="4477e-128">Scrollen Sie nach unten zum Abschnitt **Plattformen** .</span><span class="sxs-lookup"><span data-stu-id="4477e-128">Scroll down to the **Platforms** section.</span></span>

    1. <span data-ttu-id="4477e-129">Wählen Sie **Plattform hinzufügen**aus.</span><span class="sxs-lookup"><span data-stu-id="4477e-129">Select **Add Platform**.</span></span>
    1. <span data-ttu-id="4477e-130">Wählen Sie im Dialogfeld **Plattform hinzufügen** die Option **Web**aus.</span><span class="sxs-lookup"><span data-stu-id="4477e-130">In the **Add Platform** dialog, select **Web**.</span></span>

        ![Screenshot Erstellen einer Plattform für die APP](/tutorial/images/arp-create-app-04.png)

    1. <span data-ttu-id="4477e-132">Geben Sie \*\*\*\* im Feld Webplattform die URL `http://localhost:8000/callback` für die Umleitungs- **URLs**ein.</span><span class="sxs-lookup"><span data-stu-id="4477e-132">In the **Web** platform box, enter the URL `http://localhost:8000/callback` for the **Redirect URLs**.</span></span>

        ![Screenshot der neu hinzugefügten Webplattform für die Anwendung](/tutorial/images/arp-create-app-05.png)

1. <span data-ttu-id="4477e-134">Scrollen Sie zum unteren Rand der Seite, und wählen Sie **Speichern**aus.</span><span class="sxs-lookup"><span data-stu-id="4477e-134">Scroll to the bottom of the page and select **Save**.</span></span>

## <a name="configure-the-sample"></a><span data-ttu-id="4477e-135">Konfigurieren des Beispiels</span><span class="sxs-lookup"><span data-stu-id="4477e-135">Configure the sample</span></span>

1. <span data-ttu-id="4477e-136">Benennen Sie `.env.example` die Datei `.env`in um.</span><span class="sxs-lookup"><span data-stu-id="4477e-136">Rename the `.env.example` file to `.env`.</span></span>
1. <span data-ttu-id="4477e-137">Bearbeiten Sie `.env` die Datei, und nehmen Sie die folgenden Änderungen vor.</span><span class="sxs-lookup"><span data-stu-id="4477e-137">Edit the `.env` file and make the following changes.</span></span>
    1. <span data-ttu-id="4477e-138">Ersetzen `YOUR_APP_ID_HERE` Sie durch die **Anwendungs-ID** , die Sie im App-Registrierungs Portal erhalten haben.</span><span class="sxs-lookup"><span data-stu-id="4477e-138">Replace `YOUR_APP_ID_HERE` with the **Application Id** you got from the App Registration Portal.</span></span>
    1. <span data-ttu-id="4477e-139">Ersetzen `YOUR_APP_PASSWORD_HERE` Sie durch das Kennwort, das Sie im App-Registrierungs Portal erhalten haben.</span><span class="sxs-lookup"><span data-stu-id="4477e-139">Replace `YOUR_APP_PASSWORD_HERE` with the password you got from the App Registration Portal.</span></span>
1. <span data-ttu-id="4477e-140">Navigieren Sie in der Befehlszeilenschnittstelle zu diesem Verzeichnis, und führen Sie den folgenden Befehl aus, um die Anforderungen zu installieren.</span><span class="sxs-lookup"><span data-stu-id="4477e-140">In your command-line interface (CLI), navigate to this directory and run the following command to install requirements.</span></span>

    ```Shell
    composer install
    ```
1. <span data-ttu-id="4477e-141">Führen Sie in der Befehlszeilenschnittstelle (CLI) den folgenden Befehl aus, um einen Anwendungsschlüssel zu generieren.</span><span class="sxs-lookup"><span data-stu-id="4477e-141">In your command-line interface (CLI), run the following command to generate an application key.</span></span>

    ```Shell
    php artisan key:generate
    ```

## <a name="run-the-sample"></a><span data-ttu-id="4477e-142">Ausführen des Beispiels</span><span class="sxs-lookup"><span data-stu-id="4477e-142">Run the sample</span></span>

1. <span data-ttu-id="4477e-143">Führen Sie den folgenden Befehl in der CLI aus, um die Anwendung zu starten.</span><span class="sxs-lookup"><span data-stu-id="4477e-143">Run the following command in your CLI to start the application.</span></span>

    ```Shell
    php artisan serve
    ```

1. <span data-ttu-id="4477e-144">Öffnen Sie einen Browser, und navigieren Sie zu `http://localhost:8000`.</span><span class="sxs-lookup"><span data-stu-id="4477e-144">Open a browser and browse to `http://localhost:8000`.</span></span>