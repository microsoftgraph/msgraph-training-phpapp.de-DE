<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="8c826-101">Erstellen Sie zunächst ein neues Laravel-Projekt.</span><span class="sxs-lookup"><span data-stu-id="8c826-101">Begin by creating a new Laravel project.</span></span>

1. <span data-ttu-id="8c826-102">Öffnen Sie die Befehlszeilenschnittstelle (CLI), navigieren Sie zu einem Verzeichnis, in dem Sie über Berechtigungen zum Erstellen von Dateien verfügen, und führen Sie den folgenden Befehl aus, um eine neue php-app zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="8c826-102">Open your command-line interface (CLI), navigate to a directory where you have rights to create files, and run the following command to create a new PHP app.</span></span>

    ```Shell
    laravel new graph-tutorial
    ```

1. <span data-ttu-id="8c826-103">Navigieren Sie zum Verzeichnis **Graph-Tutorial** , und geben Sie den folgenden Befehl ein, um einen lokalen Webserver zu starten.</span><span class="sxs-lookup"><span data-stu-id="8c826-103">Navigate to the **graph-tutorial** directory and enter the following command to start a local web server.</span></span>

    ```Shell
    php artisan serve
    ```

1. <span data-ttu-id="8c826-104">Öffnen Sie Ihren Browser und navigieren Sie zu `http://localhost:8000`.</span><span class="sxs-lookup"><span data-stu-id="8c826-104">Open your browser and navigate to `http://localhost:8000`.</span></span> <span data-ttu-id="8c826-105">Wenn alles funktioniert, wird eine standardmäßige Laravel-Seite angezeigt.</span><span class="sxs-lookup"><span data-stu-id="8c826-105">If everything is working, you will see a default Laravel page.</span></span> <span data-ttu-id="8c826-106">Wenn diese Seite nicht angezeigt wird, überprüfen Sie die [Laravel-Dokumente](https://laravel.com/docs/7.x).</span><span class="sxs-lookup"><span data-stu-id="8c826-106">If you don't see that page, check the [Laravel docs](https://laravel.com/docs/7.x).</span></span>

## <a name="install-packages"></a><span data-ttu-id="8c826-107">Installieren von Paketen</span><span class="sxs-lookup"><span data-stu-id="8c826-107">Install packages</span></span>

<span data-ttu-id="8c826-108">Bevor Sie fortfahren, installieren Sie einige zusätzliche Pakete, die Sie später verwenden werden:</span><span class="sxs-lookup"><span data-stu-id="8c826-108">Before moving on, install some additional packages that you will use later:</span></span>

- <span data-ttu-id="8c826-109">[oauth2-Client](https://github.com/thephpleague/oauth2-client) für die Verarbeitung von Anmelde-und OAuth-Token-Flows.</span><span class="sxs-lookup"><span data-stu-id="8c826-109">[oauth2-client](https://github.com/thephpleague/oauth2-client) for handling sign-in and OAuth token flows.</span></span>
- <span data-ttu-id="8c826-110">[Microsoft-Graph](https://github.com/microsoftgraph/msgraph-sdk-php) für das tätigen von Anrufen an Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="8c826-110">[microsoft-graph](https://github.com/microsoftgraph/msgraph-sdk-php) for making calls to Microsoft Graph.</span></span>

1. <span data-ttu-id="8c826-111">Führen Sie den folgenden Befehl aus, um die vorhandene Version von zu entfernen `guzzlehttp/guzzle` .</span><span class="sxs-lookup"><span data-stu-id="8c826-111">Run the following command to remove the existing version of `guzzlehttp/guzzle`.</span></span> <span data-ttu-id="8c826-112">Die von Laravel installierte Version steht in Konflikt mit der Version, die für das Microsoft Graph php SDK erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="8c826-112">The version installed by Laravel conflicts with the version required by the Microsoft Graph PHP SDK.</span></span>

    ```Shell
    composer remove guzzlehttp/guzzle
    ```

1. <span data-ttu-id="8c826-113">Führen Sie den folgenden Befehl in der CLI aus.</span><span class="sxs-lookup"><span data-stu-id="8c826-113">Run the following command in your CLI.</span></span>

    ```Shell
    composer require league/oauth2-client microsoft/microsoft-graph
    ```

## <a name="design-the-app"></a><span data-ttu-id="8c826-114">Entwerfen der App</span><span class="sxs-lookup"><span data-stu-id="8c826-114">Design the app</span></span>

1. <span data-ttu-id="8c826-115">Erstellen Sie eine neue Datei im **./Resources/views** -Verzeichnis mit dem Namen `layout.blade.php` , und fügen Sie den folgenden Code hinzu.</span><span class="sxs-lookup"><span data-stu-id="8c826-115">Create a new file in the **./resources/views** directory named `layout.blade.php` and add the following code.</span></span>

    :::code language="php" source="../demo/graph-tutorial/resources/views/layout.blade.php" id="LayoutSnippet":::

    <span data-ttu-id="8c826-116">Dieser Code fügt [Bootstrap](http://getbootstrap.com/) zur einfachen Formatierung hinzu, und [Font Awesome](https://fontawesome.com/) für einige einfache Symbole.</span><span class="sxs-lookup"><span data-stu-id="8c826-116">This code adds [Bootstrap](http://getbootstrap.com/) for simple styling, and [Font Awesome](https://fontawesome.com/) for some simple icons.</span></span> <span data-ttu-id="8c826-117">Außerdem wird ein globales Layout mit einer Navigationsleiste definiert.</span><span class="sxs-lookup"><span data-stu-id="8c826-117">It also defines a global layout with a nav bar.</span></span>

1. <span data-ttu-id="8c826-118">Erstellen Sie ein neues Verzeichnis im `./public` Verzeichnis mit dem Namen `css` , und erstellen Sie dann eine neue Datei im `./public/css` Verzeichnis mit dem Namen `app.css` .</span><span class="sxs-lookup"><span data-stu-id="8c826-118">Create a new directory in the `./public` directory named `css`, then create a new file in the `./public/css` directory named `app.css`.</span></span> <span data-ttu-id="8c826-119">Fügen Sie den folgenden Code hinzu.</span><span class="sxs-lookup"><span data-stu-id="8c826-119">Add the following code.</span></span>

    :::code language="css" source="../demo/graph-tutorial/public/css/app.css":::

1. <span data-ttu-id="8c826-120">Öffnen Sie die `./resources/views/welcome.blade.php` Datei, und ersetzen Sie den Inhalt durch Folgendes.</span><span class="sxs-lookup"><span data-stu-id="8c826-120">Open the `./resources/views/welcome.blade.php` file and replace its contents with the following.</span></span>

    :::code language="php" source="../demo/graph-tutorial/resources/views/welcome.blade.php" id="WelcomeSnippet":::

1. <span data-ttu-id="8c826-121">Aktualisieren Sie die Basis `Controller` Klasse in **./app/http/Controllers/Controller.php** , indem Sie der Klasse die folgende Funktion hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="8c826-121">Update the base `Controller` class in **./app/Http/Controllers/Controller.php** by adding the following function to the class.</span></span>

    :::code language="php" source="../demo/graph-tutorial/app/Http/Controllers/Controller.php" id="LoadViewDataSnippet":::

1. <span data-ttu-id="8c826-122">Erstellen Sie eine neue Datei im `./app/Http/Controllers` Verzeichnis mit dem Namen `HomeController.php` , und fügen Sie den folgenden Code hinzu.</span><span class="sxs-lookup"><span data-stu-id="8c826-122">Create a new file in the `./app/Http/Controllers` directory named `HomeController.php` and add the following code.</span></span>

    :::code language="php" source="../demo/graph-tutorial/app/Http/Controllers/HomeController.php":::

1. <span data-ttu-id="8c826-123">Aktualisieren Sie die Route in `./routes/web.php` , um den neuen Controller zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="8c826-123">Update the route in `./routes/web.php` to use the new controller.</span></span> <span data-ttu-id="8c826-124">Ersetzen Sie den gesamten Inhalt dieser Datei durch Folgendes:</span><span class="sxs-lookup"><span data-stu-id="8c826-124">Replace the entire contents of this file with the following.</span></span>

    ```php
    <?php

    use Illuminate\Support\Facades\Route;

    Route::get('/', 'HomeController@welcome');
    ```

1. <span data-ttu-id="8c826-125">Speichern Sie alle Änderungen, und starten Sie den Server neu.</span><span class="sxs-lookup"><span data-stu-id="8c826-125">Save all of your changes and restart the server.</span></span> <span data-ttu-id="8c826-126">Nun sollte die APP sehr unterschiedlich aussehen.</span><span class="sxs-lookup"><span data-stu-id="8c826-126">Now, the app should look very different.</span></span>

    ![Screenshot der neu gestalteten Homepage](./images/create-app-01.png)
