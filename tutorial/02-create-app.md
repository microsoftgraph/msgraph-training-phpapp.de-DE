<!-- markdownlint-disable MD002 MD041 -->

Erstellen Sie zunächst ein neues Laravel-Projekt.

1. Öffnen Sie Die Befehlszeilenschnittstelle (CLI), navigieren Sie zu einem Verzeichnis, in dem Sie über die Rechte zum Erstellen von Dateien verfügen, und führen Sie den folgenden Befehl aus, um eine neue PHP-App zu erstellen.

    ```Shell
    laravel new graph-tutorial
    ```

1. Navigieren Sie zum **Verzeichnis des Graph-Lernprogramms,** und geben Sie den folgenden Befehl ein, um einen lokalen Webserver zu starten.

    ```Shell
    php artisan serve
    ```

1. Öffnen Sie Ihren Browser und navigieren Sie zu `http://localhost:8000`. Wenn alles funktioniert, wird eine standardmäßige Laravel-Seite angezeigt. Wenn diese Seite nicht angezeigt wird, überprüfen Sie die [Laravel-Dokumente.](https://laravel.com/docs/8.x)

## <a name="install-packages"></a>Installieren von Paketen

Installieren Sie vor dem Wechsel einige zusätzliche Pakete, die Sie später verwenden werden:

- [oauth2-client](https://github.com/thephpleague/oauth2-client) for handling sign-in and OAuth token flows.
- [microsoft-graph](https://github.com/microsoftgraph/msgraph-sdk-php) für Aufrufe an Microsoft Graph.

1. Führen Sie den folgenden Befehl in Der CLI aus.

    ```Shell
    composer require league/oauth2-client microsoft/microsoft-graph
    ```

## <a name="design-the-app"></a>Entwerfen der App

1. Erstellen Sie eine neue Datei im Verzeichnis **./resources/views** mit dem `layout.blade.php` Namen, und fügen Sie den folgenden Code hinzu.

    :::code language="php" source="../demo/graph-tutorial/resources/views/layout.blade.php" id="LayoutSnippet":::

    Dieser Code fügt [Bootstrap](http://getbootstrap.com/) zur einfachen Formatierung hinzu, und [Font Awesome](https://fontawesome.com/) für einige einfache Symbole. Außerdem wird ein globales Layout mit einer Navigationsleiste definiert.

1. Erstellen Sie ein neues Verzeichnis im Verzeichnis namens , und erstellen Sie dann eine `./public` neue Datei im Verzeichnis mit dem Namen `css` `./public/css` `app.css` . Fügen Sie den folgenden Code hinzu.

    :::code language="css" source="../demo/graph-tutorial/public/css/app.css":::

1. Öffnen Sie `./resources/views/welcome.blade.php` die Datei, und ersetzen Sie ihren Inhalt durch Folgendes.

    :::code language="php" source="../demo/graph-tutorial/resources/views/welcome.blade.php" id="WelcomeSnippet":::

1. Aktualisieren Sie die `Controller` Basisklasse in **./app/Http/Controllers/Controller.php,** indem Sie der Klasse die folgende Funktion hinzufügen.

    :::code language="php" source="../demo/graph-tutorial/app/Http/Controllers/Controller.php" id="LoadViewDataSnippet":::

1. Erstellen Sie eine neue Datei im `./app/Http/Controllers` Verzeichnis mit dem `HomeController.php` Namen, und fügen Sie den folgenden Code hinzu.

    :::code language="php" source="../demo/graph-tutorial/app/Http/Controllers/HomeController.php":::

1. Aktualisieren Sie die Route, `./routes/web.php` um den neuen Controller zu verwenden. Ersetzen Sie den gesamten Inhalt dieser Datei durch Folgendes.

    ```php
    <?php

    use Illuminate\Support\Facades\Route;

    Route::get('/', 'HomeController@welcome');
    ```

1. Öffnen **Sie ./app/Providers/RouteServiceProvider.php,** und entkomment die `$namespace` Deklaration.

    ```php
    /**
     * This namespace is applied to your controller routes.
     *
     * In addition, it is set as the URL generator's root namespace.
     *
     * @var string
     */
    protected $namespace = 'App\Http\Controllers';
    ```

1. Speichern Sie alle Änderungen, und starten Sie den Server neu. Jetzt sollte die App ganz anders aussehen.

    ![Screenshot der neu gestalteten Homepage](./images/create-app-01.png)
