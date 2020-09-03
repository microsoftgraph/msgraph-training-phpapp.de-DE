# <a name="contributing-to-microsoft-graph-training-repositories"></a><span data-ttu-id="55b31-101">Mitwirken an Microsoft Graph-Schulungs-Repositories</span><span class="sxs-lookup"><span data-stu-id="55b31-101">Contributing to Microsoft Graph training repositories</span></span>

<span data-ttu-id="55b31-102">Vielen Dank, dass Sie zu diesem Projekt beigetragen haben!</span><span class="sxs-lookup"><span data-stu-id="55b31-102">Thank you for contributing to this project!</span></span> <span data-ttu-id="55b31-103">Beachten Sie Folgendes, bevor Sie Ihre Pull-Anforderung übermitteln.</span><span class="sxs-lookup"><span data-stu-id="55b31-103">Before submitting your pull request, be sure to consider the following.</span></span>

## <a name="overview"></a><span data-ttu-id="55b31-104">Übersicht</span><span class="sxs-lookup"><span data-stu-id="55b31-104">Overview</span></span>

<span data-ttu-id="55b31-105">Der Code in diesem Repository dient drei Zwecken:</span><span class="sxs-lookup"><span data-stu-id="55b31-105">The code in this repository serves three purposes:</span></span>

