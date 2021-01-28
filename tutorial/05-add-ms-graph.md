<!-- markdownlint-disable MD002 MD041 -->

In dieser Übung integrieren Sie Microsoft Graph in die Anwendung. Für diese Anwendung verwenden Sie die [Microsoft-Graph-Bibliothek,](https://github.com/microsoftgraph/msgraph-sdk-php) um Aufrufe an Microsoft Graph zu senden.

## <a name="get-calendar-events-from-outlook"></a>Abrufen von Kalenderereignissen von Outlook

1. Erstellen Sie ein neues Verzeichnis im **Verzeichnis ./app** mit dem Namen, erstellen Sie dann eine neue Datei in diesem Verzeichnis namens , und fügen Sie `TimeZones` den folgenden Code `TimeZones.php` hinzu.

    :::code language="php" source="../demo/graph-tutorial/app/TimeZones/TimeZones.php":::

    Diese Klasse implementiert eine vereinfachte Zuordnung von Windows-Zeitzonennamen zu IANA-Zeitzonenbezeichnern.

1. Erstellen Sie eine neue Datei im **Verzeichnis ./app/Http/Controllers** mit dem Namen `CalendarController.php` , und fügen Sie den folgenden Code hinzu.

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

        $viewData['dateRange'] = $startOfWeek->format('M j, Y').' - '.$endOfWeek->format('M j, Y');

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
    - Die `startDateTime` Parameter und Parameter definieren den Anfang und das Ende der `endDateTime` Ansicht.
    - Der Parameter beschränkt die für jedes Ereignis zurückgegebenen Felder auf die Felder, die `$select` tatsächlich von der Ansicht verwendet werden.
    - Der Parameter sortiert die Ergebnisse nach dem Datum und der Uhrzeit, zu der sie erstellt wurden, dabei ist das letzte `$orderby` Element das erste Element.
    - Der `$top` Parameter beschränkt die Ergebnisse auf 25 Ereignisse.
    - Die Kopfzeile bewirkt, dass die Start- und Endzeiten in der Antwort an die bevorzugte Zeitzone des Benutzers `Prefer: outlook.timezone=""` angepasst werden.

1. Aktualisieren Sie die Routen in **./routes/web.php,** um diesem neuen Controller eine Route hinzuzufügen.

    ```php
    Route::get('/calendar', 'CalendarController@calendar');
    ```

1. Melden Sie sich an, und klicken **Sie** in der Navigationsleiste auf den Kalenderlink. Wenn alles funktioniert, sollte ein JSON-Abbild von Ereignissen im Kalender des Benutzers angezeigt werden.

## <a name="display-the-results"></a>Anzeigen der Ergebnisse

Jetzt können Sie eine Ansicht hinzufügen, um die Ergebnisse benutzerfreundlicher anzuzeigen.

1. Erstellen Sie eine neue Datei im Verzeichnis **./resources/views** mit dem `calendar.blade.php` Namen, und fügen Sie den folgenden Code hinzu.

    :::code language="php" source="../demo/graph-tutorial/resources/views/calendar.blade.php" id="CalendarSnippet":::

    Dadurch wird eine Ereignissammlung durchlaufen und jedem Ereignis wird jeweils eine Tabellenzeile hinzugefügt.

1. Aktualisieren Sie die Routen in **./routes/web.php,** um Routen für `/calendar/new` hinzuzufügen. Sie werden diese Funktionen im nächsten Abschnitt implementieren, aber die Route muss jetzt definiert werden, da **calendar.blade.php** darauf verweist.

    ```php
    Route::get('/calendar/new', 'CalendarController@getNewEventForm');
    Route::post('/calendar/new', 'CalendarController@createNewEvent');
    ```

1. Entfernen Sie `return response()->json($events);` die Zeile aus der Aktion in `calendar` **./app/Http/Controllers/CalendarController.php,** und ersetzen Sie sie durch den folgenden Code.

    ```php
    $viewData['events'] = $events;
    return view('calendar', $viewData);
    ```

1. Aktualisieren Sie die Seite, und die App sollte nun eine Tabelle mit Ereignissen rendern.

    ![Ein Screenshot der Tabelle mit Ereignissen](./images/add-msgraph-01.png)
