<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="b05fd-101">Erstellen Sie zunächst ein neues Laravel-Projekt.</span><span class="sxs-lookup"><span data-stu-id="b05fd-101">Begin by creating a new Laravel project.</span></span>

1. <span data-ttu-id="b05fd-102">Öffnen Sie die Befehlszeilenschnittstelle (CLI), navigieren Sie zu einem Verzeichnis, in dem Sie über Berechtigungen zum Erstellen von Dateien verfügen, und führen Sie den folgenden Befehl aus, um eine neue php-app zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="b05fd-102">Open your command-line interface (CLI), navigate to a directory where you have rights to create files, and run the following command to create a new PHP app.</span></span>

    ```Shell
    laravel new graph-tutorial
    ```

1. <span data-ttu-id="b05fd-103">Navigieren Sie zum Verzeichnis **Graph-Tutorial** , und geben Sie den folgenden Befehl ein, um einen lokalen Webserver zu starten.</span><span class="sxs-lookup"><span data-stu-id="b05fd-103">Navigate to the **graph-tutorial** directory and enter the following command to start a local web server.</span></span>

    ```Shell
    php artisan serve
    ```

1. <span data-ttu-id="b05fd-104">Öffnen Sie Ihren Browser und navigieren Sie zu `http://localhost:8000`.</span><span class="sxs-lookup"><span data-stu-id="b05fd-104">Open your browser and navigate to `http://localhost:8000`.</span></span> <span data-ttu-id="b05fd-105">Wenn alles funktioniert, wird eine standardmäßige Laravel-Seite angezeigt.</span><span class="sxs-lookup"><span data-stu-id="b05fd-105">If everything is working, you will see a default Laravel page.</span></span> <span data-ttu-id="b05fd-106">Wenn diese Seite nicht angezeigt wird, überprüfen Sie die [Laravel-Dokumente](https://laravel.com/docs/7.x).</span><span class="sxs-lookup"><span data-stu-id="b05fd-106">If you don't see that page, check the [Laravel docs](https://laravel.com/docs/7.x).</span></span>

## <a name="install-packages"></a><span data-ttu-id="b05fd-107">Installieren von Paketen</span><span class="sxs-lookup"><span data-stu-id="b05fd-107">Install packages</span></span>

<span data-ttu-id="b05fd-108">Bevor Sie fortfahren, installieren Sie einige zusätzliche Pakete, die Sie später verwenden werden:</span><span class="sxs-lookup"><span data-stu-id="b05fd-108">Before moving on, install some additional packages that you will use later:</span></span>

- <span data-ttu-id="b05fd-109">[oauth2-Client](https://github.com/thephpleague/oauth2-client) für die Verarbeitung von Anmelde-und OAuth-Token-Flows.</span><span class="sxs-lookup"><span data-stu-id="b05fd-109">[oauth2-client](https://github.com/thephpleague/oauth2-client) for handling sign-in and OAuth token flows.</span></span>
- <span data-ttu-id="b05fd-110">[Microsoft-Graph](https://github.com/microsoftgraph/msgraph-sdk-php) für das tätigen von Anrufen an Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="b05fd-110">[microsoft-graph](https://github.com/microsoftgraph/msgraph-sdk-php) for making calls to Microsoft Graph.</span></span>

1. <span data-ttu-id="b05fd-111">Führen Sie den folgenden Befehl in der CLI aus.</span><span class="sxs-lookup"><span data-stu-id="b05fd-111">Run the following command in your CLI.</span></span>

    ```Shell
    composer require league/oauth2-client microsoft/microsoft-graph
    ```

## <a name="design-the-app"></a><span data-ttu-id="b05fd-112">Entwerfen der App</span><span class="sxs-lookup"><span data-stu-id="b05fd-112">Design the app</span></span>

1. <span data-ttu-id="b05fd-113">Erstellen Sie eine neue Datei im **./Resources/views** -Verzeichnis `layout.blade.php` mit dem Namen, und fügen Sie den folgenden Code hinzu.</span><span class="sxs-lookup"><span data-stu-id="b05fd-113">Create a new file in the **./resources/views** directory named `layout.blade.php` and add the following code.</span></span>

    :::code language="php" source="../demo/graph-tutorial/resources/views/layout.blade.php" id="LayoutSnippet":::

    <span data-ttu-id="b05fd-114">Dieser Code fügt [Bootstrap](http://getbootstrap.com/) zur einfachen Formatierung hinzu, und [Font Awesome](https://fontawesome.com/) für einige einfache Symbole.</span><span class="sxs-lookup"><span data-stu-id="b05fd-114">This code adds [Bootstrap](http://getbootstrap.com/) for simple styling, and [Font Awesome](https://fontawesome.com/) for some simple icons.</span></span> <span data-ttu-id="b05fd-115">Außerdem wird ein globales Layout mit einer Navigationsleiste definiert.</span><span class="sxs-lookup"><span data-stu-id="b05fd-115">It also defines a global layout with a nav bar.</span></span>

1. <span data-ttu-id="b05fd-116">Erstellen Sie ein neues Verzeichnis im `./public` Verzeichnis mit `css`dem Namen, und erstellen Sie dann eine `./public/css` neue Datei `app.css`im Verzeichnis mit dem Namen.</span><span class="sxs-lookup"><span data-stu-id="b05fd-116">Create a new directory in the `./public` directory named `css`, then create a new file in the `./public/css` directory named `app.css`.</span></span> <span data-ttu-id="b05fd-117">Fügen Sie den folgenden Code hinzu.</span><span class="sxs-lookup"><span data-stu-id="b05fd-117">Add the following code.</span></span>

    :::code language="css" source="../demo/graph-tutorial/public/css/app.css":::

1. <span data-ttu-id="b05fd-118">Öffnen Sie `./resources/views/welcome.blade.php` die Datei, und ersetzen Sie den Inhalt durch Folgendes.</span><span class="sxs-lookup"><span data-stu-id="b05fd-118">Open the `./resources/views/welcome.blade.php` file and replace its contents with the following.</span></span>

    :::code language="php" source="../demo/graph-tutorial/resources/views/welcome.blade.php" id="WelcomeSnippet":::

1. <span data-ttu-id="b05fd-119">Aktualisieren Sie die `Controller` Basisklasse in **./app/http/Controllers/Controller.php** , indem Sie der Klasse die folgende Funktion hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="b05fd-119">Update the base `Controller` class in **./app/Http/Controllers/Controller.php** by adding the following function to the class.</span></span>

    :::code language="php" source="../demo/graph-tutorial/app/Http/Controllers/Controller.php" id="LoadViewDataSnippet":::

1. <span data-ttu-id="b05fd-120">Erstellen Sie eine neue Datei im `./app/Http/Controllers` Verzeichnis mit `HomeController.php` dem Namen, und fügen Sie den folgenden Code hinzu.</span><span class="sxs-lookup"><span data-stu-id="b05fd-120">Create a new file in the `./app/Http/Controllers` directory named `HomeController.php` and add the following code.</span></span>

    :::code language="php" source="../demo/graph-tutorial/app/Http/Controllers/HomeController.php":::

1. <span data-ttu-id="b05fd-121">Aktualisieren Sie die Route `./routes/web.php` in, um den neuen Controller zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="b05fd-121">Update the route in `./routes/web.php` to use the new controller.</span></span> <span data-ttu-id="b05fd-122">Ersetzen Sie den gesamten Inhalt dieser Datei durch Folgendes:</span><span class="sxs-lookup"><span data-stu-id="b05fd-122">Replace the entire contents of this file with the following.</span></span>

    ```php
    <?php

    use Illuminate\Support\Facades\Route;

    Route::get('/', 'HomeController@welcome');
    ```

1. <span data-ttu-id="b05fd-123">Speichern Sie alle Änderungen, und starten Sie den Server neu.</span><span class="sxs-lookup"><span data-stu-id="b05fd-123">Save all of your changes and restart the server.</span></span> <span data-ttu-id="b05fd-124">Nun sollte die APP sehr unterschiedlich aussehen.</span><span class="sxs-lookup"><span data-stu-id="b05fd-124">Now, the app should look very different.</span></span>

    ![Screenshot der neu gestalteten Homepage](./images/create-app-01.png)
