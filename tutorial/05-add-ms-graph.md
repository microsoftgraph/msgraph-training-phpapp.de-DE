<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="39341-101">In dieser Übung werden Sie das Microsoft Graph in die Anwendung integrieren.</span><span class="sxs-lookup"><span data-stu-id="39341-101">In this exercise you will incorporate the Microsoft Graph into the application.</span></span> <span data-ttu-id="39341-102">Für diese Anwendung verwenden Sie die [Microsoft-Graph-](https://github.com/microsoftgraph/msgraph-sdk-php) Bibliothek, um Anrufe an Microsoft Graph zu tätigen.</span><span class="sxs-lookup"><span data-stu-id="39341-102">For this application, you will use the [microsoft-graph](https://github.com/microsoftgraph/msgraph-sdk-php) library to make calls to Microsoft Graph.</span></span>

## <a name="get-calendar-events-from-outlook"></a><span data-ttu-id="39341-103">Abrufen von Kalenderereignissen von Outlook</span><span class="sxs-lookup"><span data-stu-id="39341-103">Get calendar events from Outlook</span></span>

1. <span data-ttu-id="39341-104">Erstellen Sie ein neues Verzeichnis im **./app** -Verzeichnis mit dem Namen `TimeZones` , erstellen Sie dann eine neue Datei in dem Verzeichnis mit dem Namen `TimeZones.php` , und fügen Sie den folgenden Code hinzu.</span><span class="sxs-lookup"><span data-stu-id="39341-104">Create a new directory in the **./app** directory named `TimeZones`, then create a new file in that directory named `TimeZones.php`, and add the following code.</span></span>

    :::code language="php" source="../demo/graph-tutorial/app/TimeZones/TimeZones.php":::

    <span data-ttu-id="39341-105">Diese Klasse implementiert eine vereinfachte Zuordnung von Windows-Zeitzonennamen zu IANA-Zeitzonenbezeichnern.</span><span class="sxs-lookup"><span data-stu-id="39341-105">This class implements a simplistic mapping of Windows time zone names to IANA time zone identifiers.</span></span>

1. <span data-ttu-id="39341-106">Erstellen Sie eine neue Datei im **/App/http/Controllers** -Verzeichnis mit dem Namen `CalendarController.php` , und fügen Sie den folgenden Code hinzu.</span><span class="sxs-lookup"><span data-stu-id="39341-106">Create a new file in the **./app/Http/Controllers** directory named `CalendarController.php`, and add the following code.</span></span>

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

    <span data-ttu-id="39341-107">Überlegen Sie sich, was dieser Code macht.</span><span class="sxs-lookup"><span data-stu-id="39341-107">Consider what this code is doing.</span></span>

    - <span data-ttu-id="39341-108">Die URL, die aufgerufen wird, lautet `/v1.0/me/calendarView`.</span><span class="sxs-lookup"><span data-stu-id="39341-108">The URL that will be called is `/v1.0/me/calendarView`.</span></span>
    - <span data-ttu-id="39341-109">Die `startDateTime` `endDateTime` Parameter und definieren den Anfang und das Ende der Ansicht.</span><span class="sxs-lookup"><span data-stu-id="39341-109">The `startDateTime` and `endDateTime` parameters define the start and end of the view.</span></span>
    - <span data-ttu-id="39341-110">Der `$select` Parameter schränkt die für die einzelnen Ereignisse zurückgegebenen Felder auf diejenigen ein, die von der Ansicht tatsächlich verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="39341-110">The `$select` parameter limits the fields returned for each events to just those the view will actually use.</span></span>
    - <span data-ttu-id="39341-111">Der `$orderby` Parameter sortiert die Ergebnisse nach dem Datum und der Uhrzeit, zu der Sie erstellt wurden, wobei das letzte Element zuerst angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="39341-111">The `$orderby` parameter sorts the results by the date and time they were created, with the most recent item being first.</span></span>
    - <span data-ttu-id="39341-112">Der `$top` Parameter schränkt die Ergebnisse auf 25 Ereignisse ein.</span><span class="sxs-lookup"><span data-stu-id="39341-112">The `$top` parameter limits the results to 25 events.</span></span>
    - <span data-ttu-id="39341-113">Der `Prefer: outlook.timezone=""` Header bewirkt, dass die Anfangs-und Endzeiten in der Antwort an die bevorzugte Zeitzone des Benutzers angepasst werden.</span><span class="sxs-lookup"><span data-stu-id="39341-113">The `Prefer: outlook.timezone=""` header causes the start and end times in the response to be adjusted to the user's preferred time zone.</span></span>

1. <span data-ttu-id="39341-114">Aktualisieren Sie die Routen in **./routes/Web.php** , um dieser neuen Steuerung eine Route hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="39341-114">Update the routes in **./routes/web.php** to add a route to this new controller.</span></span>

    ```php
    Route::get('/calendar', 'CalendarController@calendar');
    ```

1. <span data-ttu-id="39341-115">Melden Sie sich an, und klicken Sie in der Navigationsleiste auf den Link **Kalender** .</span><span class="sxs-lookup"><span data-stu-id="39341-115">Sign in and click the **Calendar** link in the nav bar.</span></span> <span data-ttu-id="39341-116">Wenn alles funktioniert, sollte ein JSON-Abbild von Ereignissen im Kalender des Benutzers angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="39341-116">If everything works, you should see a JSON dump of events on the user's calendar.</span></span>

## <a name="display-the-results"></a><span data-ttu-id="39341-117">Anzeigen der Ergebnisse</span><span class="sxs-lookup"><span data-stu-id="39341-117">Display the results</span></span>

<span data-ttu-id="39341-118">Jetzt können Sie eine Ansicht hinzufügen, um die Ergebnisse benutzerfreundlicher anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="39341-118">Now you can add a view to display the results in a more user-friendly manner.</span></span>

1. <span data-ttu-id="39341-119">Erstellen Sie eine neue Datei im **./Resources/views** -Verzeichnis mit dem Namen `calendar.blade.php` , und fügen Sie den folgenden Code hinzu.</span><span class="sxs-lookup"><span data-stu-id="39341-119">Create a new file in the **./resources/views** directory named `calendar.blade.php` and add the following code.</span></span>

    :::code language="php" source="../demo/graph-tutorial/resources/views/calendar.blade.php" id="CalendarSnippet":::

    <span data-ttu-id="39341-120">Dadurch wird eine Ereignissammlung durchlaufen und jedem Ereignis wird jeweils eine Tabellenzeile hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="39341-120">That will loop through a collection of events and add a table row for each one.</span></span>

1. <span data-ttu-id="39341-121">Entfernen Sie die- `return response()->json($events);` `calendar` Aktion in **./app/http/Controllers/CalendarController.php**, und ersetzen Sie Sie durch den folgenden Code.</span><span class="sxs-lookup"><span data-stu-id="39341-121">Remove the `return response()->json($events);` line from the `calendar` action in **./app/Http/Controllers/CalendarController.php**, and replace it with the following code.</span></span>

    ```php
    $viewData['events'] = $events;
    return view('calendar', $viewData);
    ```

1. <span data-ttu-id="39341-122">Aktualisieren Sie die Seite, und die APP sollte jetzt eine Tabelle mit Ereignissen rendern.</span><span class="sxs-lookup"><span data-stu-id="39341-122">Refresh the page and the app should now render a table of events.</span></span>

    ![Ein Screenshot der Tabelle mit Ereignissen](./images/add-msgraph-01.png)
