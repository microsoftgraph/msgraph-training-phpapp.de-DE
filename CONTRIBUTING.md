# <a name="contributing-to-microsoft-graph-training-repositories"></a>Mitwirken an Microsoft Graph-Schulungs-Repositories

Vielen Dank, dass Sie zu diesem Projekt beigetragen haben! Beachten Sie Folgendes, bevor Sie Ihre Pull-Anforderung übermitteln.

## <a name="overview"></a>Übersicht

Der Code in diesem Repository dient drei Zwecken:

- Die Abschlag Dateien im [Lernprogramm](/tutorial) Ordner werden als Lernprogramm auf der Seite [Microsoft Graph Tutorials](https://docs.microsoft.com/graph/tutorials) veröffentlicht.
- Das Beispielprojekt im [Demo](/demo) Ordner ist die Quelle für einen [Microsoft Graph-Schnellstart](https://developer.microsoft.com/graph/quick-start).**\***
- Das Beispielprojekt im Demo-Ordner ist auch direkt von GitHub herunterladbar und sollte nach einer einfachen Konfiguration wie folgt ausgeführt werden.

> **\*** Nicht alle Schulungs-Repositories stehen als Schnellstarts (noch) zur Verfügung.

Dies sollte beachtet werden, da Änderungen an einer Stelle *möglicherweise* Änderungen an einem anderen Ort erfordern, damit die Dinge synchron bleiben. Wo möglich, bezieht sich die Abschreibungs Dateien direkt auf die Quellcodedateien (mithilfe einer benutzerdefinierten `:::code` Syntax), sodass durch die Aktualisierung von Code in Source automatisch der Code im Abschlag aktualisiert wird.

## <a name="updating-code"></a>Aktualisieren von Code

Die `:::code` beim Abschlag verwendete Syntax hängt von bestimmten Kommentaren in der Quellcodedatei ab. Diese Kommentare sehen wie folgt aus:

```csharp
// <MySnippet>
Console.WriteLine("Hello World!");
// </MySnippet>
```

Wenn Sie Code zwischen diesen "Marker"-Kommentaren aktualisieren, erhalten die Abschlag Dateien automatisch diese Änderungen, wenn Sie auf der Microsoft Graph-Dokumentations Website veröffentlicht werden. Wenn Sie Code außerhalb dieser Kommentare aktualisieren, ist es sehr wahrscheinlich, dass Sie den entsprechenden Abschlag aktualisieren müssen.

## <a name="adding-features"></a>Hinzufügen von Features

Während die Begeisterung geschätzt wird, senden Sie bitte keine Pull-Anforderungen, um dem Beispiel neue Features hinzuzufügen. Da es sich bei diesem Repository hauptsächlich um ein Lernprogramm zum Erstellen Ihrer ersten App handelt, ist die Featuregruppe durch Entwurf beschränkt.

## <a name="submitting-pull-requests"></a>Senden von Pull-Anforderungen

Senden Sie alle Pull-Anforderungen an die `master` Verzweigung.

## <a name="when-do-changes-get-published"></a>Wann werden Änderungen veröffentlicht?

Die Veröffentlichung von Updates auf der [Microsoft Graph-Lern](https://docs.microsoft.com/graph/tutorials) Programmwebsite ist nicht automatisch. Änderungen müssen zuerst in die Verzweigung heraufgestuft werden `live` , dann muss ein Build von den Websiteadministratoren ausgelöst werden. Dies erfolgt in der Regel auf der Grundlage der erforderlichen Anforderungen.

## <a name="code-of-conduct"></a>Verhaltensregeln

In diesem Projekt wurden die [Microsoft Open Source-Verhaltensregeln](https://opensource.microsoft.com/codeofconduct/) übernommen. Weitere Informationen finden Sie unter [Häufig gestellte Fragen zu Verhaltensregeln](https://opensource.microsoft.com/codeofconduct/faq/), oder richten Sie Ihre Fragen oder Kommentare an [opencode@microsoft.com](mailto:opencode@microsoft.com).
