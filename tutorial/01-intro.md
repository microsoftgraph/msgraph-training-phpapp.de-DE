<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="b4a0b-101">In diesem Lernprogramm erfahren Sie, wie Sie eine PHP-Web-App erstellen, die die Microsoft Graph-API verwendet, um Kalenderinformationen für einen Benutzer abzurufen.</span><span class="sxs-lookup"><span data-stu-id="b4a0b-101">This tutorial teaches you how to build a PHP web app that uses the Microsoft Graph API to retrieve calendar information for a user.</span></span>

> [!TIP]
> <span data-ttu-id="b4a0b-102">Wenn Sie das fertige Lernprogramm einfach herunterladen möchten, können Sie es auf zwei Arten herunterladen.</span><span class="sxs-lookup"><span data-stu-id="b4a0b-102">If you prefer to just download the completed tutorial, you can download it in two ways.</span></span>
>
> - <span data-ttu-id="b4a0b-103">Laden Sie den [php-Schnellstart](https://developer.microsoft.com/graph/quick-start?platform=option-php) herunter, um in wenigen Minuten Arbeitscode zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="b4a0b-103">Download the [PHP quick start](https://developer.microsoft.com/graph/quick-start?platform=option-php) to get working code in minutes.</span></span>
> - <span data-ttu-id="b4a0b-104">Laden Sie das [GitHub-Repository](https://github.com/microsoftgraph/msgraph-training-phpapp)herunter, oder Klonen Sie es.</span><span class="sxs-lookup"><span data-stu-id="b4a0b-104">Download or clone the [GitHub repository](https://github.com/microsoftgraph/msgraph-training-phpapp).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="b4a0b-105">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="b4a0b-105">Prerequisites</span></span>

<span data-ttu-id="b4a0b-106">Bevor Sie mit diesem Lernprogramm beginnen, sollten Sie [php](http://php.net/downloads.php), [Composer](https://getcomposer.org/)und [Laravel](https://laravel.com/) auf Ihrem Entwicklungscomputer installiert haben.</span><span class="sxs-lookup"><span data-stu-id="b4a0b-106">Before you start this tutorial, you should have [PHP](http://php.net/downloads.php), [Composer](https://getcomposer.org/), and [Laravel](https://laravel.com/) installed on your development machine.</span></span>

> [!NOTE]
> <span data-ttu-id="b4a0b-107">Dieses Tutorial wurde mit PHP Version 7,2 geschrieben.</span><span class="sxs-lookup"><span data-stu-id="b4a0b-107">This tutorial was written with PHP version 7.2.</span></span> <span data-ttu-id="b4a0b-108">Die Schritte in diesem Leitfaden funktionieren möglicherweise mit anderen Versionen, jedoch nicht getestet.</span><span class="sxs-lookup"><span data-stu-id="b4a0b-108">The steps in this guide may work with other versions, but that has not been tested.</span></span>

## <a name="feedback"></a><span data-ttu-id="b4a0b-109">Feedback</span><span class="sxs-lookup"><span data-stu-id="b4a0b-109">Feedback</span></span>

<span data-ttu-id="b4a0b-110">Geben Sie Feedback zu diesem Lernprogramm im [GitHub-Repository](https://github.com/microsoftgraph/msgraph-training-phpapp)an.</span><span class="sxs-lookup"><span data-stu-id="b4a0b-110">Please provide any feedback on this tutorial in the [GitHub repository](https://github.com/microsoftgraph/msgraph-training-phpapp).</span></span>
