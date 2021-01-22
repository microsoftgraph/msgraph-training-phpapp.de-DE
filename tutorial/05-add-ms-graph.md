<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="e0519-101">In dieser Übung integrieren Sie Microsoft Graph in die Anwendung.</span><span class="sxs-lookup"><span data-stu-id="e0519-101">In this exercise you will incorporate the Microsoft Graph into the application.</span></span> <span data-ttu-id="e0519-102">Für diese Anwendung verwenden Sie die [Microsoft-Graph-Bibliothek,](https://github.com/microsoftgraph/msgraph-sdk-php) um Aufrufe an Microsoft Graph zu senden.</span><span class="sxs-lookup"><span data-stu-id="e0519-102">For this application, you will use the [microsoft-graph](https://github.com/microsoftgraph/msgraph-sdk-php) library to make calls to Microsoft Graph.</span></span>

## <a name="get-calendar-events-from-outlook"></a><span data-ttu-id="e0519-103">Abrufen von Kalenderereignissen von Outlook</span><span class="sxs-lookup"><span data-stu-id="e0519-103">Get calendar events from Outlook</span></span>

1. <span data-ttu-id="e0519-104">Erstellen Sie ein neues Verzeichnis im **Verzeichnis ./app** mit dem Namen, erstellen Sie dann eine neue Datei in diesem Verzeichnis namens , und `TimeZones` fügen Sie den folgenden Code `TimeZones.php` hinzu.</span><span class="sxs-lookup"><span data-stu-id="e0519-104">Create a new directory in the **./app** directory named `TimeZones`, then create a new file in that directory named `TimeZones.php`, and add the following code.</span></span>

    :::code language="php" source="../demo/graph-tutorial/app/TimeZones/TimeZones.php":::

    <span data-ttu-id="e0519-105">Diese Klasse implementiert eine vereinfachte Zuordnung von Windows-Zeitzonennamen zu IANA-Zeitzonenbezeichnern.</span><span class="sxs-lookup"><span data-stu-id="e0519-105">This class implements a simplistic mapping of Windows time zone names to IANA time zone identifiers.</span></span>

1. <span data-ttu-id="e0519-106">Erstellen Sie eine neue Datei im **Verzeichnis ./app/Http/Controllers** mit dem Namen `CalendarController.php` , und fügen Sie den folgenden Code hinzu.</span><span class="sxs-lookup"><span data-stu-id="e0519-106">Create a new file in the **./app/Http/Controllers** directory named `CalendarController.php`, and add the following code.</span></span>

    ```php
    <?php

    namespace App\Http\Controllers;

    use App\Http\Controllers\Controller;
    use Illuminate\Http\Request;
    use Microsoft\Graph\Graph;
    use Microsoft\Graph\Model;
    use App\TokenStore\TokenCache;
    use App\TimeZones\TimeZones;

    class CalendarController extends Controller
    {
      public function calendar()
      {
        $viewData = $this->loadViewData();

        $graph = $this->getGraph();

        // Get user's timezone
        $timezone = TimeZones::getTzFromWindows($viewData['userTimeZone']);

        // Get start and end of week
        $startOfWeek = new \DateTimeImmutable('sunday -1 week', $timezone);
        $endOfWeek = new \DateTimeImmutable('sunday', $timezone);

        $queryParams = array(
          'startDateTime' => $startOfWeek->format(\DateTimeInterface::ISO8601),
          'endDateTime' => $endOfWeek->format(\DateTimeInterface::ISO8601),
          // Only request the properties used by the app
          '$select' => 'subject,organizer,start,end',
          // Sort them by start time
          '$orderby' => 'start/dateTime',
          // Limit results to 25
          '$top' => 25
        );

        // Append query parameters to the '/me/calendarView' url
        $getEventsUrl = '/me/calendarView?'.http_build_query($queryParams);

        $events = $graph->createRequest('GET', $getEventsUrl)
          // Add the user's timezone to the Prefer header
          ->addHeaders(array(
            'Prefer' => 'outlook.timezone="'.$viewData['userTimeZone'].'"'
          ))
          ->setReturnType(Model\Event::class)
          ->execute();

        return response()->json($events);
      }

      private function getGraph(): Graph
      {
        // Get the access token from the cache
        $tokenCache = new TokenCache();
        $accessToken = $tokenCache->getAccessToken();

        // Create a Graph client
        $graph = new Graph();
        $graph->setAccessToken($accessToken);
        return $graph;
      }
    }
    ```

    <span data-ttu-id="e0519-107">Überlegen Sie sich, was dieser Code macht.</span><span class="sxs-lookup"><span data-stu-id="e0519-107">Consider what this code is doing.</span></span>

    - <span data-ttu-id="e0519-108">Die URL, die aufgerufen wird, lautet `/v1.0/me/calendarView`.</span><span class="sxs-lookup"><span data-stu-id="e0519-108">The URL that will be called is `/v1.0/me/calendarView`.</span></span>
    - <span data-ttu-id="e0519-109">Die `startDateTime` Parameter und Parameter definieren den Anfang und das Ende der `endDateTime` Ansicht.</span><span class="sxs-lookup"><span data-stu-id="e0519-109">The `startDateTime` and `endDateTime` parameters define the start and end of the view.</span></span>
    - <span data-ttu-id="e0519-110">Der Parameter beschränkt die für jedes Ereignis zurückgegebenen Felder auf die Felder, die `$select` tatsächlich von der Ansicht verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="e0519-110">The `$select` parameter limits the fields returned for each events to just those the view will actually use.</span></span>
    - <span data-ttu-id="e0519-111">Der Parameter sortiert die Ergebnisse nach dem Datum und der Uhrzeit, zu der sie erstellt wurden, dabei ist das letzte `$orderby` Element das erste Element.</span><span class="sxs-lookup"><span data-stu-id="e0519-111">The `$orderby` parameter sorts the results by the date and time they were created, with the most recent item being first.</span></span>
    - <span data-ttu-id="e0519-112">Der `$top` Parameter beschränkt die Ergebnisse auf 25 Ereignisse.</span><span class="sxs-lookup"><span data-stu-id="e0519-112">The `$top` parameter limits the results to 25 events.</span></span>
    - <span data-ttu-id="e0519-113">Die Kopfzeile bewirkt, dass die Start- und Endzeiten in der Antwort an die bevorzugte Zeitzone des Benutzers `Prefer: outlook.timezone=""` angepasst werden.</span><span class="sxs-lookup"><span data-stu-id="e0519-113">The `Prefer: outlook.timezone=""` header causes the start and end times in the response to be adjusted to the user's preferred time zone.</span></span>

1. <span data-ttu-id="e0519-114">Aktualisieren Sie die Routen in **./routes/web.php,** um diesem neuen Controller eine Route hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="e0519-114">Update the routes in **./routes/web.php** to add a route to this new controller.</span></span>

    ```php
    Route::get('/calendar', 'CalendarController@calendar');
    ```

1. <span data-ttu-id="e0519-115">Melden Sie sich an, und klicken **Sie** in der Navigationsleiste auf den Kalenderlink.</span><span class="sxs-lookup"><span data-stu-id="e0519-115">Sign in and click the **Calendar** link in the nav bar.</span></span> <span data-ttu-id="e0519-116">Wenn alles funktioniert, sollte ein JSON-Abbild von Ereignissen im Kalender des Benutzers angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="e0519-116">If everything works, you should see a JSON dump of events on the user's calendar.</span></span>

## <a name="display-the-results"></a><span data-ttu-id="e0519-117">Anzeigen der Ergebnisse</span><span class="sxs-lookup"><span data-stu-id="e0519-117">Display the results</span></span>

<span data-ttu-id="e0519-118">Jetzt können Sie eine Ansicht hinzufügen, um die Ergebnisse benutzerfreundlicher anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="e0519-118">Now you can add a view to display the results in a more user-friendly manner.</span></span>

1. <span data-ttu-id="e0519-119">Erstellen Sie eine neue Datei im Verzeichnis **./resources/views** mit dem `calendar.blade.php` Namen, und fügen Sie den folgenden Code hinzu.</span><span class="sxs-lookup"><span data-stu-id="e0519-119">Create a new file in the **./resources/views** directory named `calendar.blade.php` and add the following code.</span></span>

    :::code language="php" source="../demo/graph-tutorial/resources/views/calendar.blade.php" id="CalendarSnippet":::

    <span data-ttu-id="e0519-120">Dadurch wird eine Ereignissammlung durchlaufen und jedem Ereignis wird jeweils eine Tabellenzeile hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="e0519-120">That will loop through a collection of events and add a table row for each one.</span></span>

1. <span data-ttu-id="e0519-121">Aktualisieren Sie die Routen in **./routes/web.php,** um Routen für `/calendar/new` hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="e0519-121">Update the routes in **./routes/web.php** to add routes for `/calendar/new`.</span></span> <span data-ttu-id="e0519-122">Sie werden diese Funktionen im nächsten Abschnitt implementieren, aber die Route muss jetzt definiert werden, da **calendar.blade.php** darauf verweist.</span><span class="sxs-lookup"><span data-stu-id="e0519-122">You will implement these functions in the next section, but the route need to be defined now because **calendar.blade.php** references it.</span></span>

    ```php
    Route::get('/calendar/new', 'CalendarController@getNewEventForm');
    Route::post('/calendar/new', 'CalendarController@createNewEvent');
    ```

1. <span data-ttu-id="e0519-123">Entfernen Sie `return response()->json($events);` die Zeile aus der Aktion in `calendar` **./app/Http/Controllers/CalendarController.php,** und ersetzen Sie sie durch den folgenden Code.</span><span class="sxs-lookup"><span data-stu-id="e0519-123">Remove the `return response()->json($events);` line from the `calendar` action in **./app/Http/Controllers/CalendarController.php**, and replace it with the following code.</span></span>

    ```php
    $viewData['events'] = $events;
    return view('calendar', $viewData);
    ```

1. <span data-ttu-id="e0519-124">Aktualisieren Sie die Seite, und die App sollte nun eine Tabelle mit Ereignissen rendern.</span><span class="sxs-lookup"><span data-stu-id="e0519-124">Refresh the page and the app should now render a table of events.</span></span>

    ![Ein Screenshot der Tabelle mit Ereignissen](./images/add-msgraph-01.png)
