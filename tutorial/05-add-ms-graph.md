<!-- markdownlint-disable MD002 MD041 -->

In dieser Übung werden Sie das Microsoft Graph in die Anwendung integrieren. Für diese Anwendung verwenden Sie die [Microsoft-Graph-](https://github.com/microsoftgraph/msgraph-sdk-php) Bibliothek, um Anrufe an Microsoft Graph zu tätigen.

## <a name="get-calendar-events-from-outlook"></a>Abrufen von Kalenderereignissen von Outlook

1. Erstellen Sie ein neues Verzeichnis im **./app** -Verzeichnis mit dem Namen `TimeZones` , erstellen Sie dann eine neue Datei in dem Verzeichnis mit dem Namen `TimeZones.php` , und fügen Sie den folgenden Code hinzu.

    :::code language="php" source="../demo/graph-tutorial/app/TimeZones/TimeZones.php":::

    Diese Klasse implementiert eine vereinfachte Zuordnung von Windows-Zeitzonennamen zu IANA-Zeitzonenbezeichnern.

1. Erstellen Sie eine neue Datei im **/App/http/Controllers** -Verzeichnis mit dem Namen `CalendarController.php` , und fügen Sie den folgenden Code hinzu.

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

    Überlegen Sie sich, was dieser Code macht.

    - Die URL, die aufgerufen wird, lautet `/v1.0/me/calendarView`.
    - Die `startDateTime` `endDateTime` Parameter und definieren den Anfang und das Ende der Ansicht.
    - Der `$select` Parameter schränkt die für die einzelnen Ereignisse zurückgegebenen Felder auf diejenigen ein, die von der Ansicht tatsächlich verwendet werden.
    - Der `$orderby` Parameter sortiert die Ergebnisse nach dem Datum und der Uhrzeit, zu der Sie erstellt wurden, wobei das letzte Element zuerst angezeigt wird.
    - Der `$top` Parameter schränkt die Ergebnisse auf 25 Ereignisse ein.
    - Der `Prefer: outlook.timezone=""` Header bewirkt, dass die Anfangs-und Endzeiten in der Antwort an die bevorzugte Zeitzone des Benutzers angepasst werden.

1. Aktualisieren Sie die Routen in **./routes/Web.php** , um dieser neuen Steuerung eine Route hinzuzufügen.

    ```php
    Route::get('/calendar', 'CalendarController@calendar');
    ```

1. Melden Sie sich an, und klicken Sie in der Navigationsleiste auf den Link **Kalender** . Wenn alles funktioniert, sollte ein JSON-Abbild von Ereignissen im Kalender des Benutzers angezeigt werden.

## <a name="display-the-results"></a>Anzeigen der Ergebnisse

Jetzt können Sie eine Ansicht hinzufügen, um die Ergebnisse benutzerfreundlicher anzuzeigen.

1. Erstellen Sie eine neue Datei im **./Resources/views** -Verzeichnis mit dem Namen `calendar.blade.php` , und fügen Sie den folgenden Code hinzu.

    :::code language="php" source="../demo/graph-tutorial/resources/views/calendar.blade.php" id="CalendarSnippet":::

    Dadurch wird eine Ereignissammlung durchlaufen und jedem Ereignis wird jeweils eine Tabellenzeile hinzugefügt.

1. Entfernen Sie die- `return response()->json($events);` `calendar` Aktion in **./app/http/Controllers/CalendarController.php**, und ersetzen Sie Sie durch den folgenden Code.

    ```php
    $viewData['events'] = $events;
    return view('calendar', $viewData);
    ```

1. Aktualisieren Sie die Seite, und die APP sollte jetzt eine Tabelle mit Ereignissen rendern.

    ![Ein Screenshot der Tabelle mit Ereignissen](./images/add-msgraph-01.png)
