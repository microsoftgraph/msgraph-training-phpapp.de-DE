<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="030c1-101">In dieser Übung integrieren Sie Microsoft Graph in die Anwendung.</span><span class="sxs-lookup"><span data-stu-id="030c1-101">In this exercise you will incorporate the Microsoft Graph into the application.</span></span> <span data-ttu-id="030c1-102">Für diese Anwendung verwenden Sie die [Microsoft-Graph-](https://github.com/microsoftgraph/msgraph-sdk-php) Bibliothek, um Aufrufe von Microsoft Graph zu tätigen.</span><span class="sxs-lookup"><span data-stu-id="030c1-102">For this application, you will use the [microsoft-graph](https://github.com/microsoftgraph/msgraph-sdk-php) library to make calls to Microsoft Graph.</span></span>

## <a name="get-calendar-events-from-outlook"></a><span data-ttu-id="030c1-103">Abrufen von Kalenderereignissen aus Outlook</span><span class="sxs-lookup"><span data-stu-id="030c1-103">Get calendar events from Outlook</span></span>

<span data-ttu-id="030c1-104">Beginnen wir mit dem Hinzufügen eines Controllers für die Kalenderansicht.</span><span class="sxs-lookup"><span data-stu-id="030c1-104">Let's start by adding a controller for the calendar view.</span></span> <span data-ttu-id="030c1-105">Erstellen Sie eine neue Datei im `./app/Http/Controllers` Ordner mit `CalendarController.php`dem Namen, und fügen Sie den folgenden Code hinzu.</span><span class="sxs-lookup"><span data-stu-id="030c1-105">Create a new file in the `./app/Http/Controllers` folder named `CalendarController.php`, and add the following code.</span></span>

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

<span data-ttu-id="030c1-106">Überlegen Sie sich, was dieser Code tut.</span><span class="sxs-lookup"><span data-stu-id="030c1-106">Consider what this code is doing.</span></span>

- <span data-ttu-id="030c1-107">Die URL, die aufgerufen wird, `/v1.0/me/events`lautet.</span><span class="sxs-lookup"><span data-stu-id="030c1-107">The URL that will be called is `/v1.0/me/events`.</span></span>
- <span data-ttu-id="030c1-108">Der `$select` Parameter schränkt die für jedes Ereignis zurückgegebenen Felder auf diejenigen ein, die die Ansicht tatsächlich verwendet.</span><span class="sxs-lookup"><span data-stu-id="030c1-108">The `$select` parameter limits the fields returned for each events to just those the view will actually use.</span></span>
- <span data-ttu-id="030c1-109">Der `$orderby` Parameter sortiert die Ergebnisse nach dem Datum und der Uhrzeit, zu denen Sie erstellt wurden, wobei das neueste Element zuerst angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="030c1-109">The `$orderby` parameter sorts the results by the date and time they were created, with the most recent item being first.</span></span>

<span data-ttu-id="030c1-110">Aktualisieren der Routen in `./routes/web.php` So fügen Sie eine Route zu diesem neuen Controller hinzu</span><span class="sxs-lookup"><span data-stu-id="030c1-110">Update the routes in `./routes/web.php` to add a route to this new controller</span></span>

```php
Route::get('/calendar', 'CalendarController@calendar');
```

<span data-ttu-id="030c1-111">Jetzt können Sie dies testen.</span><span class="sxs-lookup"><span data-stu-id="030c1-111">Now you can test this.</span></span> <span data-ttu-id="030c1-112">Melden Sie sich an, und klicken Sie in der Navigationsleiste auf den Link **Kalender** .</span><span class="sxs-lookup"><span data-stu-id="030c1-112">Sign in and click the **Calendar** link in the nav bar.</span></span> <span data-ttu-id="030c1-113">Wenn alles funktioniert, sollte ein JSON-Dump von Ereignissen im Kalender des Benutzers angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="030c1-113">If everything works, you should see a JSON dump of events on the user's calendar.</span></span>

## <a name="display-the-results"></a><span data-ttu-id="030c1-114">Anzeigen der Ergebnisse</span><span class="sxs-lookup"><span data-stu-id="030c1-114">Display the results</span></span>

<span data-ttu-id="030c1-115">Jetzt können Sie eine Ansicht hinzufügen, um die Ergebnisse benutzerfreundlicher anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="030c1-115">Now you can add a view to display the results in a more user-friendly manner.</span></span> <span data-ttu-id="030c1-116">Erstellen Sie eine neue Datei im `./resources/views` Verzeichnis mit `calendar.blade.php` dem Namen, und fügen Sie den folgenden Code hinzu.</span><span class="sxs-lookup"><span data-stu-id="030c1-116">Create a new file in the `./resources/views` directory named `calendar.blade.php` and add the following code.</span></span>

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

<span data-ttu-id="030c1-117">, Der eine Auflistung von Ereignissen durchläuft und jeweils eine Tabellenzeile hinzufügt.</span><span class="sxs-lookup"><span data-stu-id="030c1-117">That will loop through a collection of events and add a table row for each one.</span></span> <span data-ttu-id="030c1-118">Entfernen Sie `return response()->json($events);` die Leitung aus `calendar` der Aktion `./app/Http/Controllers/CalendarController.php`in, und ersetzen Sie Sie durch den folgenden Code.</span><span class="sxs-lookup"><span data-stu-id="030c1-118">Remove the `return response()->json($events);` line from the `calendar` action in `./app/Http/Controllers/CalendarController.php`, and replace it with the following code.</span></span>

```php
$viewData['events'] = $events;
return view('calendar', $viewData);
```

<span data-ttu-id="030c1-119">Aktualisieren Sie die Seite, und die APP sollte jetzt eine Tabelle mit Ereignissen rendern.</span><span class="sxs-lookup"><span data-stu-id="030c1-119">Refresh the page and the app should now render a table of events.</span></span>

![Screenshot der Ereignistabelle](./images/add-msgraph-01.png)