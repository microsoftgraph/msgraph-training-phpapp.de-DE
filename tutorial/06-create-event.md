<!-- markdownlint-disable MD002 MD041 -->

In diesem Abschnitt fügen Sie die Möglichkeit zum Erstellen von Ereignissen im Kalender des Benutzers hinzu.

## <a name="create-new-event-form"></a>Erstellen eines neuen Ereignisformulars

1. Erstellen Sie eine neue Datei im Verzeichnis **./resources/views** mit dem `newevent.blade.php` Namen, und fügen Sie den folgenden Code hinzu.

    :::code language="php" source="../demo/graph-tutorial/resources/views/newevent.blade.php" id="NewEventFormSnippet":::

## <a name="add-controller-actions"></a>Hinzufügen von Controlleraktionen

1. Öffnen **Sie ./app/Http/Controllers/CalendarController.php,** und fügen Sie die folgende Funktion zum Rendern des Formulars hinzu.

    :::code language="php" source="../demo/graph-tutorial/app/Http/Controllers/CalendarController.php" id="getNewEventFormSnippet":::

1. Fügen Sie die folgende Funktion hinzu, um die Formulardaten beim Absenden des Benutzers zu empfangen, und erstellen Sie ein neues Ereignis im Kalender des Benutzers.

    :::code language="php" source="../demo/graph-tutorial/app/Http/Controllers/CalendarController.php" id="createNewEventSnippet":::

    Überlegen Sie, was dieser Code macht.

    - Die Eingabe des Teilnehmerfelds wird in ein Array von [Graph-Teilnehmerobjekten](https://docs.microsoft.com/graph/api/resources/attendee?view=graph-rest-1.0) konvertiert.
    - Es erstellt ein [Ereignis aus](https://docs.microsoft.com/graph/api/resources/event?view=graph-rest-1.0) der Formulareingabe.
    - Es sendet einen POST an den Endpunkt und leitet `/me/events` dann zurück zur Kalenderansicht.

1. Speichern Sie alle Änderungen, und starten Sie den Server neu. Verwenden Sie die **Schaltfläche "Neues Ereignis",** um zum neuen Ereignisformular zu navigieren.

1. Füllen Sie die Werte im Formular aus. Verwenden Sie ein Startdatum aus der aktuellen Woche. Wählen Sie **Erstellen** aus.

    ![Screenshot des neuen Ereignisformulars](images/create-event-01.png)

1. Wenn die App zur Kalenderansicht umleitiert, stellen Sie sicher, dass das neue Ereignis in den Ergebnissen vorhanden ist.
