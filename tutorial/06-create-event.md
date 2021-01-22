<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="d24e0-101">In diesem Abschnitt fügen Sie die Möglichkeit zum Erstellen von Ereignissen im Kalender des Benutzers hinzu.</span><span class="sxs-lookup"><span data-stu-id="d24e0-101">In this section you will add the ability to create events on the user's calendar.</span></span>

## <a name="create-new-event-form"></a><span data-ttu-id="d24e0-102">Erstellen eines neuen Ereignisformulars</span><span class="sxs-lookup"><span data-stu-id="d24e0-102">Create new event form</span></span>

1. <span data-ttu-id="d24e0-103">Erstellen Sie eine neue Datei im Verzeichnis **./resources/views** mit dem `newevent.blade.php` Namen, und fügen Sie den folgenden Code hinzu.</span><span class="sxs-lookup"><span data-stu-id="d24e0-103">Create a new file in the **./resources/views** directory named `newevent.blade.php` and add the following code.</span></span>

    :::code language="php" source="../demo/graph-tutorial/resources/views/newevent.blade.php" id="NewEventFormSnippet":::

## <a name="add-controller-actions"></a><span data-ttu-id="d24e0-104">Hinzufügen von Controlleraktionen</span><span class="sxs-lookup"><span data-stu-id="d24e0-104">Add controller actions</span></span>

1. <span data-ttu-id="d24e0-105">Öffnen **Sie ./app/Http/Controllers/CalendarController.php,** und fügen Sie die folgende Funktion zum Rendern des Formulars hinzu.</span><span class="sxs-lookup"><span data-stu-id="d24e0-105">Open **./app/Http/Controllers/CalendarController.php** and add the following function to render the form.</span></span>

    :::code language="php" source="../demo/graph-tutorial/app/Http/Controllers/CalendarController.php" id="getNewEventFormSnippet":::

1. <span data-ttu-id="d24e0-106">Fügen Sie die folgende Funktion hinzu, um die Formulardaten beim Absenden des Benutzers zu empfangen, und erstellen Sie ein neues Ereignis im Kalender des Benutzers.</span><span class="sxs-lookup"><span data-stu-id="d24e0-106">Add the following function to receive the form data when the user's submits, and create a new event on the user's calendar.</span></span>

    :::code language="php" source="../demo/graph-tutorial/app/Http/Controllers/CalendarController.php" id="createNewEventSnippet":::

    <span data-ttu-id="d24e0-107">Überlegen Sie, was dieser Code macht.</span><span class="sxs-lookup"><span data-stu-id="d24e0-107">Consider what this code does.</span></span>

    - <span data-ttu-id="d24e0-108">Die Eingabe des Teilnehmerfelds wird in ein Array von [Graph-Teilnehmerobjekten](https://docs.microsoft.com/graph/api/resources/attendee?view=graph-rest-1.0) konvertiert.</span><span class="sxs-lookup"><span data-stu-id="d24e0-108">It converts the attendees field input to an array of Graph [attendee](https://docs.microsoft.com/graph/api/resources/attendee?view=graph-rest-1.0) objects.</span></span>
    - <span data-ttu-id="d24e0-109">Es erstellt ein [Ereignis aus](https://docs.microsoft.com/graph/api/resources/event?view=graph-rest-1.0) der Formulareingabe.</span><span class="sxs-lookup"><span data-stu-id="d24e0-109">It builds an [event](https://docs.microsoft.com/graph/api/resources/event?view=graph-rest-1.0) from the form input.</span></span>
    - <span data-ttu-id="d24e0-110">Es sendet einen POST an den Endpunkt und leitet `/me/events` dann zurück zur Kalenderansicht.</span><span class="sxs-lookup"><span data-stu-id="d24e0-110">It sends a POST to the `/me/events` endpoint, then redirects back to the calendar view.</span></span>

1. <span data-ttu-id="d24e0-111">Speichern Sie alle Änderungen, und starten Sie den Server neu.</span><span class="sxs-lookup"><span data-stu-id="d24e0-111">Save all of your changes and restart the server.</span></span> <span data-ttu-id="d24e0-112">Verwenden Sie die **Schaltfläche "Neues Ereignis",** um zum neuen Ereignisformular zu navigieren.</span><span class="sxs-lookup"><span data-stu-id="d24e0-112">Use the **New event** button to navigate to the new event form.</span></span>

1. <span data-ttu-id="d24e0-113">Füllen Sie die Werte im Formular aus.</span><span class="sxs-lookup"><span data-stu-id="d24e0-113">Fill in the values on the form.</span></span> <span data-ttu-id="d24e0-114">Verwenden Sie ein Startdatum aus der aktuellen Woche.</span><span class="sxs-lookup"><span data-stu-id="d24e0-114">Use a start date from the current week.</span></span> <span data-ttu-id="d24e0-115">Wählen Sie **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="d24e0-115">Select **Create**.</span></span>

    ![Screenshot des neuen Ereignisformulars](images/create-event-01.png)

1. <span data-ttu-id="d24e0-117">Wenn die App zur Kalenderansicht umleitiert, stellen Sie sicher, dass das neue Ereignis in den Ergebnissen vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="d24e0-117">When the app redirects to the calendar view, verify that your new event is present in the results.</span></span>
