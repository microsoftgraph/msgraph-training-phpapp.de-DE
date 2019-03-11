<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="32969-101">Öffnen Sie die Befehlszeilenschnittstelle (CLI), navigieren Sie zu einem Verzeichnis, in dem Sie die Berechtigung zum Erstellen von Dateien haben, und führen Sie den folgenden Befehl aus, um eine neue PHP-APP zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="32969-101">Open your command-line interface (CLI), navigate to a directory where you have rights to create files, and run the following command to create a new PHP app.</span></span>

```Shell
laravel new graph-tutorial
```

<span data-ttu-id="32969-102">Laravel erstellt ein neues Verzeichnis mit `graph-tutorial` dem Namen und Gerüst eine php-app.</span><span class="sxs-lookup"><span data-stu-id="32969-102">Laravel creates a new directory called `graph-tutorial` and scaffolds a PHP app.</span></span> <span data-ttu-id="32969-103">Navigieren Sie zu diesem neuen Verzeichnis, und geben Sie den folgenden Befehl ein, um einen lokalen Webserver zu starten.</span><span class="sxs-lookup"><span data-stu-id="32969-103">Navigate to this new directory and enter the following command to start a local web server.</span></span>

```Shell
php artisan serve
```

<span data-ttu-id="32969-104">Öffnen Sie Ihren Browser und navigieren Sie zu `http://localhost:8000`.</span><span class="sxs-lookup"><span data-stu-id="32969-104">Open your browser and navigate to `http://localhost:8000`.</span></span> <span data-ttu-id="32969-105">Wenn alles funktioniert, wird eine Laravel-Standardseite angezeigt.</span><span class="sxs-lookup"><span data-stu-id="32969-105">If everything is working, you will see a default Laravel page.</span></span> <span data-ttu-id="32969-106">Wenn diese Seite nicht angezeigt wird, überprüfen Sie die [Laravel-Dokumente](https://laravel.com/docs/5.6).</span><span class="sxs-lookup"><span data-stu-id="32969-106">If you don't see that page, check the [Laravel docs](https://laravel.com/docs/5.6).</span></span>

<span data-ttu-id="32969-107">Bevor Sie fortfahren, installieren Sie einige zusätzliche Bibliotheken, die Sie später verwenden werden:</span><span class="sxs-lookup"><span data-stu-id="32969-107">Before moving on, install some additional libraries that you will use later:</span></span>

- <span data-ttu-id="32969-108">[oauth2-Client](https://github.com/thephpleague/oauth2-client) für die Verarbeitung von Anmelde-und OAuth-Token Flows.</span><span class="sxs-lookup"><span data-stu-id="32969-108">[oauth2-client](https://github.com/thephpleague/oauth2-client) for handling sign-in and OAuth token flows.</span></span>
- <span data-ttu-id="32969-109">[Microsoft-Graph](https://github.com/microsoftgraph/msgraph-sdk-php) für Aufrufe von Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="32969-109">[microsoft-graph](https://github.com/microsoftgraph/msgraph-sdk-php) for making calls to Microsoft Graph.</span></span>

<span data-ttu-id="32969-110">Führen Sie den folgenden Befehl in der CLI aus.</span><span class="sxs-lookup"><span data-stu-id="32969-110">Run the following command in your CLI.</span></span>

```Shell
composer require league/oauth2-client:dev-master microsoft/microsoft-graph
```

## <a name="design-the-app"></a><span data-ttu-id="32969-111">Entwerfen der APP</span><span class="sxs-lookup"><span data-stu-id="32969-111">Design the app</span></span>

<span data-ttu-id="32969-112">Erstellen Sie zunächst das globale Layout für die app.</span><span class="sxs-lookup"><span data-stu-id="32969-112">Start by creating the global layout for the app.</span></span> <span data-ttu-id="32969-113">Erstellen Sie eine neue Datei im `./resources/views` Verzeichnis mit `layout.blade.php` dem Namen, und fügen Sie den folgenden Code hinzu.</span><span class="sxs-lookup"><span data-stu-id="32969-113">Create a new file in the  `./resources/views` directory named `layout.blade.php` and add the following code.</span></span>

```php
<!DOCTYPE html>
<html>
  <head>
    <title>PHP Graph Tutorial</title>

    <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.1.1/css/bootstrap.min.css"
        integrity="sha384-WskhaSGFgHYWDcbwN70/dfYBj47jz9qbsMId/iRN3ewGhXQFZCSftd1LZCfmhktB" crossorigin="anonymous">
    <link rel="stylesheet" href="https://use.fontawesome.com/releases/v5.1.0/css/all.css"
        integrity="sha384-lKuwvrZot6UHsBSfcMvOkWwlCMgc0TaWr+30HWe3a4ltaBwTZhyTEggF5tJv8tbt" crossorigin="anonymous">
    <link rel="stylesheet" href="{{ asset('/css/app.css') }}">
    <script src="https://code.jquery.com/jquery-3.3.1.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.14.3/umd/popper.min.js"
        integrity="sha384-ZMP7rVo3mIykV+2+9J3UJ46jBk0WLaUAdn689aCwoqbBJiSnjAK/l8WvCWPIPm49" crossorigin="anonymous"></script>
    <script src="https://stackpath.bootstrapcdn.com/bootstrap/4.1.1/js/bootstrap.min.js"
        integrity="sha384-smHYKdLADwkXOn1EmN1qk/HfnUcbVRZyYmZ4qpPea6sjB/pTJ0euyQp0Mk8ck+5T" crossorigin="anonymous"></script>
  </head>

  <body>
    <nav class="navbar navbar-expand-md navbar-dark fixed-top bg-dark">
      <div class="container">
        <a href="/" class="navbar-brand">PHP Graph Tutorial</a>
        <button class="navbar-toggler" type="button" data-toggle="collapse" data-target="#navbarCollapse"
            aria-controls="navbarCollapse" aria-expanded="false" aria-label="Toggle navigation">
          <span class="navbar-toggler-icon"></span>
        </button>
        <div class="collapse navbar-collapse" id="navbarCollapse">
          <ul class="navbar-nav mr-auto">
            <li class="nav-item">
              <a href="/" class="nav-link {{$_SERVER['REQUEST_URI'] == '/' ? ' active' : ''}}">Home</a>
            </li>
            @if(isset($userName))
              <li class="nav-item" data-turbolinks="false">
                <a href="/calendar" class="nav-link{{$_SERVER['REQUEST_URI'] == '/calendar' ? ' active' : ''}}">Calendar</a>
              </li>
            @endif
          </ul>
          <ul class="navbar-nav justify-content-end">
            <li class="nav-item">
              <a class="nav-link" href="https://developer.microsoft.com/graph/docs/concepts/overview" target="_blank">
                <i class="fas fa-external-link-alt mr-1"></i>Docs
              </a>
            </li>
            @if(isset($userName))
              <li class="nav-item dropdown">
                <a class="nav-link dropdown-toggle" data-toggle="dropdown" href="#" role="button"
                  aria-haspopup="true" aria-expanded="false">
                  @if(isset($user_avatar))
                    <img src="{{ $user_avatar }}" class="rounded-circle align-self-center mr-2" style="width: 32px;">
                  @else
                    <i class="far fa-user-circle fa-lg rounded-circle align-self-center mr-2" style="width: 32px;"></i>
                  @endif
                </a>
                <div class="dropdown-menu dropdown-menu-right">
                  <h5 class="dropdown-item-text mb-0">{{ $userName }}</h5>
                  <p class="dropdown-item-text text-muted mb-0">{{ $userEmail }}</p>
                  <div class="dropdown-divider"></div>
                  <a href="/signout" class="dropdown-item">Sign Out</a>
                </div>
              </li>
            @else
              <li class="nav-item">
                <a href="/signin" class="nav-link">Sign In</a>
              </li>
            @endif
          </ul>
        </div>
      </div>
    </nav>
    <main role="main" class="container">
      @if(session('error'))
        <div class="alert alert-danger" role="alert">
          <p class="mb-3">{{ session('error') }}</p>
          @if(session('errorDetail'))
            <pre class="alert-pre border bg-light p-2"><code>{{ session('errorDetail') }}</code></pre>
          @endif
        </div>
      @endif

      @yield('content')
    </main>
  </body>
</html>
```

<span data-ttu-id="32969-114">Dieser Code fügt [Bootstrap](http://getbootstrap.com/) für einfache Styling und [Font awesome](https://fontawesome.com/) für einige einfache Symbole.</span><span class="sxs-lookup"><span data-stu-id="32969-114">This code adds [Bootstrap](http://getbootstrap.com/) for simple styling, and [Font Awesome](https://fontawesome.com/) for some simple icons.</span></span> <span data-ttu-id="32969-115">Außerdem wird ein globales Layout mit einer NAV-Leiste definiert.</span><span class="sxs-lookup"><span data-stu-id="32969-115">It also defines a global layout with a nav bar.</span></span>

<span data-ttu-id="32969-116">Öffnen `./public/css/app.css` Sie jetzt, und ersetzen Sie den gesamten Inhalt durch Folgendes.</span><span class="sxs-lookup"><span data-stu-id="32969-116">Now open `./public/css/app.css` and replace its entire contents with the following.</span></span>

```css
body {
  padding-top: 4.5rem;
}

.alert-pre {
  word-wrap: break-word;
  word-break: break-all;
  white-space: pre-wrap;
}
```

<span data-ttu-id="32969-117">Aktualisieren Sie nun die Standardseite.</span><span class="sxs-lookup"><span data-stu-id="32969-117">Now update the default page.</span></span> <span data-ttu-id="32969-118">Öffnen Sie `./resources/views/welcome.blade.php` die Datei, und ersetzen Sie Ihren Inhalt durch Folgendes.</span><span class="sxs-lookup"><span data-stu-id="32969-118">Open the `./resources/views/welcome.blade.php` file and replace its contents with the following.</span></span>

```php
@extends('layout')

@section('content')
<div class="jumbotron">
  <h1>PHP Graph Tutorial</h1>
  <p class="lead">This sample app shows how to use the Microsoft Graph API to access Outlook and OneDrive data from PHP</p>
  @if(isset($userName))
    <h4>Welcome {{ $userName }}!</h4>
    <p>Use the navigation bar at the top of the page to get started.</p>
  @else
    <a href="/signin" class="btn btn-primary btn-large">Click here to sign in</a>
  @endif
</div>
@endsection
```

<span data-ttu-id="32969-119">Aktualisieren Sie die `Controller` Basisklasse `./app/Http/Controllers/Controller.php` in, indem Sie der Klasse die folgende Funktion hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="32969-119">Update the base `Controller` class in `./app/Http/Controllers/Controller.php` by adding the following function to the class.</span></span>

```php
public function loadViewData()
{
  $viewData = [];

  // Check for flash errors
  if (session('error')) {
    $viewData['error'] = session('error');
    $viewData['errorDetail'] = session('errorDetail');
  }

  // Check for logged on user
  if (session('userName'))
  {
    $viewData['userName'] = session('userName');
    $viewData['userEmail'] = session('userEmail');
  }

  return $viewData;
}
```

<span data-ttu-id="32969-120">Fügen Sie als nächstes einen Controller für die Homepage hinzu.</span><span class="sxs-lookup"><span data-stu-id="32969-120">Next, add a controller for the home page.</span></span> <span data-ttu-id="32969-121">Erstellen Sie eine neue Datei im `./app/Http/Controllers` Verzeichnis mit `HomeController.php` dem Namen, und fügen Sie den folgenden Code hinzu.</span><span class="sxs-lookup"><span data-stu-id="32969-121">Create a new file in the `./app/Http/Controllers` directory named `HomeController.php` and add the following code.</span></span>

```php
<?php

namespace App\Http\Controllers;

use App\Http\Controllers\Controller;
use Illuminate\Http\Request;

class HomeController extends Controller
{
  public function welcome()
  {
    $viewData = $this->loadViewData();

    return view('welcome', $viewData);
  }
}
```

<span data-ttu-id="32969-122">Aktualisieren Sie schließlich die Route in `./routes/web.php` , um den neuen Controller zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="32969-122">Finally, update the route in `./routes/web.php` to use the new controller.</span></span> <span data-ttu-id="32969-123">Ersetzen Sie den gesamten Inhalt dieser Datei durch Folgendes.</span><span class="sxs-lookup"><span data-stu-id="32969-123">Replace the entire contents of this file with the following.</span></span>

```php
<?php

Route::get('/', 'HomeController@welcome');
```

<span data-ttu-id="32969-124">Speichern Sie alle Änderungen, und starten Sie den Server neu.</span><span class="sxs-lookup"><span data-stu-id="32969-124">Save all of your changes and restart the server.</span></span> <span data-ttu-id="32969-125">Nun sollte die APP sehr unterschiedlich aussehen.</span><span class="sxs-lookup"><span data-stu-id="32969-125">Now, the app should look very different.</span></span>

![Screenshot der neu gestalteten Homepage](./images/create-app-01.png)