- <span data-ttu-id="55b31-106">Die Abschlag Dateien im [Lernprogramm](/tutorial) Ordner werden als Lernprogramm auf der Seite [Microsoft Graph Tutorials](https://docs.microsoft.com/graph/tutorials) veröffentlicht.</span><span class="sxs-lookup"><span data-stu-id="55b31-106">The Markdown files in the [tutorial](/tutorial) folder are published as a tutorial on the [Microsoft Graph tutorials](https://docs.microsoft.com/graph/tutorials) page.</span></span>
- <span data-ttu-id="55b31-107">Das Beispielprojekt im [Demo](/demo) Ordner ist die Quelle für einen [Microsoft Graph-Schnellstart](https://developer.microsoft.com/graph/quick-start).**\***</span><span class="sxs-lookup"><span data-stu-id="55b31-107">The sample project in the [demo](/demo) folder is the source for a [Microsoft Graph quick start](https://developer.microsoft.com/graph/quick-start).**\***</span></span>
- <span data-ttu-id="55b31-108">Das Beispielprojekt im Demo-Ordner ist auch direkt von GitHub herunterladbar und sollte nach einer einfachen Konfiguration wie folgt ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="55b31-108">The sample project in the demo folder is also downloadable directly from GitHub and should run as-is after some simple configuration.</span></span>

> <span data-ttu-id="55b31-109">**\*** Nicht alle Schulungs-Repositories stehen als Schnellstarts (noch) zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="55b31-109">**\*** Not all training repositories are available as quick starts (yet).</span></span>

<span data-ttu-id="55b31-110">Dies sollte beachtet werden, da Änderungen an einer Stelle *möglicherweise* Änderungen an einem anderen Ort erfordern, damit die Dinge synchron bleiben. Wo möglich, bezieht sich die Abschreibungs Dateien direkt auf die Quellcodedateien (mithilfe einer benutzerdefinierten `:::code` Syntax), sodass durch die Aktualisierung von Code in Source automatisch der Code im Abschlag aktualisiert wird.</span><span class="sxs-lookup"><span data-stu-id="55b31-110">This is important to keep in mind, because changes in one place *may* require changes in another, to keep things in sync. Whereever possible, the Markdown files refer to the source code files directly (using a custom `:::code` syntax), so that updating code in source will automatically update the code in Markdown.</span></span>

## <a name="updating-code"></a><span data-ttu-id="55b31-111">Aktualisieren von Code</span><span class="sxs-lookup"><span data-stu-id="55b31-111">Updating code</span></span>

<span data-ttu-id="55b31-112">Die `:::code` beim Abschlag verwendete Syntax hängt von bestimmten Kommentaren in der Quellcodedatei ab.</span><span class="sxs-lookup"><span data-stu-id="55b31-112">The `:::code` syntax used in Markdown depends on specific comments in the source code file.</span></span> <span data-ttu-id="55b31-113">Diese Kommentare sehen wie folgt aus:</span><span class="sxs-lookup"><span data-stu-id="55b31-113">These comments look like the following:</span></span>

```csharp
// <MySnippet>
Console.WriteLine("Hello World!");
// </MySnippet>
```

<span data-ttu-id="55b31-114">Wenn Sie Code zwischen diesen "Marker"-Kommentaren aktualisieren, erhalten die Abschlag Dateien automatisch diese Änderungen, wenn Sie auf der Microsoft Graph-Dokumentations Website veröffentlicht werden.</span><span class="sxs-lookup"><span data-stu-id="55b31-114">If you update code between these "marker" comments, the Markdown files will automatically get those changes when published to the Microsoft Graph documentation site.</span></span> <span data-ttu-id="55b31-115">Wenn Sie Code außerhalb dieser Kommentare aktualisieren, ist es sehr wahrscheinlich, dass Sie den entsprechenden Abschlag aktualisieren müssen.</span><span class="sxs-lookup"><span data-stu-id="55b31-115">If you update code outside of those comments, it's very likely that you'll need to update the corresponding Markdown.</span></span>

## <a name="adding-features"></a><span data-ttu-id="55b31-116">Hinzufügen von Features</span><span class="sxs-lookup"><span data-stu-id="55b31-116">Adding features</span></span>

<span data-ttu-id="55b31-117">Während die Begeisterung geschätzt wird, senden Sie bitte keine Pull-Anforderungen, um dem Beispiel neue Features hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="55b31-117">While the enthusiasm is appreciated, please don't send pull requests to add new features to the sample.</span></span> <span data-ttu-id="55b31-118">Da es sich bei diesem Repository hauptsächlich um ein Lernprogramm zum Erstellen Ihrer ersten App handelt, ist die Featuregruppe durch Entwurf beschränkt.</span><span class="sxs-lookup"><span data-stu-id="55b31-118">Because this repository is primarily a "build your first app" tutorial, the feature set is limited, by design.</span></span>

## <a name="submitting-pull-requests"></a><span data-ttu-id="55b31-119">Senden von Pull-Anforderungen</span><span class="sxs-lookup"><span data-stu-id="55b31-119">Submitting pull requests</span></span>

<span data-ttu-id="55b31-120">Senden Sie alle Pull-Anforderungen an die `master` Verzweigung.</span><span class="sxs-lookup"><span data-stu-id="55b31-120">Please submit all pull requests to the `master` branch.</span></span>

## <a name="when-do-changes-get-published"></a><span data-ttu-id="55b31-121">Wann werden Änderungen veröffentlicht?</span><span class="sxs-lookup"><span data-stu-id="55b31-121">When do changes get published?</span></span>

<span data-ttu-id="55b31-122">Die Veröffentlichung von Updates auf der [Microsoft Graph-Lern](https://docs.microsoft.com/graph/tutorials) Programmwebsite ist nicht automatisch.</span><span class="sxs-lookup"><span data-stu-id="55b31-122">Publishing of updates to the [Microsoft Graph tutorials](https://docs.microsoft.com/graph/tutorials) site is not automatic.</span></span> <span data-ttu-id="55b31-123">Änderungen müssen zuerst in die Verzweigung heraufgestuft werden `live` , dann muss ein Build von den Websiteadministratoren ausgelöst werden.</span><span class="sxs-lookup"><span data-stu-id="55b31-123">Changes must first be promoted to the `live` branch, then a build must be triggered by the site admins.</span></span> <span data-ttu-id="55b31-124">Dies erfolgt in der Regel auf der Grundlage der erforderlichen Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="55b31-124">This is typically done on an "as-needed" basis.</span></span>

## <a name="code-of-conduct"></a><span data-ttu-id="55b31-125">Verhaltensregeln</span><span class="sxs-lookup"><span data-stu-id="55b31-125">Code of conduct</span></span>

<span data-ttu-id="55b31-p106">In diesem Projekt wurden die [Microsoft Open Source-Verhaltensregeln](https://opensource.microsoft.com/codeofconduct/) übernommen. Weitere Informationen finden Sie unter [Häufig gestellte Fragen zu Verhaltensregeln](https://opensource.microsoft.com/codeofconduct/faq/), oder richten Sie Ihre Fragen oder Kommentare an [opencode@microsoft.com](mailto:opencode@microsoft.com).</span><span class="sxs-lookup"><span data-stu-id="55b31-p106">This project has adopted the [Microsoft Open Source Code of Conduct](https://opensource.microsoft.com/codeofconduct/). For more information see the [Code of Conduct FAQ](https://opensource.microsoft.com/codeofconduct/faq/) or contact [opencode@microsoft.com](mailto:opencode@microsoft.com) with any additional questions or comments.</span></span>
