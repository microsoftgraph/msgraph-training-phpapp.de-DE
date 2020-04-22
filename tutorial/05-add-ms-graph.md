<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="227b7-101">In dieser Übung werden Sie das Microsoft Graph in die Anwendung integrieren.</span><span class="sxs-lookup"><span data-stu-id="227b7-101">In this exercise you will incorporate the Microsoft Graph into the application.</span></span> <span data-ttu-id="227b7-102">Für diese Anwendung verwenden Sie die [Microsoft-Graph-](https://github.com/microsoftgraph/msgraph-sdk-php) Bibliothek, um Anrufe an Microsoft Graph zu tätigen.</span><span class="sxs-lookup"><span data-stu-id="227b7-102">For this application, you will use the [microsoft-graph](https://github.com/microsoftgraph/msgraph-sdk-php) library to make calls to Microsoft Graph.</span></span>

## <a name="get-calendar-events-from-outlook"></a><span data-ttu-id="227b7-103">Abrufen von Kalenderereignissen von Outlook</span><span class="sxs-lookup"><span data-stu-id="227b7-103">Get calendar events from Outlook</span></span>

1. <span data-ttu-id="227b7-104">Erstellen Sie eine neue Datei im **/App/http/Controllers** -Verzeichnis mit `CalendarController.php`dem Namen, und fügen Sie den folgenden Code hinzu.</span><span class="sxs-lookup"><span data-stu-id="227b7-104">Create a new file in the **./app/Http/Controllers** directory named `CalendarController.php`, and add the following code.</span></span>

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

    <span data-ttu-id="227b7-105">Überlegen Sie sich, was dieser Code macht.</span><span class="sxs-lookup"><span data-stu-id="227b7-105">Consider what this code is doing.</span></span>

    - <span data-ttu-id="227b7-106">Die URL, die aufgerufen wird, lautet `/v1.0/me/events`.</span><span class="sxs-lookup"><span data-stu-id="227b7-106">The URL that will be called is `/v1.0/me/events`.</span></span>
    - <span data-ttu-id="227b7-107">Der `$select` Parameter schränkt die für die einzelnen Ereignisse zurückgegebenen Felder auf diejenigen ein, die von der Ansicht tatsächlich verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="227b7-107">The `$select` parameter limits the fields returned for each events to just those the view will actually use.</span></span>
    - <span data-ttu-id="227b7-108">Der `$orderby` Parameter sortiert die Ergebnisse nach dem Datum und der Uhrzeit, zu der Sie erstellt wurden, wobei das letzte Element zuerst angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="227b7-108">The `$orderby` parameter sorts the results by the date and time they were created, with the most recent item being first.</span></span>

1. <span data-ttu-id="227b7-109">Aktualisieren Sie die Routen in **./routes/Web.php** , um dieser neuen Steuerung eine Route hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="227b7-109">Update the routes in **./routes/web.php** to add a route to this new controller.</span></span>

    ```php
    Route::get('/calendar', 'CalendarController@calendar');
    ```

1. <span data-ttu-id="227b7-110">Melden Sie sich an, und klicken Sie in der Navigationsleiste auf den Link **Kalender** .</span><span class="sxs-lookup"><span data-stu-id="227b7-110">Sign in and click the **Calendar** link in the nav bar.</span></span> <span data-ttu-id="227b7-111">Wenn alles funktioniert, sollte ein JSON-Abbild von Ereignissen im Kalender des Benutzers angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="227b7-111">If everything works, you should see a JSON dump of events on the user's calendar.</span></span>

## <a name="display-the-results"></a><span data-ttu-id="227b7-112">Anzeigen der Ergebnisse</span><span class="sxs-lookup"><span data-stu-id="227b7-112">Display the results</span></span>

<span data-ttu-id="227b7-113">Jetzt können Sie eine Ansicht hinzufügen, um die Ergebnisse benutzerfreundlicher anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="227b7-113">Now you can add a view to display the results in a more user-friendly manner.</span></span>

1. <span data-ttu-id="227b7-114">Erstellen Sie eine neue Datei im **./Resources/views** -Verzeichnis `calendar.blade.php` mit dem Namen, und fügen Sie den folgenden Code hinzu.</span><span class="sxs-lookup"><span data-stu-id="227b7-114">Create a new file in the **./resources/views** directory named `calendar.blade.php` and add the following code.</span></span>

    :::code language="php" source="../demo/graph-tutorial/resources/views/calendar.blade.php" id="CalendarSnippet":::

    <span data-ttu-id="227b7-115">Dadurch wird eine Ereignissammlung durchlaufen und jedem Ereignis wird jeweils eine Tabellenzeile hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="227b7-115">That will loop through a collection of events and add a table row for each one.</span></span>

1. <span data-ttu-id="227b7-116">Entfernen Sie `return response()->json($events);` die- `calendar` Aktion in **./app/http/Controllers/CalendarController.php**, und ersetzen Sie Sie durch den folgenden Code.</span><span class="sxs-lookup"><span data-stu-id="227b7-116">Remove the `return response()->json($events);` line from the `calendar` action in **./app/Http/Controllers/CalendarController.php**, and replace it with the following code.</span></span>

    ```php
    $viewData['events'] = $events;
    return view('calendar', $viewData);
    ```

1. <span data-ttu-id="227b7-117">Aktualisieren Sie die Seite, und die APP sollte jetzt eine Tabelle mit Ereignissen rendern.</span><span class="sxs-lookup"><span data-stu-id="227b7-117">Refresh the page and the app should now render a table of events.</span></span>

    ![Ein Screenshot der Tabelle mit Ereignissen](./images/add-msgraph-01.png)
