<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="d2c5b-101">In dieser Übung werden Sie das Microsoft Graph in die Anwendung integrieren.</span><span class="sxs-lookup"><span data-stu-id="d2c5b-101">In this exercise you will incorporate the Microsoft Graph into the application.</span></span> <span data-ttu-id="d2c5b-102">Für diese Anwendung verwenden Sie die [Microsoft-Graph-](https://github.com/microsoftgraph/msgraph-sdk-php) Bibliothek, um Anrufe an Microsoft Graph zu tätigen.</span><span class="sxs-lookup"><span data-stu-id="d2c5b-102">For this application, you will use the [microsoft-graph](https://github.com/microsoftgraph/msgraph-sdk-php) library to make calls to Microsoft Graph.</span></span>

## <a name="get-calendar-events-from-outlook"></a><span data-ttu-id="d2c5b-103">Abrufen von Kalenderereignissen aus Outlook</span><span class="sxs-lookup"><span data-stu-id="d2c5b-103">Get calendar events from Outlook</span></span>

<span data-ttu-id="d2c5b-104">Beginnen wir mit dem Hinzufügen eines Controllers für die Kalenderansicht.</span><span class="sxs-lookup"><span data-stu-id="d2c5b-104">Let's start by adding a controller for the calendar view.</span></span> <span data-ttu-id="d2c5b-105">Erstellen Sie eine neue Datei im `./app/Http/Controllers` Ordner mit `CalendarController.php`dem Namen, und fügen Sie den folgenden Code hinzu.</span><span class="sxs-lookup"><span data-stu-id="d2c5b-105">Create a new file in the `./app/Http/Controllers` folder named `CalendarController.php`, and add the following code.</span></span>

```php
<?php

namespace App\Http\Controllers;

use App\Http\Controllers\Controller;
use Illuminate\Http\Request;
use Microsoft\Graph\Graph;
use Microsoft\Graph\Model;
use App\TokenStore\TokenCache;

class CalendarController extends Controller
{
  public function calendar()
  {
    $viewData = $this->loadViewData();

    // Get the access token from the cache
    $tokenCache = new TokenCache();
    $accessToken = $tokenCache->getAccessToken();

    // Create a Graph client
    $graph = new Graph();
    $graph->setAccessToken($accessToken);

    $queryParams = array(
      '$select' => 'subject,organizer,start,end',
      '$orderby' => 'createdDateTime DESC'
    );

    // Append query parameters to the '/me/events' url
    $getEventsUrl = '/me/events?'.http_build_query($queryParams);

    $events = $graph->createRequest('GET', $getEventsUrl)
      ->setReturnType(Model\Event::class)
      ->execute();

    return response()->json($events);
  }
}
```

<span data-ttu-id="d2c5b-106">Überprüfen Sie, was dieser Code tut.</span><span class="sxs-lookup"><span data-stu-id="d2c5b-106">Consider what this code is doing.</span></span>

- <span data-ttu-id="d2c5b-107">Die URL, die aufgerufen wird `/v1.0/me/events`.</span><span class="sxs-lookup"><span data-stu-id="d2c5b-107">The URL that will be called is `/v1.0/me/events`.</span></span>
- <span data-ttu-id="d2c5b-108">Der `$select` Parameter schränkt die für die einzelnen Ereignisse zurückgegebenen Felder auf diejenigen ein, die von der Ansicht tatsächlich verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="d2c5b-108">The `$select` parameter limits the fields returned for each events to just those the view will actually use.</span></span>
- <span data-ttu-id="d2c5b-109">Der `$orderby` Parameter sortiert die Ergebnisse nach dem Datum und der Uhrzeit, zu der Sie erstellt wurden, wobei das letzte Element zuerst angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="d2c5b-109">The `$orderby` parameter sorts the results by the date and time they were created, with the most recent item being first.</span></span>

<span data-ttu-id="d2c5b-110">Aktualisieren der Routen in `./routes/web.php` So fügen Sie diesem neuen Controller eine Route hinzu</span><span class="sxs-lookup"><span data-stu-id="d2c5b-110">Update the routes in `./routes/web.php` to add a route to this new controller</span></span>

```php
Route::get('/calendar', 'CalendarController@calendar');
```

<span data-ttu-id="d2c5b-111">Nun können Sie dies testen.</span><span class="sxs-lookup"><span data-stu-id="d2c5b-111">Now you can test this.</span></span> <span data-ttu-id="d2c5b-112">Melden Sie sich an, und klicken Sie in der Navigationsleiste auf den Link **Kalender** .</span><span class="sxs-lookup"><span data-stu-id="d2c5b-112">Sign in and click the **Calendar** link in the nav bar.</span></span> <span data-ttu-id="d2c5b-113">Wenn alles funktioniert, sollte ein JSON-Abbild der Ereignisse im Kalender des Benutzers angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="d2c5b-113">If everything works, you should see a JSON dump of events on the user's calendar.</span></span>

## <a name="display-the-results"></a><span data-ttu-id="d2c5b-114">Anzeigen der Ergebnisse</span><span class="sxs-lookup"><span data-stu-id="d2c5b-114">Display the results</span></span>

<span data-ttu-id="d2c5b-115">Jetzt können Sie eine Ansicht hinzufügen, um die Ergebnisse auf eine benutzerfreundlichere Weise anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="d2c5b-115">Now you can add a view to display the results in a more user-friendly manner.</span></span> <span data-ttu-id="d2c5b-116">Erstellen Sie eine neue Datei im `./resources/views` Verzeichnis mit `calendar.blade.php` dem Namen, und fügen Sie den folgenden Code hinzu.</span><span class="sxs-lookup"><span data-stu-id="d2c5b-116">Create a new file in the `./resources/views` directory named `calendar.blade.php` and add the following code.</span></span>

```php
@extends('layout')

@section('content')
<h1>Calendar</h1>
<table class="table">
  <thead>
    <tr>
      <th scope="col">Organizer</th>
      <th scope="col">Subject</th>
      <th scope="col">Start</th>
      <th scope="col">End</th>
    </tr>
  </thead>
  <tbody>
    @isset($events)
      @foreach($events as $event)
        <tr>
          <td>{{ $event->getOrganizer()->getEmailAddress()->getName() }}</td>
          <td>{{ $event->getSubject() }}</td>
          <td>{{ \Carbon\Carbon::parse($event->getStart()->getDateTime())->format('n/j/y g:i A') }}</td>
          <td>{{ \Carbon\Carbon::parse($event->getEnd()->getDateTime())->format('n/j/y g:i A') }}</td>
        </tr>
      @endforeach
    @endif
  </tbody>
</table>
@endsection
```

<span data-ttu-id="d2c5b-117">Dadurch wird eine Auflistung von Ereignissen durchlaufen und für jeden eine Tabellenzeile hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="d2c5b-117">That will loop through a collection of events and add a table row for each one.</span></span> <span data-ttu-id="d2c5b-118">Entfernen Sie `return response()->json($events);` die- `calendar` Aktion in `./app/Http/Controllers/CalendarController.php`, und ersetzen Sie Sie durch den folgenden Code.</span><span class="sxs-lookup"><span data-stu-id="d2c5b-118">Remove the `return response()->json($events);` line from the `calendar` action in `./app/Http/Controllers/CalendarController.php`, and replace it with the following code.</span></span>

```php
$viewData['events'] = $events;
return view('calendar', $viewData);
```

<span data-ttu-id="d2c5b-119">Aktualisieren Sie die Seite, und die APP sollte jetzt eine Tabelle mit Ereignissen rendern.</span><span class="sxs-lookup"><span data-stu-id="d2c5b-119">Refresh the page and the app should now render a table of events.</span></span>

![Ein Screenshot der Ereignistabelle](./images/add-msgraph-01.png)
